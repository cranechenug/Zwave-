# Zwave-
关于如何修改ZWAVE DEMO中的参数
一、如何修改IO口配置
1.io_zdp03a.c
***********************************************
  /**
 * Map zdp03a button to ZW050 pin.
 */
BYTE buttonMap[] = {0x11, 0x24, 0x36, 0x23, 0x22, 0x21, 0x34};

/**
 * Map zdp03a LED to ZW050 pin.
 * 07 > port 0 pin 7
 * 37 > port 3 pin 7
 */
BYTE ledMap[] = {0x7, 0x37, 0x10, 0x12, 0x14, 0x15, 0x16, 0x17};
***********************************************
1.1此段代码中，buttonMap数组中：
  0x11为外部中断接口，禁止更改。
  0x24为S0按键；0x36位S1按键；0x23为S2按键；0x22为S3按键；0x21为S4按键；0x34为S5按键；
 S0-S5这些按键对应的IO口，可以按需要进行更改。 
 1.2此段代码中，ledMap数组中：
  0x7为LED1指示灯；0x37为LED2指示灯；0x10为LED3指示灯；0x12为LED3指示灯；0x14为LED3指示灯；0x15为LED4指示灯；0x16为LED5指示灯；0x17为LED6指示灯
 LED1-LED6这些LED对应的IO口，可以按需要进行更改。
2.SwitchOnOff.c
*********************************************************
 BYTE                       /* RET TRUE        */
ApplicationInitHW(
  BYTE bWakeupReason)      /* IN  Nothing     */
{
#ifdef ZW_ISD51_DEBUG
  ISD_UART_init();
#endif
  wakeupReason = bWakeupReason;
  /* hardware initialization */
  ValidNVR = InitZDP03A();
  SetPinIn(S1,1); //PIN_IN(P24, 1); /*s1 ZDP03A*/
  SetPinIn(S2,1); //PIN_IN(P36, 1); /*s2 ZDP03A*/

  SetPinOut(LED1_OUT); //PIN_OUT(LED1)
  Led(LED1_OUT,OFF); //LED_OFF(1);
  SetPinOut(LED2_OUT); //PIN_OUT(LED1)
  Led(LED2_OUT,OFF); //LED_OFF(1);
  Transport_OnApplicationInitHW(bWakeupReason);
  return(TRUE);
}
*********************************************************
ApplicationInitHW()中，主要初始化硬件状态。
SetPinIn(S1,1)即将1中设置好的S1 IO口配置为输入、使能上拉。
SetPinIn(S2,0)即将1中设置好的S2 IO口配置为输入、禁止上拉。
SetPinOut(LED1_OUT)即将1中设置好的LED1 IO口配置为输出。
Led(LED1_OUT,OFF)即将LED1，输出低电平。
Led(LED2_OUT,ON)即将LED2，输出高电平。
以上IO口的初始化可以在这里面按需要进行配置。
3.
IO_MAP portMap_zm5202[] =
{
  {0x07,0x10}, /**< LED 1*/
  {0x37,0x37}, /**< LED 2*/
  {0x11,0x11}, /**< S0 */
  {0x24,0x24}, /**< S1 */
  {0x36,0x36}, /**< S2 */
  {0x23,0x23}, /**< S3 */
  {0x22,0x22}, /**< S4 */
};
此段代码涉及到IO口的转换，在CMD时，如果输入BOARD=ZM5202，那么，portMap_zm5202[]将生效
*********************NVR配置说明文档中的内容INS12366*********************************
3.4.2  BOARD parameter
This parameter specifies the target hardware platform when building the application.
Table 2. Description of BOARD parameters in command line
Parameter                Result
No parameters specified  Building for all hardware platforms.
BOARD=ZDP03A             Building for a ZDB3502 mounted on a ZDP03A
BOARD=ZM5101             Building for a ZDB5101 mounted on a ZDP03A
BOARD=ZM5202             Building for a ZDB5202 mounted on a ZDP03A

3.4.3  CHIP parameter
This parameter specifies the chip used. However, only the 500 Series chip is supported currently.
Table 3. Description of CHIP parameters in command line
Parameter                 Result
No parameters specified  Building application for 500 Series chip
CHIP=ZW050x              Building application for 500 Series chip
*********************NVR配置说明文档中的内容INS12366******************************************
SetPinIn中调用buttonMap数组
*************************
/*============================ PinIn ===============================
** Function description
** This function...
**
** Side effects:
**
**-------------------------------------------------------------------------*/
BOOL SetPinIn( S_BUTTONS button, BOOL pullUp)
{
  BYTE pin = buttonMap[button];

  if(0 != myIo.size)
  {
    if(FALSE == SeachPinMap(&pin, TRUE))
    {
      return FALSE;
    }
  }
  myIo.enButtons |= (1<<button);
  PinIn( pin, pullUp);
  return TRUE;
}
********************************************
SetPinOut中调用ledMap数组
*****************************
/*============================ PinOut ===============================
** Function description
** This function...
**
** Side effects:
**
**-------------------------------------------------------------------------*/
BOOL SetPinOut( LED_OUT led)
{
  BYTE pin = ledMap[led];

  ZW_DEBUG_IO_ZDP03A_SEND_STR("SetPinOut(");
  ZW_DEBUG_IO_ZDP03A_SEND_NUM(led);
  ZW_DEBUG_IO_ZDP03A_SEND_BYTE(')');
  ZW_DEBUG_IO_ZDP03A_SEND_NL();

  if(0 != myIo.size)
  {
    if(FALSE == SeachPinMap(&pin, TRUE) )
    {
      return FALSE;
    }
  }
  PinOut( pin);
  return TRUE;
}
***********************************

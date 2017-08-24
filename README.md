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
此段代码涉及到IO口的转换，还没有弄明白。待明白后再上传。

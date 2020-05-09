---
layout: post
title: Sử dụng 74HC595 mở rộng ngõ ra Tiva C
description: Code mẫu và hướng dẫn sử dụng IC 74HC595 để mở rộng ngõ ra cho Tiva C TM4C123GH6PM.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller]
comments: true
---

~~~
#include <stdint.h>
#include <stdbool.h>
#include "inc/hw_types.h"
#include "inc/hw_gpio.h"
#include "driverlib/pin_map.h"
#include "driverlib/sysctl.c"
#include "driverlib/sysctl.h"
#include "driverlib/gpio.c"
#include "driverlib/gpio.h"

#define HC_DS    GPIO_PIN_1
#define HC_CLK   GPIO_PIN_2
#define HC_Latch GPIO_PIN_3

uint8_t data;

void delayMS(int ms)
{
    SysCtlDelay( (SysCtlClockGet()/(3000))*ms ) ;
}

void HC595_Out(void)
{
    uint8_t i, temp;
    for(i = 0; i < 8; i++) // i < 8 nếu chỉ có 1 IC 595, nếu 2 thì i < 16, tương tự khi nối tiếp thêm nhiều 595
    {
        temp = data & (0x80>>i); //xet xem bit 0 hay 1
        if (temp == 0)
            GPIOPinWrite(GPIO_PORTF_BASE, HC_DS, 0x00);
        else
            GPIOPinWrite(GPIO_PORTF_BASE, HC_DS, 0xff);
        // tao xung dich du lieu
        GPIOPinWrite(GPIO_PORTF_BASE, HC_CLK, 0x00);
        delayMS(1);
        GPIOPinWrite(GPIO_PORTF_BASE, HC_CLK, 0xff);
    }
    // tao xung chot du lieu
    GPIOPinWrite(GPIO_PORTF_BASE, HC_Latch, 0x00);
    delayMS(1);
    GPIOPinWrite(GPIO_PORTF_BASE, HC_Latch, 0xff);
}
int main(void)
{
    SysCtlClockSet(SYSCTL_SYSDIV_2_5|SYSCTL_USE_PLL|SYSCTL_OSC_MAIN|SYSCTL_XTAL_16MHZ);
    SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOF);
    SysCtlDelay(3);
    GPIOPinTypeGPIOOutput(GPIO_PORTF_BASE, GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3);
    GPIOPinWrite(GPIO_PORTF_BASE, HC_DS, 0x00);
    GPIOPinWrite(GPIO_PORTF_BASE, HC_CLK|HC_Latch, 0xff);

  while (1)
  {
      data = 0x00;
      HC595_Out();
      delayMS(1000);
      data = 0xff;
      HC595_Out();
      delayMS(1000);
  }
}
~~~
Updating...
---
layout: post
title: Sử dụng cảm biến siêu âm đo khoảng cách với Tiva C
description: Code mẫu và hướng dẫn sử dụng cảm biến siêu âm US-015 đo khoảng cách với Tiva C TM4C123GH6PM.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller, US-015]
comments: true
---

<h2>**Cảm biến siêu âm đo khoảng cách US-015**</h2>

Cảm biến siêu âm Ultrasonic US-015 được sử dụng để nhận biết khoảng cách từ vật thể đến cảm biến nhờ sóng siêu âm, cảm biến có thời gian phản hồi nhanh, độ chính xác cao, phù hợp cho các ứng dụng phát hiện vật cản, đo khoảng cách bằng sóng siêu âm.

Cảm biến siêu âm Ultrasonic US-015 được đánh giá là cho độ ổn định và chính xác cao hơn so với hai phiên bản cảm biến siêu âm HY-SRF05 và HC-SR04 với cách sử dụng, thư viện và code mẫu tương thích 100%.

<h2>**Nguyên lý hoạt động của các cảm biến siêu âm đo khoảng cách US-015, SRF0, SRF05**</h2>

Sau khi cấp nguồn cho US-015, ta bắt đầu cấp một xung mức cao vào chân Trigger của US-015, sau đó module sẽ phát ra sóng siêu âm, sóng này lan truyền trong không khí, khi gặp vật cản, sóng phản hồi lại và được nhận biết bởi cảm biến trên US-015.
Khi nhận được sóng siêu âm phản hồi, US-015 sẽ tạo một xung mức cao trên chân Echo, ta sẽ dùng vi điều khiển đo thời gian tính từ lúc bắt đầu phát siêu âm tới khi nhận được phản hồi, dựa vào tốc độ âm thanh trong không khí để tính ra khoảng cách.
**Lưu ý:**
* Cần chia đôi kết quả vì tổng thời gian đo được là thời gian cả lượt đi và lượt về của sóng siêu âm.
* Do cảm biến hoạt động bằng cách thu lại sóng phản hồi nên đối với môi trường quá rộng, bề mặt cần đo hấp thụ hoặc tán xạ sóng siêu âm thì kết quả đo có thể không chính xác.

<h2>**Code mẫu US-015 với Tiva C dùng delay**</h2>
~~~
#include <stdint.h>
#include <stdbool.h>
#include "inc/hw_memmap.h"
#include "inc/hw_types.h"
#include "inc/hw_ints.h"
#include "driverlib/gpio.h"
#include "driverlib/pin_map.h"
#include "driverlib/sysctl.h"
#include "driverlib/uart.h"
#include "driverlib/interrupt.h"
#include "stdlib.h"
#include "inc/hw_ints.h"

#include "inc/hw_timer.h"
#include "inc/hw_uart.h"
#include "inc/hw_gpio.h"
#include "inc/hw_pwm.h"
#include "inc/hw_types.h"

#include "driverlib/timer.h"
#include "driverlib/gpio.h"
#include "driverlib/rom.h"
#include "driverlib/rom_map.h"
#include "driverlib/sysctl.h"
#include "driverlib/uart.h"
#include "driverlib/udma.h"
#include "driverlib/pwm.h"
#include "driverlib/ssi.h"
#include "driverlib/systick.h"
#include <string.h>
#include "utils/uartstdio.c"



void inputInt();

const double temp = 1.0/16.0;

//Stores the pulse length
volatile uint32_t pulse = 0;
volatile uint32_t time = 0;
volatile uint32_t dis = 0;

//Tells the main code if the a pulse is being read at the moment
volatile uint8_t echowait = 0;

int main()
{
    //Set system clock to 16Mhz
    SysCtlClockSet(SYSCTL_SYSDIV_1 | SYSCTL_USE_OSC |   SYSCTL_OSC_MAIN | SYSCTL_XTAL_16MHZ);
	// Cau hinh timer 2 de do thoi gian phan hoi cua sieu am
    SysCtlPeripheralEnable(SYSCTL_PERIPH_TIMER2);
    SysCtlDelay(3);
    TimerConfigure(TIMER2_BASE, TIMER_CFG_PERIODIC_UP);
    TimerEnable(TIMER2_BASE,TIMER_A);

    //Configure Trigger pin
    SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOD);
    SysCtlDelay(3);
    GPIOPinTypeGPIOOutput(GPIO_PORTD_BASE, GPIO_PIN_3);

    //Configure Echo pin
    SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOD);
    SysCtlDelay(3);
    GPIOPinTypeGPIOInput(GPIO_PORTD_BASE, GPIO_PIN_2);

    // Cau hinh ngat ca.nh len va ca.nh xuong tren PD2
    GPIOIntTypeSet(GPIO_PORTD_BASE, GPIO_PIN_2,GPIO_BOTH_EDGES);
    GPIOIntRegister(GPIO_PORTD_BASE,inputInt);
    GPIOIntEnable(GPIO_PORTD_BASE, GPIO_PIN_2);

    // Vong lap doc gia tri tu cam bien
    while(1)
      {
        if(echowait != 1)
        {
            //Gui xung 10uS vao chan Trig
            GPIOPinWrite(GPIO_PORTD_BASE, GPIO_PIN_3, GPIO_PIN_3);
            SysCtlDelay(60); // (16.000.000hz / 3.000.000)*10 = 54, lay tron 60
            GPIOPinWrite(GPIO_PORTD_BASE, GPIO_PIN_3, ~GPIO_PIN_3);

            // Cho cho den khi nhan duoc gia tri tu Pin Echo
            while(echowait != 0);

            // Chuyen tu so xung dem duoc => thoi gian
            time =(uint32_t)(temp * pulse);
            dis = time * 0.0172;
         }
        SysCtlDelay(SysCtlClockGet()/6);
      }
}
//----- Ket thuc chuong trinh chinh ------------------------------------------------
void inputInt(){
  //Clear interrupt flag.
  GPIOIntClear(GPIO_PORTD_BASE, GPIO_PIN_2);

  if (GPIOPinRead(GPIO_PORTD_BASE, GPIO_PIN_2) == GPIO_PIN_2)
  {
    HWREG(TIMER2_BASE + TIMER_O_TAV) = 0; //Loads value 0 into the timer.
    TimerEnable(TIMER2_BASE,TIMER_A);
    echowait = 1;
  }
  else
  {
    pulse = TimerValueGet(TIMER2_BASE,TIMER_A); //record value
    TimerDisable(TIMER2_BASE,TIMER_A);
    echowait = 0;
  }
}
~~~
Updating...
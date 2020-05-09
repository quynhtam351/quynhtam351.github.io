---
layout: post
title: Sử dụng chức năng SysTick tạo delay chớp tắt led trên Tiva C
subtitle: Tiva C Error connecting to target fix
description: Sử dụng chức năng SysTick tích hợp của Tiva C TM4C123GH6PM để tạo delay chớp tắt led.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller]
comments: true
---
**SysTick trên Tiva C là gì?**

Cortex-M4 được tích hợp một timer hệ thống tên là SysTick, nó cung cấp một bộ đếm lùi 24 bit đơn giản với có chế điều khiển mềm dẻo.
Bộ đếm có thể được sử dụng theo nhiều cách, ví dụ:
* Đồng hồ bấm giờ cho RTOS có thể lập trình được
* Bộ đếm báo động tốc độ cao dùng xung nhịp của hệ thống
* Một bộ đếm đơn giản đo thời gian hoàn thành hoặc thời gian đã sử dụng

**Hàm API SysTick trong thư viện TivaWare**

Đặt chu kì cho SysTick, giá trị Period nằm trong khoảng từ 1 đến 2^24
~~~
void SysTickPeriodSet(uint32_t ui32Period)
~~~
Ngoài ra còn có các hàm cần khai báo lần lượt là SysTickIntRegister, SysTickIntEnable, SysTickEnable.
Bên dưới là code mẫu chớp tắt LED: LED đỏ chu kì 1 giây, LED xanh dương chu kì 2 giây và LED xanh lá chu kì 6 giây.
~~~
#include <stdint.h>
#include <stdbool.h>
#include "inc/hw_types.h"
#include "inc/hw_memmap.h"
#include "driverlib/sysctl.h"
#include "driverlib/gpio.h"
#include "driverlib/systick.h"

volatile uint32_t time = 0;
uint32_t RedLedTime = 0, BlueLedTime = 0;

void SysTick_ISR()
{
    time++;
    if ((RedLedTime + 499) < time) //mất khoảng 1 ms để thực hiện các lệnh trong if, nên lấy 499 cho tròn số
     {
         GPIOPinWrite(GPIO_PORTF_BASE, GPIO_PIN_1, ~GPIOPinRead(GPIO_PORTF_BASE, GPIO_PIN_1));
		 // Đọc giá trị PF1 từ thanh ghi, đảo bit (~) sau đó ghi trở lại.
         RedLedTime = time;
     }
     if ((BlueLedTime + 999) < time)
      {
          GPIOPinWrite(GPIO_PORTF_BASE, GPIO_PIN_2, ~GPIOPinRead(GPIO_PORTF_BASE, GPIO_PIN_2));
          BlueLedTime = time;
      }
}

int main(void)
{
    // 80MHz
    SysCtlClockSet(SYSCTL_SYSDIV_2_5|SYSCTL_USE_PLL|SYSCTL_OSC_MAIN|SYSCTL_XTAL_16MHZ);

    SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOF);
    SysCtlDelay(10);

    GPIOPinTypeGPIOOutput(GPIO_PORTF_BASE, GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3);
	
    SysTickPeriodSet(SysCtlClockGet()/1000);
    SysTickIntRegister(&SysTick_ISR);
    SysTickIntEnable();
    SysTickEnable();

	while(1)
	 {
		 SysCtlDelay(SysCtlClockGet());
		 GPIOPinWrite(GPIO_PORTF_BASE, GPIO_PIN_3, ~GPIOPinRead(GPIO_PORTF_BASE, GPIO_PIN_3));
	 }
}
~~~
Ở đây mình lấy period bằng tần số clock chia cho 1000, tương ứng với 1ms mỗi lần ngắt SysTick xảy ra, biến time sẽ lưu thời gian (tính theo ms)
từ lúc code bắt đầu chạy. Biến time có độ lớn là 32bit, để tràn biến này, TM4C123GH6PM phải chạy khoảng 49 ngày.
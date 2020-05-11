---
layout: post
title: Sử dụng 74HC165 mở rộng ngõ VÀO Tiva C
description: Code mẫu và hướng dẫn sử dụng IC 74HC165 để mở rộng ngõ vào cho Tiva C TM4C123GH6PM.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller, 74HC165]
comments: true
---

<h2>Chức năng IC 74HC165</h2>

74HC165 là các IC dịch 8 bit dữ liệu song song sang nối tiếp khi được cấp xung clock.
Các IC 74HC165 còn có chức năng cấm đầu ra (ngừng đầu ra) và dịch bit nối tiếp đầu ra sang IC bổ sung.
Có thể hiểu đơn giản là 74HC595 dịch dữ liệu từ 8 chân input song song thành dữ liệu trên một chân output duy nhất, và còn có khả năng nối tiếp nhiều IC với nhau.
Điều này có nghĩa là với 4 chân của vi điều khiển, ta có thể mở rộng ngõ vào thành những con số rất lớn: 8, 16, 24, 32, ...

<h2>Thông số và cách sử dụng 74HC165</h2>

Điện áp hoạt động của 74HC165 khá rộng, từ 2 - 6V, thông thường là 5V.
![74HC165 Pin diagram](https://quynhtam351.github.io/img/74HC/74HC165-pin-diagram.jpg){: .center-block :}

Chức năng các pin như sau:

| Tên | Chức năng |
| :------ |:--- |
|A,B,C,D,E,F,G,H|Ngõ vào song song|
|CLK|Ngõ vào xung clock|
|CLK INH|Cấm clock. Khi ở mức cao, không có sự thay đổi ở ngõ ra|
|GND|Chân GND|
|QH|Ngõ ra nối tiếp|
|<span style="text-decoration: overline">QH</span>|Ngõ ra nối tiếp đảo ngược|
|SER|Ngõ vào nối tiếp, ứng dụng cho nối tiếp nhiều HC165|
|SH/<span style="text-decoration: overline">LD</span>|Dịch hoặc tải ngõ vào. Khi ở mức cao dữ liệu được dịch đi, khi ở mức thấp nạp dữ liệu từ ngõ vào song song.|
|VCC|Chân nguồn dương|

**Nguyên lý hoạt động:**
- Kéo chân LD xuống thấp trong khoảng 2ms sau đó đưa lại mức cao, lúc này trạng thái 8 ngõ vào được đưa vào thanh ghi của HC165.
- Kéo chân CLK INH xuống thấp để cho phép đầu ra.
- Đọc bit đầu tiên ở ngõ ra sau đó cấp xung vào chân CLK để lần lượt đọc các bit tiếp theo.
- Nếu nối tiếp nhiều HC165 thì ngõ ra của IC trước nối vào ngõ vào nối tiếp SER của IC sau, các chân CLK, CLK INH và SH/<span style="text-decoration: overline">LD</span> nối chung để cùng nhận tín hiệu điều khiển.

~~~
//------ 74HC165--------------
#define HC_DS    GPIO_PIN_1
#define HC_CLK   GPIO_PIN_2
#define HC_Snap  GPIO_PIN_3
#define HC_En    GPIO_PIN_4

void HC165_In(void)
{
    // tao xung get du lieu
    GPIOPinWrite(GPIO_PORTF_BASE, HC_Snap, 0x00);
    delayMS(2);
    GPIOPinWrite(GPIO_PORTF_BASE, HC_Snap, 0xff);
    //delayMS(2);
    GPIOPinWrite(GPIO_PORTF_BASE, HC_En, 0x00);

    uint8_t i;
    temp = 0;
    for(i = 0; i < 24; i++) //nối tiếp 3 IC 74HC165 => 24 bits
    {
        //temp = ((temp<<1)|(GPIOPinRead(GPIO_PORTF_BASE, HC_DS)>>1));
        if(GPIOPinRead(GPIO_PORTF_BASE, HC_DS) != 0)
        {
            temp = ((temp<<1)|0x01);
            //astatus[i] = ~astatus[i];
        }
        else
        {
            temp = (temp<<1);

        }
        GPIOPinWrite(GPIO_PORTF_BASE, HC_CLK, 0xff);
        //delayMS(1);
        GPIOPinWrite(GPIO_PORTF_BASE, HC_CLK, 0x00);

        //temp = ((temp<<i)|(GPIOPinRead(GPIO_PORTF_BASE, HC_DS)>>1));
    }
    data = temp;
    GPIOPinWrite(GPIO_PORTF_BASE, HC_En, 0xff);
}
~~~
Updating...
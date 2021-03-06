---
layout: post
title: Lưu ý khi sử dụng KIT Tiva C Launchpad TM4C123GH6PM
description: Tổng hợp các vấn đề thường gặp cần lưu ý khi sử dụng KIT phát triển Tiva C Launchpad. Hướng dẫn unlock pins, cấp nguồn cho Tiva C.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller]
comments: true
---

**1. Nếu cần hàn thêm header cho các chân còn trống:**

Dùng loại header đực, đơn, loại dài. Đẩy đế nhựa giữ header vào giữa thanh header.
Hàn sao cho khoảng ½ header nằm trên board, và ½ còn lại quay xuống dưới.
Vì ở vòng thi đấu robot, board M4 này được gắn lên 1 board mạch lớn, do đó cần các header quay xuống dưới để tiếp xúc được.
Phần header quay lên trên bạn có thể dùng để cắm dây bus như thông thường.

**Không được đặt board lên các bề mặt dẫn điện (vd bàn kim loại,…), không được để chì, dây diện, ốc vít và các vật dẫn điện khác ở dưới board.** Điều đó có thể làm chạm, chập và chết chip.
Như hình sau, các chân I/O được hàn bằng header dài, 1 nửa nằm trên và 1 nửa quay xuống dưới, nên board màu đỏ có thể gắn được lên board màu xanh (board xanh có hàng header cái).

![Tiva C launchpad](/img/luu-y-khi-dung-launchpad/Tiva-C-launchpad-1.jpg){: .center-block :}

Do mặt sau kit M4 có sẵn 2 hàng header cái, do vậy các bạn canh sao cho độ dài của header (đực) mình mới hàn thêm bằng với header cái có sẵn để cắm lên board không bị vênh và tiếp xúc tốt.
Đây là header dài (khoảng 1.5cm, còn có 1 loại dài hơn (khoảng 2cm) nhưng khó kiếm chỗ mua):

![Tiva C launchpad](/img/luu-y-khi-dung-launchpad/Tiva-C-launchpad-2.jpg){: .center-block :}

Port từ A – D có 8 pins, port E có 6 pins và port F chỉ có 5 pins.

* Port A: 01234567
* Port B: 01234567
* Port C: 4567
* Port D: 0123-67
* Port E: 012345
* Port F: 01234


**2. Cấp nguồn 5V từ mạch ngoài vào KIT Tiva C Launchpad**

Do ta không thể lúc nào cũng dùng nguồn USB để chip M4 hoạt động, do đó cần phải có biện pháp cấp nguồn ngoài cho MCU. Một số bạn tận dụng các cọc 3V3 hoặc +Vbus (5V) tại 2 hàng header mở rộng để cấp nguồn qua các chân này, tuy nhiên cách đó không phải là tốt nhất.
Vị trí cấp nguồn tốt nhất cho mạch, đó là vị trí cấp nguồn 5V được đánh dấu trên hình dưới đây (không dùng chức năng giao tiếp USB), với ưu điểm:
* Vẫn sử dụng được công tắc nguồn (gạt sang trái là lấy nguồn ngoài, gạt sang phải là lấy nguồn từ cổng USB của laptop, tránh được trường hợp xung đột nếu cắm dây vô nạp code mà quên cắt nguồn ngoài).
* Sử dụng được con regulator (ổn áp từ 5v –> 3v3) trên kit M4 để tạo nguồn 3v3 cho MCU (Thông thường để ổn định, người ta thường thiết kế cấp nguồn riêng cho MCU, ko dùng chung với ngoại vi).
**Khi cấp nguồn qua vị trí này, bạn hàn header dài như lưu ý ở phần #1.**

![Tiva C launchpad](/img/luu-y-khi-dung-launchpad/Tiva-C-launchpad-3.jpg){: .center-block :}

**3. Đệm giữa I/O của MCU với ngoại vi**

Có thể dùng transistor, ULN2803 như giới thiệu trong buổi training (tất nhiên là phải phù hợp với mục đích sử dụng).
Cần nhớ rằng chân MCU có khả năng cấp / chịu dòng (source/sink current) giới hạn, bạn không được cho chân MCU tải quá giới hạn này (check datasheet,…).
Khi nhiều chân I/O cùng cấp dòng ra có thể làm sụt áp nguồn, chip bị reset, chạy không ổn định, hoặc tệ hơn, die luôn, do đó, với các dòng MCU loại chân dán nhỏ nhỏ như thế này, ta luôn luôn phải đệm giữa chân MCU và các ngoại vi.

Một IC thường được sử dụng là 74HC245 – đệm không đảo (output = input)
Lưu ý có 74HC245 (cấp nguồn được từ 2V – 6V), 74HCT245 (chỉ cấp nguồn 5V). Con này có 8 kênh vào, 8 kênh ra. Hướng truyền tín hiệu được quy định bởi chân DIR (nếu DIR=1 thì input là các chân A, output là các chân B, và ngược lại).
Cách mắc sơ đồ như ví dụ sau:

![Tiva C launchpad](/img/luu-y-khi-dung-launchpad/Tiva-C-launchpad-4.png){: .center-block :}

Khi cấp nguồn như Phần #2, bạn nên dùng nguồn 3v3 trên KIT M4 chỉ dành riêng cho MCU thôi, còn phần mạch phát triển của bạn có thêm một nguồn 3v3 khác, dành cho các ngoại vi, 2 nguồn này chỉ cần nối chung GND.
Nguồn “khác” này cấp cho IC đệm 74HC245.
Nếu muốn tạo ra tín hiệu 5V thì chỉ cần cấp nguồn 5V cho con 74HC245 là OK.
Các chân của TM4C123GH6PM có thể chịu được điện áp 5V, **trừ PD4, PD5, PB0 chỉ chịu được 3.6V**, tuy nhiên Launchpad chỉ có PB0 được layout.
* Nếu cấp điện áp 5V trên chân GPIO khi MCU chưa được cấp nguồn VDD thì giới hạn chịu đựng là 10000 giờ ở nhiệt độ 27 độ C, 5000 giờ ở nhiệt độ 85 độ C, tính cộng dồn trong suốt vòng đời thiết bị.
* Nếu cấp điện áp 3.3V trên chân GPIO khi MCU chưa được cấp nguồn VDD hoặc cấp điện áp 5V trên chân GPIO khi <b>đã</b> cấp nguồn VDD thì không ảnh hưởng đến TM4C123GH6PM.

Túm lại của cái post #3 này là: **bạn luôn luôn phải đệm giữa chân MCU và bất-cứ-thứ-gì-bên-ngoài**, kể cả chỉ xài để bật tắt 1 con LED cho vui mắt.
Đơn giản nhất là dùng transistor (có thể tham khảo trong schematic kit M4, phần nối với con LED RGB: 3 chân đều có BJT đệm giữa). Chú ý thao tác này sẽ tránh được khá nhiều hậu quả.

**4. Không nên dùng nguồn 3v3 từ KIT M4 cấp cho các ngoại vi, ví dụ như Led Hồng ngoại và các IC điều khiển LED.**

Cần thiết kế mạch nguồn 3v3 khác để xài cho các mạch bên ngoài, như tinh thần đã nêu ở post #2 và #3.
Thiết kế mạch đệm trước (v.dụ như post #3), và lấy tín hiệu từ đó điều khiển các module khác. Trong quá trình code và test thử, hạn chế lấy I/O của MCU cắm lung tung. Cứ phải làm mạch đệm trước (nhắc đi và nhắc lại từ post #3 sang post #4)
(nếu dùng 74HC595 để điều khiển LEDs thì lấy các chân SPI từ MCU nối sang luôn, không cần phải đệm, vì các chân này chỉ thực hiện communication, không tiêu thụ nhiều dòng).

**5. Không dùng module Hibernate của M4, không nạp code test module này (hình như là Lab6) vào chip.**

Khi chuyển chế độ sang hibernate, chip có thể không wake up được, nghỉ xài luôn.

**6. Nhẹ nhàng và mềm mại, đối xử dịu dàng với KIT Tiva C Launchpad**

Các linh kiện trên board đều là linh kiện dán, nhỏ bé nên nếu bạn thao tác quá thô bạo sẽ làm chúng nó bong tróc, sút ra,…
Đặc biệt là jack micro-USB, khi tháo cáp phải thật dịu dàng, không được lắc, một tay giữ jack-female (trên board), 1 tay rút cáp, nhẹ nhàng và thoải mái.
Nếu cắm nguồn mà đèn LED nguồn không sáng, đồng thời có gì đó nóng lên (MCU, IC nguồn,…) thì thôi, rút nguồn ra, đem lên support báo cáo tình trạng bệnh.

*Nguồn: payitforward*

**7. GPIO Port C0, C1, C2, C3, D7, E7 và F0 không hoạt động**

Các GPIO kể trên mặc định được khoá lại cho các chức năng JTAG và NMI.
Để sử dụng các pin trên làm IO hoặc bất kì chức năng nào khác, phải mở khoá pin trước.
Việc mở khoá bao gồm quá trình ghi các Key lên thanh ghi tương ứng và phải thực hiện trước khi cấu hình GPIO.
Bên dưới là các ví dụ mở khoá cho GPIO:<br>
**Lưu ý: phải include các header định nghĩa key và địa chỉ thanh ghi, hoặc sử dụng giá trị trực tiếp**

~~~
#include "inc/hw_types.h"
#include "inc/hw_gpio.h"
#include "inc/hw_memmap.h"
~~~

Mở khoá TM4C123GH6PM Port C<br>
**Lưu ý: Port C sử dụng cho cổng JTAG nạp code và debug, không tự ý sử dụng cho chức năng khác**
~~~
HWREG(GPIO_PORTC_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTC_BASE+GPIO_O_CR) |= (GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3);
~~~
Mở khoá TM4C123GH6PM Port D
~~~
HWREG(GPIO_PORTD_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTD_BASE+GPIO_O_CR) |= GPIO_PIN_7;
~~~
Mở khoá TM4C123GH6PM Port F
~~~
HWREG(GPIO_PORTF_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTF_BASE+GPIO_O_CR) |= GPIO_PIN_0;
~~~

**Phần này dùng cho họ <span style="color:red">TM4C129</span>**<br>
Mở khoá TM4C129 Port C
~~~
HWREG(GPIO_PORTC_AHB_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTC_AHB_BASE+GPIO_O_CR) |= (GPIO_PIN_0|GPIO_PIN_1|GPIO_PIN_2|GPIO_PIN_3);
~~~
Mở khoá TM4C129 Port D
~~~
HWREG(GPIO_PORTD_AHB_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTD_AHB_BASE+GPIO_O_CR) |= GPIO_PIN_7;
~~~
Mở khoá TM4C129 Port E
~~~
HWREG(GPIO_PORTE_AHB_BASE+GPIO_O_LOCK) = GPIO_LOCK_KEY;
HWREG(GPIO_PORTE_AHB_BASE+GPIO_O_CR) |= GPIO_PIN_7;
~~~

**8. Sau khi bật (enable) ngoại vi (peripheral), chương trình nhảy vào FaultISR**

Hàm enable viết vào thanh ghi SYSCTL.RCGCxxx, việc này mất 5 chu kì để đánh địa chỉ.
 Nên có một khoảng delay nhỏ để ngoại vi được sẵn sàng. Bên dưới là code mẫu với cách tiếp cận tốt hơn:<br>
~~~
SysCtlPeripheralEnable(SYSCTL_PERIPH_I2C2);
while(!(SysCtlPeripheralReady(SYSCTL_PERIPH_I2C2)));
~~~
**9. Ngắt giả khi chương trình khởi động lại**

Khi chương trình CCS được khởi động lại bằng CPU reset, các ngoại vi không reset gây ra ngắt giả nếu chức năng ngắt được bật.
Có hai giải pháp cho vấn đề này:<br>
* Luôn luôn dùng System Reset thay cho CPU reset.
* Thêm các lệnh để đảm bảo các ngoại vi được reset trước khi kích hoạt clock. Bên dưới là một ví dụ với I2C2, các module khác có thể làm tương tự.
~~~
SysCtlPeripheralDisable(SYSCTL_PERIPH_I2C2);
SysCtlPeripheralReset(SYSCTL_PERIPH_I2C2);
SysCtlPeripheralEnable(SYSCTL_PERIPH_I2C2);
while(!(SysCtlPeripheralReady(SYSCTL_PERIPH_I2C2)));
~~~

**10. JTAG không thể kết nối trên board tuỳ biến**

Tham khảo tài liệu [Using TM4C12x Devices Over JTAG Interface](http://www.ti.com/lit/an/spma075/spma075.pdf)

**11. Xáo trộn Pins, các pin nối trực tiếp với nhau trên Tiva C**

Pin PB7 được nối đến PD1 thông qua một điện trở 0 Ohm, tương tự với PB6 và PD0.
Việc này do Texas Instruments cố tình thiết kế cho Tiva C Launchpad tương thích ngược với boosterpack của board **MSP430G2**.
Vị trí hai điện trở 0 Ohm trên Launchpad: <br>
![Shunted pins Tiva C launchpad](/img/luu-y-khi-dung-launchpad/shunted-pins-tiva-c.jpg){: .center-block :}


**Updating...**
















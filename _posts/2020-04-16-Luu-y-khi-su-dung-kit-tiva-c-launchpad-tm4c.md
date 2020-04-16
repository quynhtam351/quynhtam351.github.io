---
layout: post
title: Lưu ý khi sử dụng KIT Tiva C Launchpad TM4C123GH6PM
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

Khi cấp nguồn như Phần #2, bạn nên dùng nguồn 3v3 trên KIT M4 chỉ dành riêng cho MCUthôi, còn phần mạch phát triển của bạn có thêm một nguồn 3v3 khác, dành cho các ngoại vi, 2 nguồn này chỉ cần nối chung GND.
Nguồn “khác” này cấp cho IC đệm 74HC245.
Nếu muốn tạo ra tín hiệu 5V thì chỉ cần cấp nguồn 5V cho con 74HC245 là OK.

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
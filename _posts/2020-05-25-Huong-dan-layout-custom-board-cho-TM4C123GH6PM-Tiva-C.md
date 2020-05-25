---
layout: post
title: Hướng dẫn layout custom board cho TM4C123GH6PM Tiva C
description: Hướng dẫn layout custom board cho TM4C123GH6PM Tiva C TM4C123GH6PM.
categories: [tiva-c]
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller, US-015]
comments: true
---

TM4C123GH6PM được đóng gói dạng 64-Pin LQFP (Low profile Quad Flat Package), kích thước 10×10×1.4mm. 
Mỗi cạnh IC gồm 16 chân, mỗi chân dài khoảng 1mm. Khoảng cách giữa các chân (tính từ tâm) là 0.5mm (~20mils), khoảng trống giữa 2 chân là 0.25mm (~10mils), độ rộng chân là 0.17 – 0.25mm (~10mils).

![TM4C123GH6PM Dimension](https://quynhtam351.github.io/img/TM4C-custom-board/dimension.png){: .center-block :}

<h2>1.Độ rộng đường dây và khoảng trống:</h2>

Chân IC có độ rộng 0.25mm (~10mils), các đơn vị gia công ở VN thường chỉ gia công được đường mạch tối thiểu 8 mils, do đó ta cần lưu ý khi vẽ mạch. Đối với các đường dây Clock, cần đảm bảo khoảng trống 2 bên lớn hơn 2 lần độ rộng đường dây để tránh “crosstalk” giữa các đường dây.

<h2>2.Lỗ via</h2>

Hạn chế sử dụng lỗ Via và tham khảo kích thước lỗ Via tối thiểu mà đơn vị gia công có thể thực hiện được. Khi có 2 lớp phủ GND ở 2 mặt board, nên thêm một loạt lỗ via nối 2 lớp phủ GND lại với nhau.

<h2>3.Đi dây góc 90 độ</h2>

Theo các nghiên cứu, góc 90 độ không ảnh hưởng đến chất lượng tín hiệu trên dây, tuy nhiên khi vẽ mạch cần tránh góc 90 độ do các lý do sau:

-Góc 90 độ tạo thành một góc “bẫy axit” khi gia công, làm mòn góc, giảm độ rộng thực tế của đường mạch tại góc này.
-Thay góc 90 độ bằng  2 góc 45 độ giúp giảm độ dài đường dây
-Trên thực tế, đi dây 45 độ cho tính thẩm mỹ cao hơn

<h2>4.Phủ đồng</h2>

Phủ đồng giúp giảm nhiễu, tang tính thẩm mỹ, tuy nhiên không nên lạm dụng.
Đặc biệt, không để các lớp phủ đồng nằm riêng lẻ, phải kết nối các lớp phủ đồng đến đường dây hoặc GND.

<h2>5.Không để dây tín hiệu đi qua vết cắt, khe, rãnh trên PCB.</h2>

![TM4C123GH6PM trace layout](https://quynhtam351.github.io/img/TM4C-custom-board/pcb trace layout.png){: .center-block :}

<h2>6.Độ dài đường dây</h2>

Các đường tín hiệu đi cùng nhau nên có cùng độ dài, vẽ đường dây ngắn nhất có thể. Các linh kiện như điện trở treo, tụ… nên đặt cùng khoảng cách đến dây.

![TM4C123GH6PM pair layout](https://quynhtam351.github.io/img/TM4C-custom-board/differential pair layout.png){: .center-block :}

<h2>7.Nguồn</h2>

TM4C chỉ yêu cầu điện áp 3.3V cấp vào VDD và VDDA , các điện áp khác được cấp bởi các mạch hạ áp trong IC.

![TM4C123GH6PM power](https://quynhtam351.github.io/img/TM4C-custom-board/power.png){: .center-block :}

Trong ứng dụng thông thường, các chân GND, GNDA nên được nối với nhau và nối vào GND của nguồn. VDD, VDDA nối với nguồn dương 3.3V.
Nguồn vào cho TM4C phải đảm bảo "sạch", ổn định, ít gợn sóng, đủ dòng. Chất lượng điện áp VDDA còn ảnh hưởng trực tiếp đến giá trị ADC đọc được.
Lưu ý: VDDC là output, tuy nhiên chỉ nối với tụ lọc, không lấy áp từ chân này, không cấp nguồn vào chân này, không dùng chân này cho mục đích khác.

<h2>8.Tụ lọc nguồn LDO:</h2>

Để các mạch hạ áp trong IC hoạt động tốt, TM4C đòi hỏi phải có các tụ lọc nguồn nối vào chân VDDC , giá trị tụ lọc thường từ 3.3uF đến 3.4uF.
-	Tụ lọc thường chia ra cho tất cả các chân VDDC nhưng không nhất thiết phải bằng nhau. Ví dụ trong hình dưới, pin 56 được lắp tụ 2.2uF, 1.0uF và 0.1uF, pin 25 được lắp tụ 0.1uF (tổng 3.4uF).
-	Tụ lớn nhất nên đặt gần chân IC nhất có thể.
-	Giá trị Max EDR trong datasheet cho CLDO phải được bao gồm điện trở của via và đường dây.
-	Các chân VDDC phải được nối với nhau.

![TM4C123GH6PM capacitor](https://quynhtam351.github.io/img/TM4C-custom-board/capacitor placement.png){: .center-block :}

<h2>9.Tụ Decoupling</h2>

Nên có tụ decoupling ở mỗi cạnh của IC, tổng giá trị tụ thường từ 2 – 22uF. Thiếu tụ có thể gây ra vấn đề khi sử dụng các GPIO xuất dòng lớn. Điện áp ngưỡng của tụ nên từ 6.3 – 25V.
Vị trí các tụ nên đặt gần chân IC nhất có thể.

![TM4C123GH6PM routing](https://quynhtam351.github.io/img/TM4C-custom-board/routing option.png){: .center-block :}

<h2>10.	Chia đường nguồn và GND</h2>

TM4C được thiết kế để đường nguồn VDD và VDDA sử dụng chung nguồn 3.3V, tuy nhiên trong những ứng dụng đòi hỏi độ chính xác cao ở ADC, ta có thể tách đường nguồn VDDA riêng để cung cấp điện áp sạch hơn. VDD và VDDA cần được cấp – ngắt nguồn đồng thời với nhau. GND và GNDA luôn phải được nối với nhau, thường là nối vào phủ đồng.

<h2>11.	VREF+ / VREF- </h2>

Đây là điện áp tham chiếu ngoài cho chức năng ADC, nếu cần kết quả ADC với độ chính xác cao hơn, ta có thể sử dụng chân này để cung cấp điện áp tham chiếu có chất lượng cao hơn cho TM4C. 
Không để trống VREF+ / VREF-. Nếu không sử đụng điện áp tham chiếu ngoài, VREF+  phải được nối vào VDDA. Còn VREF-  nối vào GNDA.

<h2>12.	VBAT</h2>

VBAT để cung cấp nguồn điện nuôi RAM hoặc module RTC khi không có VDD. VBAT có thời gian trễ được cung cấp trong datasheet. Nếu dùng pin cúc áo làm nguồn Vbat thì cần có mạch lọc. Nếu không dùng Vbat, chân VBAT phải được nối lên VDD.

![TM4C123GH6PM vbat](https://quynhtam351.github.io/img/TM4C-custom-board/vbat.png){: .center-block :}

<h2>13.	Thạch anh</h2>

Thạch anh đi kèm 2 tụ lọc, tất cả đặt gần chân IC nhất có thể. Đường dây nối tụ và thạch anh đến 2 chân nên bằng nhau. Dây rộng tối thiểu là T10 (10 mils ~ 0.25mm), độ dài nên ngắn hơn 0.25in (6mm), không dài quá 0.75in (18mm).

![TM4C123GH6PM crystal](https://quynhtam351.github.io/img/TM4C-custom-board/crystal.png){: .center-block :}

<h2>14.	Hibernate</h2>

Chân WAKE và HIB được dùng ở chế độ ngủ đông tiết kiệm năng lượng. Nếu không dùng thì WAKE phải được nối GND, HID có thể bỏ trống.

<h2>15.	Thứ tự chân</h2>

![TM4C123GH6PM pins](https://quynhtam351.github.io/img/TM4C-custom-board/pin.png){: .center-block :}

-	Các GPIO khi không sử dụng có thể để trống hoặc nối đất.
-	GNDX không dùng phải nối đất.
-	Chân reset RST nối với jumper đặt gần cụm JTAG để nối với mạch nạp.TM4C có sẵn mạch power on – reset trong IC.
-	Dòng TM4C123GH6PM không có chân NC (No Connects – Chân bỏ trống).

<h2>16.	JTAG</h2>

Theo yêu cầu cơ bản thì JTAG bao gồm các chân TCK, TMS, TDI, TDO tương ứng với chân 49, 50, 51, 52 của TM4C123GH6PM. Ngoài ra còn cần đến chân Reset (38).
Hiện tại mình cũng chỉ dùng mạch nạp đi kèm Kit Tiva C để nạp cho TM4C trên custom board, chưa thử các mạch nạp JTAG khác.
Yêu cầu cơ bản của đường JTAG là có điện trở 10k kéo lên ở chân TCK và TMS nếu đường dây dài hơn 1 inch (2.5cm).

Updating...


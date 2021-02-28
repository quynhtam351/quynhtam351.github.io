---
layout: post
title: Sửa lỗi driver Intel Management Engine Interface, máy tính tắt không hoàn toàn
categories: [windows]
tags: [hardware, Intel Management Engine Interface, driver]
comments: false
---

## Intel Management Engine Interface là gì?

Intel® Management Engine là một bộ vi điều khiển nhúng (được tích hợp trên một số chipset Intel), nó chạy hệ điều hành microkernel nhỏ nhẹ, cung cấp nhiều tính năng và dịch vụ khác nhau cho các hệ thống máy tính dựa trên bộ xử lý Intel®.

## Các lỗi thường gặp đối với driver Intel Management Engine Interface

Các dòng chip khác nhau của Intel có driver Intel Management Engine Interface riêng, ví dụ driver version 9.x dành riêng cho chipset đời 4 và 5, driver version 11.x dành cho các chipset mới hơn.
Tuy nhiên, trình cập nhật driver của Windows lại không quan tâm đến điều đó, nó luôn cập nhật phiên bản driver mới nhất cho Intel Management Engine Interface, điều này dẫn đến một số lỗi khó chịu cho người sử dụng.
Lỗi thường gặp nhất là sau khi shutdown máy tính, ta không thể mở máy lên bình thường mà phải rút toàn bộ nguồn điện, đè nút nguồn hoặc tháo pin đối với laptop. Nguyên nhân là do driver hoạt động không đúng khiến phần cứng không được tắt hoàn toàn, treo máy nhưng màn hình tắt khiến ta lầm tưởng máy đã tắt.
Đây cũng là nguyên nhân khiến laptop bị nóng dù đã tắt, hoặc laptop hết pin khi shutdown.

## Sửa lỗi driver Intel Management Engine Interface, máy tính tắt không hoàn toàn

Để sửa lỗi driver Intel Management Engine Interface, máy tính tắt không hoàn toàn, ta phải thực hiện 2 bước:
- Tắt trình update driver tự động của windows, tránh việc windows tự động update driver lên phiên bản mới nhất, tiếp tục gây lỗi.
- Tải driver Intel Management Engine Interface từ tranh chủ nhà sản xuất mainboard/laptop sau đó cài đặt vào máy hoặc rollbabck driver về bản cũ.

## Tắt trình update driver tự động của windows

Nhấn tổ hợp phím Windows + R để mở cửa sổ lệnh Run. Tại đây bạn nhập lệnh gpedit.msc rồi nhấn Enter.
Tại cửa sổ Local Group Policy Editor, các bạn mở theo đường dẫn sau:

{: .box-note}
Local Computer Policy → Computer Configuration → Administrative Templates → Windows Components → Windows Update.

Sau đó các bạn nhấp đúp vào dòng *Do not include drivers with Windows Updates*.
Trong hộp thoại tiếp theo, các tick vào Enable sau đó nhấn OK là xong.

![Do not include drivers with Windows Updates](https://quynhtam351.github.io/img/do-not-include-drivers-with-windows-updates.png){: .center-block :}

Ngoài ra còn có các cách Tắt trình update driver tự động của windows khác, các bạn có thể tra google để tham khảo thêm.

## Tải driver Intel Management Engine Interface từ tranh chủ nhà sản xuất

Tuỳ nhà sản xuất mainboard/laptop của bạn mà có cách tải driver khác nhau, bạn chỉ cần tìm kiếm trên google "driver Intel Management Engine Interface <tên máy của bạn>" sau đó chọn đúng trang web của NSX, tải về và cài đặt là xong.
Lưu ý chọn đúng phiên bản cho windows bạn đang dùng nhé.

Trên đây là các sửa lỗi driver Intel Management Engine Interface, máy tính tắt không hoàn toàn, hi vọng có thể giúp máy tính của bạn hoạt động ổn định hơn. Chúc các bạn thành công!
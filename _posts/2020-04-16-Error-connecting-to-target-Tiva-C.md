---
layout: post
title: Sửa lỗi Error connecting to target Tiva C
subtitle: Tiva C Error connecting to target fix
tags: [tivaC, TM4C123GH6PM, ARM, microcontroller]
comments: true
---
Đường đời chẳng mấy khi suôn sẻ, biết đâu ngày nào đó trên con đường trở thành coder dạo của bạn gặp trắc trở.
Chẳng hạn như 1 buổi chiều trưa hè nóng nực, bạn đang hì hục code bật tắt led trên kit Tiva M4 để mang đi thi RYA, MCU contest,.. thì tự dưng bạn gặp lỗi "**Cortex_m4_0 error connecting to the target**".

![TivaC-error-connecting](/img/TivaC-error-connecting/Error-connecting-to-the-target-tiva-c.png){: .center-block :}

Bạn thấy điều đó thật lạ lẫm, bạn bấm nạp đi nạp lại, bạn tháo cáp usb, khởi động lại máy nhưng khi nạp vẫn hiện lên lỗi đó. Bạn hoang mang, bạn lo lắng khi sắp mất toi 280k cho cái kit vừa mua.

Nguyên nhân: là do đã bị lock jtag dẫn đến không thể nạp code được.

Hướng dẫn unlock kit:

Đầu tiên các bạn tải phần mềm [Stellaris LM Flash Programmer](http://www.ti.com/tool/lmflashprogrammer)

Sau đó cài đặt, mở chương trình lên:
- Cắm kit vào máy tính
- Bật sang tab Other Utilities, tick chọn "Fury, Dust Devil, TM4C123 and TM4C129 Classes", bấm Unlock

![LM-Flash-Programmer](/img/TivaC-error-connecting/LM-Flash-Programmer-1.png){: .center-block :}

- Một Popup hiện ra, bấm Yes để tiếp tục

![LM-Flash-Programmer](/img/TivaC-error-connecting/LM-Flash-Programmer-2.png){: .center-block :}

- Lại một Popup khác xuất hiện, lúc này bạn một tay nhấn giữ nút RESET trên kit, một tay rê chuột click OK

![LM-Flash-Programmer](/img/TivaC-error-connecting/LM-Flash-Programmer-3.png){: .center-block :}

- Tiếp đó lại có 1 Popup khác hiện lên, bạn thả nút RESET và nhấn OK để chương trình tiến hành xóa bộ nhớ flash trên kit.

![LM-Flash-Programmer](/img/TivaC-error-connecting/LM-Flash-Programmer-4.png){: .center-block :}

- Đợi 1 chút có 1 Popup hiện lên như hình dưới thì chúc mừng, kit bạn đã sống lại, hãy nạp code và tiếp tục con đường coder của mình.

![LM-Flash-Programmer](/img/TivaC-error-connecting/LM-Flash-Programmer-5.png){: .center-block :}

P/S: Bạn chưa gặp lỗi này, bạn muốn thử sức hay bạn muốn trải nghiệm. Hãy tải chương trình mẫu ở link dưới và nạp thử,sau đó hãy nạp 1 chương trình khác (Nếu may mắn kit bạn sẽ bị lỗi như trên và làm theo hướng dẫn)
(https://github.com/PIFClub/TIVAM4Tutorials/tree/master/TIVAM4_TUT_PWM_ControllingLedBrightness)
Nguồn: payitforward

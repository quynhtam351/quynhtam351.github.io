---
layout: post
title: Raspberry Pi 4 booting from SSD via Orico adapter JMS578 base chipset
categories: [raspberrypi]
tags: [hardware, raspberrypi, driver, orico, jms578]
comments: false
---

Mình vừa mua và vọc vạch Raspberry, nó hoạt động tốt khi boot từ thẻ microSD và cả USB3.0, nhưng mình muốn đi xa hơn với SSD.
Không may là mình mua box SATA to USB3.0 Orico, nó sử dụng chipset JMS578 và có vẻ như Raspberry không tương thích với loại chipset này.

Sau một ngày tra google mà chưa có kết quả khả quan, mình đặt mua một box mới sử dụng chipset của ASMedia, nghe đồn loại chipset này hoạt động hoàn hảo với Raspberry.
Trong thời gian chờ hàng ship về, mình cố fix box Orico này, tìm kiếm trên google, thử 7749 lần và đã làm nó hoạt động được.

Mình tham khảo qua khá nhiều bài thảo luận và tutorial trên internet, một trong số những bài quan trọng là:

[Raspberry Pi USB Boot with JMS578 Based USB-to-SATA Enclosures](https://www.devwithimagination.com/2021/01/03/raspberry-pi-usb-boot-with-jms578-based-usb-to-sata-enclosures/)

[STICKY: If you have a Raspberry Pi 4 and are getting bad speeds transferring data to/from USB3.0 SSDs, read this](https://www.raspberrypi.org/forums/viewtopic.php?t=245931)

[SOLVED: SSD Enclosures with Jmicron chips JMS580 and JMS583 don't boot up. RPi4.](https://github.com/raspberrypi/rpi-eeprom/issues/266)

Trong đó những yếu tố chính bao gồm:

## USB_MSD_PWR_OFF_TIME

Tài liệu trên trang [Raspberry Pi 4 bootloader configuration](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bcm2711_bootloader_config.md) nói rằng:

>During USB mass storage boot, power to the USB ports is switched off for a short time to ensure the correct operation of USB mass storage devices. Most devices work correctly using the default setting: change this only if you have problems booting from a particular device. Setting USB_MSD_PWR_OFF_TIME=0 will prevent power to the USB ports being switched off during USB mass storage boot.
Minimum: 250
Maximum: 5000
Default: 1000 (1 second)

Cơ bản là Raspberry sẽ tắt cổng USB trong một khoảng thời gian nhỏ khi boot, nhưng có vẻ như "tính năng" này khiến Raspberry không thể truy cập SSD được nữa, do đó mình đã thử tắt tính năng này theo khuyến nghị của cư dân mạng.
Để tắt USB_MSD_PWR_OFF_TIME thì bạn cần một thẻ SD hoặc USB để boot vào Raspian, sau đó remote hoặc SSH để sửa EEPROM BOOTLOADER.

~~~
sudo -E rpi-eeprom-config --edit
~~~

Sửa giá trị USB_MSD_PWR_OFF_TIME=0, nếu không có sẵn giá trị này bạn có thể tự thêm vào, sau đó lưu lại rồi reboot. 

## Firmware JMS578

Bên cạnh việc Raspberry ngắt nguồn cổng USB thì firmware của JMS578 cũng là một vấn đề. Mình đã thử qua vài bản, nhưng hiện tại bản firmware boot được là "JMS578-Hardkenel-Release-v173.01.00.02-20190306.bin".
Việc cập nhật firmware khá đơn giản, trên Windows chỉ cần tải về FwUpdateTool.exe, chạy dưới quyền admin, Load file firmware, tick vào ô RD Version và Include JMS577 NVRAM sau đó nhấn Run là xong.

![FwUpdateTool_v1_19_16_24](https://quynhtam351.github.io/img/FwUpdateTool_v1_19_16_24.png){: .center-block :}

Tool cũng có phiên bản cho Linux với tham số mềm dẻo hơn rất nhiều.

Bài viết tham khảo trên odroid [JMS578 firmware update](https://wiki.odroid.com/odroid-xu4/software/jms578_fw_update)

Link tải firmware: [JMS578 firmware download](https://www.usbdev.ru/files/jmicron/jms578firmware/)

## Tắt UAS, sử dụng USB Mass Storage

>USB Attached SCSI (UAS) hoặc USB Attached SCSI Protocol (UASP) là một giao thức máy tính được sử dụng để di chuyển dữ liệu đến và đi từ các thiết bị lưu trữ USB như ổ cứng (HDD), ổ đĩa thể rắn (SSD) và ổ USB. UAS phụ thuộc vào giao thức USB và sử dụng bộ lệnh SCSI tiêu chuẩn. Việc sử dụng UAS thường cung cấp khả năng truyền nhanh hơn so với các trình điều khiển Truyền tải hàng loạt chỉ dành cho lưu trữ khối lượng lớn USB (BOT) cũ hơn.

Tuy chipset JMS578 có hỗ trợ UASP nhưng lại không tương thích với Raspberry, việc tắt UAS sẽ khiến tốc độ chỉ còn khoảng 70% nhưng đạt được sự ổn định.

Để tắt UAS, ta thêm tham số bên dưới vào đầu file /boot/cmdline.txt, bạn có thể thực hiện bằng notepad++ trên Windows hoặc nano trên Raspian.

~~~
usb-storage.quirks=aaaa:bbbb:u
~~~

Trong đó aaaa là  idVendor, bbbb là idProduct.

Thông số idVendor và idProduct có thể tra trên google, tìm trong Devices Manangement của Windows hoặc dùng lệnh dmesg trên Raspian. Tốt nhất bạn nên tự xem thông số của thiết bị mình đángowr hữu vì một số trường hợp adapter có idVendor và idProduct không giống nhau dù cùng dòng sản phẩm.
Đối với box Orico mình dùng thì idVendor là 152d, idProduct là 0578.

## Kết quả

Sau các tinh chỉnh trên, mình clone thẻ SD sang SSD sau đó boot thành công.
Kết quả speed test đạt 200MB/s, mình chưa hài lòng lắm và nghĩ là tìm một box khác tương thích sẽ đỡ tốn công sức và đạt kết quả tốt hơn. Đối với mình thì đây là một trường hợp tích luỹ kinh nghiệm khá tốt.

![SSD Orico SATA to USB JMS578 speed test](https://quynhtam351.github.io/img/raspberrypi-orico-2580u3-test.png){: .center-block :}
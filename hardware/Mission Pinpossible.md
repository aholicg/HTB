---
title: Mission Pinpossible

---

.logicdata file extension -> need Saleae Logic 1, not Sal 2.
<img width="908" height="384" alt="image" src="https://github.com/user-attachments/assets/1dc7155d-9031-4d00-b370-ade0d78931f7" />

based on the image <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/63643d1b-412d-4189-9b66-23b6b427ccad" />
we can see the chip name is `PCF8574T`
<img width="949" height="413" alt="image" src="https://github.com/user-attachments/assets/14222edf-cb60-4e59-9721-2caa262198ae" />

The exported ascii contains keypad pressed keys (obviously) <img width="689" height="974" alt="image" src="https://github.com/user-attachments/assets/45c725c0-a796-4da9-b85d-148eef29747b" />

Notice on the PCB of the "Internal Photo", there's also the name "QAPASS". Searching "QAPASS datasheet" gives us this https://www.alldatasheet.com/html-pdf/1574132/CRYSTAIFONTZ/LCD-1602A/12185/12/LCD-1602A.html
<img width="982" height="868" alt="image" src="https://github.com/user-attachments/assets/ff2938d3-beb3-4037-ac94-ac580ca644c3" />

The decoding table maps according to Low (L) and High (H) values, which mean we should export the Saleae Logic's decoded data in binary.

<img width="880" height="952" alt="image" src="https://github.com/user-attachments/assets/1197bdee-97ea-40d0-b409-84b400ae0542" />

Thought we are done here? No we are not, the first bit sequence, sent in packet 0, "0000  1000", mapped to the table -> nothing.
We should lookup "PCF8574T I2C LCD pinout" as a whole
<img width="989" height="258" alt="image" src="https://github.com/user-attachments/assets/bd8e4ad9-2dad-4db1-afea-c201239230ec" />
*ref*: https://www.kynix.com/components/pcf8574t-nxp-semiconductors-arduino-datasheet-pdf-lcd-pinout.html#pcf8574t-datasheet

Now let's look up "RS, E, and RW" 
*ref*: https://www.exploreembedded.com/wiki/LCD_16_x_2_Basics
<img width="1195" height="168" alt="image" src="https://github.com/user-attachments/assets/fd6a7fab-99e1-404d-9d93-d33a52803615" />

<img width="1236" height="850" alt="image" src="https://github.com/user-attachments/assets/07449534-db39-4b7e-a7de-3eb37fc57d7e" />

So we have to look at the packet where both bit 0 and bit 2 are 1, and the upper 4 bits are data (D4, D5, D6, D7). (Note that the decoded signal in csv already shown that all packets are data WRITE to LCD).

And with that, either decoding manually or using a script: <img width="905" height="474" alt="image" src="https://github.com/user-attachments/assets/a6ae78db-7d2e-47cd-a97c-ffdc685819b1" />

*Flag*: `HTB{84d_d3519n_c4n_134d_70_134k5!d@}`

Lưu ý: so sánh với 1 chall dùng Saleae Logic 2 như Debugging Interface:
1. Protocol: DI's protocol là Async Serial (UART) - không có clock và cần baud rate chính xác, còn với MissPin là I2C - có clock
2. Đối tượng bị intercept trong DI là debugging interface - thứ mà developer sẽ đọc được khi nhận data. Còn đối tượng trong MissPin là LCD.
3. Dữ liệu gửi qua Async Serial trong DI là message thật, chỉ cần decode là xong. Còn dữ liệu gửi trong MissPin là command để điều khiển sự hiển thị trên LCD, không phải chỉ decode là ra message, decode ra tin hiệu lệnh, tín hiệu lệnh vẽ lên LCD mới ra message.

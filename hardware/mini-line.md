---
title: mini-line

---

# Mini Line

## Initial

* Will need gcc-arm-embedded package.
* Đề cho *LPC2148 microcontrollers* -> tìm datasheet -> tìm architecture, bit width, endianess 
![image](https://hackmd.io/_uploads/HyZSNidwgx.png)
* Convert firmware.hex sang firmware (ELF) using  https://community.st.com/t5/automotive-mcus/how-to-convert-hex-file-to-elf-file/td-p/335527: 
```bash
arm-none-eabi-objcopy -I ihex -O elf32-littlearm firmware.hex firmware
```
* Cho vào Ghidra (nên tự setup language thay vì chọn default của Ghidra autodetect).

## Reversing

Sau khi xem qua 1 loạt các hàm thì thấy rằng hàm FUN_00010708 là hàm chứa logic cần điều tra chính
![image](https://hackmd.io/_uploads/HymQ3jdvge.png)
![image](https://hackmd.io/_uploads/HJAinidvxe.png)
* **Logic #1:**
```C
33  for (local_c = 0; local_c < 0x11; local_c = local_c + 1) {
34    FUN_00010674(*(byte *)((int)&local_24 + local_c) ^ 0x1a);
35  }
```
trong đó:
```C
19 local_24 = *(undefined4 *)(DAT_00010888 + 0x10720)
```
Bấm vào `DAT_00010888` để thấy giá trị của nó (xanh nước biển là Little Endian, xanh là cây là Big Endian):
![image](https://hackmd.io/_uploads/SkXM0suDgl.png)
(chuyển hết sang Big Endian) `0x002e0 + 0x10720`
![image](https://hackmd.io/_uploads/rkqhv2dPxl.png)
Press G -> nhập 0x10a00:
![image](https://hackmd.io/_uploads/Byb0vhdDeg.png)
Lấy 0x11=17 bytes: `52 4e 58 61 78 2e 68 29 45 77 29 6e 2e 76 69 45 (?)` nhưng 0x10a00 chỉ có 16 bytes @@ (same with logic2, logic3)
Decode from hex rồi XOR với 0x1a: [cyberchef](https://cyberchef.org/#recipe=From_Hex('Auto')XOR(%7B'option':'Hex','string':'1a'%7D,'Standard',false)&input=MHg1MjRlNTg2MTc4MmU2ODI5NDU3NzI5NmUyZTc2Njk0NQ), ta được phần đầu của flag: `HTB{b4r3_m3t4ls_`

* **Logic 2:**
```C
36  for (local_10 = 0; local_10 < 5; local_10 = local_10 + 1) {
37    FUN_00010674(*(byte *)((int)&local_28 + local_10) >> 1 ^ 0x39);
38  }
```
![image](https://hackmd.io/_uploads/HJc91pOvlg.png)
[cyberchef](https://cyberchef.org/#recipe=From_Hex('Auto')Bit_shift_right(1,'Logical%20shift')XOR(%7B'option':'Hex','string':'39'%7D,'Standard',false)&input=MWE5NjE0Y2M): `4r3_`

* **Logic 3:**
```C
39  for (local_14 = 0; local_14 < 14; local_14 = local_14 + 1) {
40    FUN_00010674(*(byte *)((int)&local_38 + local_14) >> 1 ^ 0x39);
41  }
```
![image](https://hackmd.io/_uploads/S1CLWT_wex.png)
![image](https://hackmd.io/_uploads/BJCyMauvge.png)
![image](https://hackmd.io/_uploads/rJfjDltvlg.png)
[cyberchef](https://cyberchef.org/#recipe=From_Hex('Auto')Bit_shift_right(1,'Logical%20shift')XOR(%7B'option':'Hex','string':'39'%7D,'Standard',false)&input=YmUgOTggYWUgY2MgMTQgMTIgYmEgYmUgMTAgMzAgMzAgMzAgODg): `fun_30df1!!!}`


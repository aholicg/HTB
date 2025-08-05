---
title: Debugging Interface

---


"5 hours of debugging can save you 5 mins of reading document" ahhh moment
![bilde](https://hackmd.io/_uploads/BkhjJw78xl.png)
https://support.saleae.com/protocol-analyzers/analyzer-user-guides/using-async-serial

Command to convert a csv column to row: 
```bash
cut -d',' -f[n] export.csv | tr -d '"' | paste
 -sd '' -
```
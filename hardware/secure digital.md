---
title: secure digital

---

a quick search for "microSD card protocol" returned "SPI".
document for saleae logic SPI [here](https://support.saleae.com/protocol-analyzers/analyzer-user-guides/using-spi).
from description "They could then trigger the read operation of the key that is transmitted over this unprotected serial interface. Can you find out what was read by the microSD card" -> cần decode MISO - Master In Slave Out
```bash
cut -d',' -f5 export.csv | tr -d '"' | paste -sd '' - | grep -i "HTB" > test.txt
```
![bilde](https://hackmd.io/_uploads/B1AuyG9Igx.png)


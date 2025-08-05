---
title: Rids

---

```python
import socket
import json

def exchange(hex_list, value=0):
    host = '94.237.54.192'
    port = 42807
    cs = 0
    usb_device_url = 'ftdi://ftdi:2232h/1'

    command_data = {
        "tool": "pyftdi",
        "cs_pin": cs,
        "url": usb_device_url,
        "data_out": [hex(x) for x in hex_list],
        "readlen": value
    }

    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(json.dumps(command_data).encode('utf-8'))
        data = b''
        while True:
            part = s.recv(1024)
            data += part
            if data.endswith(b']'):
                break
        response = json.loads(data.decode('utf-8'))
    return response

# === Memory Search ===
def find_flag():
    for addr in range(0, 0x1000000, 256):  # Read in 256-byte blocks
        a2 = (addr >> 16) & 0xFF
        a1 = (addr >> 8) & 0xFF
        a0 = addr & 0xFF

        data = exchange([0x03, a2, a1, a0], 256)

        # Convert bytes to ASCII text
        text = ''.join(chr(b) if 32 <= b <= 126 else '.' for b in data)

        if "HTB{" in text:
            print(f"\n[+] Flag found at 0x{addr:06X}:\n{text}\n")
            break
    else:
        print("[-] Flag not found in scanned range.")

find_flag()
```
![image](https://hackmd.io/_uploads/S13q4QtPgl.png)

well well well...
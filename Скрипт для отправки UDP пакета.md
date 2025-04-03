```python
import socket  
  
#udp_ip = "[31.173.80.225](https://31.173.80.225/ "https://31.173.80.225/")" # IP-адрес получателя  
udp_ip = "[172.16.0.150](https://172.16.0.150/ "https://172.16.0.150/")" # IP-адрес получателя  
udp_port = 5555 # Порт получателя  
source_port = 5555 # Порт источника  
  
message = "Hello, UDP!"  
  
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM) # Создаем сокет  
sock.bind(('[0.0.0.0](https://0.0.0.0/ "https://0.0.0.0/")', source_port)) # Связываем сокет с портом источника  
sock.sendto(message.encode(), (udp_ip, udp_port)) # Отправляем сообщение  
sock.close() # Закрываем сокет
```

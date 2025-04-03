```python
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
```
### Аргументы:

1. **`socket.AF_INET`:**
    
    - Это параметр указывает адресное семейство, которое будет использоваться сокетом. `AF_INET` обозначает использование IPv4. Если вы хотите использовать IPv6, вы бы использовали `AF_INET6`.
    - `AF_INET` расшифровывается как Address Family (семейство адресов) для Интернета, и он используется для адресов IPv4.
2. **`socket.SOCK_DGRAM`:**
    
    - Этот параметр указывает тип сокета. `SOCK_DGRAM` обозначает, что сокет будет использовать протокол UDP (User Datagram Protocol).
    - `SOCK_DGRAM` означает "датаграммный сокет". Датаграммы — это независимые, самодостаточные сообщения, которые отправляются без установки соединения и без гарантии доставки, что является отличительной чертой UDP.

### Сравнение с TCP

Для сравнения, если бы вы хотели создать TCP-сокет, вы бы использовали `SOCK_STREAM` вместо `SOCK_DGRAM`:

```python
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

### Примеры использования
#### UDP

```python
import socket

# Создаем UDP-сокет
udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Привязываем сокет к локальному адресу и порту
udp_socket.bind(("0.0.0.0", 12345))

# Отправляем UDP-пакет
udp_socket.sendto(b"Hello, World!", ("192.168.1.2", 12345))

# Получаем UDP-пакет
data, addr = udp_socket.recvfrom(1024)
print(f"Received message: {data.decode()} from {addr}")

# Закрываем сокет
udp_socket.close()
```
`udp_socket.recvfrom(1024)` является блокирующим, что означает, что выполнение программы будет остановлено (или "заблокировано") до тех пор, пока не будет получен UDP-пакет.

**Прослушивание на всех интерфейсах:** Адрес `0.0.0.0` указывает на то, что сокет будет прослушивать все доступные сетевые интерфейсы на данном хосте. Это означает, что сокет будет принимать пакеты, отправленные на любой из IP-адресов, назначенных хосту.

#### TCP
```python
import socket

# Создаем TCP-сокет
tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Привязываем сокет к локальному адресу и порту
tcp_socket.bind(("0.0.0.0", 12345))

# Начинаем прослушивание входящих соединений
tcp_socket.listen(5)

print("Server is listening on port 12345...")

# Принимаем входящее соединение
client_socket, client_address = tcp_socket.accept()
print(f"Connection from {client_address}")

# Получаем данные
data = client_socket.recv(1024)
print(f"Received data: {data.decode()}")

# Отправляем ответ
client_socket.send(b"Hello from server")

# Закрываем соединение с клиентом
client_socket.close()

# Закрываем серверный сокет
tcp_socket.close()
```

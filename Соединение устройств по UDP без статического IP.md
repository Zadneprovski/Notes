Получится соединить два устройства, только если они будут пытаться слать друг другу пакеты. Устройство, которое должно принимать пакеты, не обязано отсылать пакеты для связи постоянно, только вначале, но через время, когда у NAT обновятся таблицы (примерно 30 - 60 сек), в случае, если никакие пакеты не отправляются, связь нужно будет устанавливать заново.

## Вариант 1

Можно отправить UDP-пакет на устройство, находящееся за CGNAT, если это устройство сначала установит соединение с сервером. Это возможно благодаря технике, называемой **"UDP hole punching"**. Вот как это можно реализовать:

### Шаги для реализации

1. **Устройство за CGNAT инициирует соединение:**
    
    - Устройство за CGNAT (клиент) отправляет UDP-пакет на сервер. Это создает запись в NAT таблице на маршрутизаторе, что позволяет серверу отправлять пакеты обратно на устройство.
2. **Сервер отправляет ответ:**
    
    - После получения UDP-пакета от клиента, сервер может отправлять UDP-пакеты на IP-адрес и порт клиента, указанный в полученном пакете.

### Пример реализации

#### Серверный код (Python)

```python
import socket

# Создаем UDP сокет
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.bind(("0.0.0.0", 12345))  # Сервер слушает на порту 12345

while True:
    data, addr = sock.recvfrom(1024)  # Получаем сообщение от клиента
    print(f"Received message from {addr}: {data.decode()}")

    # Отправляем ответ клиенту
    response = "Hello from server"
    sock.sendto(response.encode(), addr)
```

#### Клиентский код (Python)

```python
import socket

# Создаем UDP сокет
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_address = ("<серверный IP>", 12345)  # Адрес сервера и порт

try:
    # Отправляем сообщение серверу
    message = "Hello from client"
    sock.sendto(message.encode(), server_address)

    # Получаем ответ от сервера
    data, server = sock.recvfrom(1024)
    print(f"Received message from server: {data.decode()}")

finally:
    sock.close()
```

### Пояснения

1. **Клиентский код:**
    
    - Клиент отправляет сообщение на сервер. Этот пакет проходит через CGNAT, создавая запись в NAT таблице.
    - Затем клиент ждет ответа от сервера.
2. **Серверный код:**
    
    - Сервер получает сообщение от клиента, запоминая адрес и порт клиента.
    - Затем сервер отправляет ответное сообщение на тот же адрес и порт.

### Замечания

- **Стабильность соединения:** Если NAT таблица на маршрутизаторе очищается (например, при простое), соединение может быть разорвано. Клиент должен периодически отправлять пакеты на сервер, чтобы поддерживать запись в NAT таблице.

## Вариант 2

Возможно соединить два устройства, находящиеся за NAT, между собой через сервер. Это может быть реализовано с использованием техники, известной как "UDP hole punching". Сервер будет выступать как посредник для установления соединения между двумя устройствами.

### Шаги для реализации

1. **Оба устройства (клиенты) устанавливают соединение с сервером.**
2. **Сервер обменяется информацией об адресах и портах клиентов.**
3. **Устройства начнут отправлять UDP-пакеты напрямую друг другу, используя информацию, полученную от сервера.**

### Пример реализации

#### Серверный код (Python)

Серверный код будет обрабатывать два клиента и обмениваться их адресами.
```python
import socket

def main():
    # Создаем UDP сокет
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind(("0.0.0.0", 12345))  # Сервер слушает на порту 12345

    clients = []

    while len(clients) < 2:
        data, addr = sock.recvfrom(1024)  # Получаем сообщение от клиента
        print(f"Received message from {addr}: {data.decode()}")
        clients.append(addr)
        if len(clients) == 2:
            break

    # Обмениваемся адресами клиентов
    sock.sendto(f"{clients[1][0]}:{clients[1][1]}".encode(), clients[0])
    sock.sendto(f"{clients[0][0]}:{clients[0][1]}".encode(), clients[1])

    print("Addresses exchanged, clients can now communicate directly")

if __name__ == "__main__":
    main()
```

#### Клиентский код (Python)

Каждый клиент будет подключаться к серверу, получать адрес другого клиента и начинать прямое общение.
```python
import socket

def main(server_ip):
    # Создаем UDP сокет
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    server_address = (server_ip, 12345)  # Адрес сервера и порт

    # Отправляем сообщение серверу
    message = "Hello from client"
    sock.sendto(message.encode(), server_address)


    data = 0

    while True:
        try:
            sock.settimeout(5)  # Установить тайм-аут на 5 секунд
            data, _ = sock.recvfrom(1024)
            break
        except socket.timeout:
            print("No response from server, resending data...")
            sock.sendto(message.encode(), server_address)


    # Получаем адрес другого клиента от сервера
    other_client_address = data.decode().split(":")
    other_client_address = (other_client_address[0], int(other_client_address[1]))

    print(f"Received other client address: {other_client_address}")

    # Начинаем отправлять сообщения другому клиенту
    while True:
        message = "message to send"
        sock.sendto(message.encode(), other_client_address)
        data, _ = sock.recvfrom(1024)
        print(f"Received message: {data.decode()}")


server_ip = "194.87.214.82"
main(server_ip)
```

### Объяснение работы

1. **Сервер:**
    
    - Сервер ждет подключения от двух клиентов.
    - Когда оба клиента подключены, сервер обменивается их адресами и портами.
2. **Клиенты:**
    
    - Клиенты подключаются к серверу и получают адрес и порт другого клиента.
    - После получения адреса другого клиента они начинают прямое общение друг с другом, используя UDP.

### Замечания

- **Поддержание соединения:** Для поддержания соединения через NAT устройства должны периодически отправлять пакеты друг другу, чтобы запись в NAT таблице не истекла.
- **Безопасность:** Убедитесь, что обмен адресами и портами происходит в защищенном режиме, чтобы предотвратить утечки информации.
- **Ошибки и повторные попытки:** Реализуйте обработку ошибок и повторные попытки подключения для повышения надежности соединения.

Этот метод позволяет двум устройствам, находящимся за NAT, устанавливать прямое соединение друг с другом через промежуточный сервер.


### 3 Вариант
```python
import socket  
import threading  
import time  
from datetime import datetime  
  
  
def send_keep_alive(sock, other_client_address):  
    while True:  
        try:  
            message = "keep-alive"  
            sock.sendto(message.encode(), other_client_address)  
            print("keep-alive for",other_client_address)  
            time.sleep(20)  # Отправляем keep-alive каждые 20 секунд  
        except Exception as e:  
            print(f"Error sending keep-alive: {e}")  
            break  
  
  
def send_time(sock, other_client_address):  
    while True:  
        try:  
            # Отправляем текущее время другому клиенту  
            message = datetime.now().strftime("%Y-%m-%d %H:%M:%S")  
            sock.sendto(message.encode(), other_client_address)  
            time.sleep(1)  # Отправляем сообщение раз в секунду  
        except Exception as e:  
            print(f"Error sending time: {e}")  
            break  
  
  
def receive_messages(sock):  
    while True:  
        try:  
            data, _ = sock.recvfrom(1024)  
            print(f"Received message: {data.decode()}")  
        except Exception as e:  
            print(f"Error receiving message: {e}")  
            break  
  
  
def main(server_ip, role):  
    while True:  
        try:  
            sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)  
            server_address = (server_ip, 12345)  
  
            message = "Hello from client"  
            sock.sendto(message.encode(), server_address)  
  
            # Получаем адреса других клиентов от сервера  
            data, _ = sock.recvfrom(1024)  
            other_client_address = data.decode().split(":")  
            other_client_address = (other_client_address[0], int(other_client_address[1]))  
  
            print(f"Received other client address: {other_client_address}")  
  
  
  
            # Запускаем поток для отправки keep-alive пакетов  
            threading.Thread(target=send_keep_alive, args=(sock, other_client_address)).start()  
  
            if role == "master":  
                # Запускаем поток для отправки текущего времени  
                threading.Thread(target=send_time, args=(sock, other_client_address)).start()  
                print("Master started\n")  
            else:  
                # Запускаем поток для приема сообщений  
                threading.Thread(target=receive_messages, args=(sock,)).start()  
                print("Slave started")  
  
            # Бесконечно ждем ошибки, чтобы перезапустить цикл и переподключиться  
            while True:  
                time.sleep(1)  
  
        except Exception as e:  
            print(f"Error: {e}. Reconnecting in 5 seconds...")  
            time.sleep(5)  # Ждем 5 секунд перед повторной попыткой подключения  
  
  
if __name__ == "__main__":  
    server_ip = "194.87.214.82"  
    role = "master"  
    main(server_ip, role)
```

#### Сервер

```python
import socket
import threading

def listener_5555(sock, false):
    while True:
        data, addr = sock.recvfrom(1024) 
        print(f"Received message from {addr}: {data.decode()}")

def main():
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind(("0.0.0.0", 12345)) 
    
    sock2 = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock2.bind(("0.0.0.0", 5555)) 

    threading.Thread(target=listener_5555, args=(sock2, False)).start()
    clients = []

    while len(clients) < 2:
        data, addr = sock.recvfrom(1024) 
        print(f"Received message from {addr}: {data.decode()}")

        if addr not in clients:
            clients.append(addr)
            print(f"Added new client: {addr}")
    
    if len(clients) == 2:
        sock.sendto(f"{clients[1][0]}:{clients[1][1]}".encode(), clients[0])
        sock.sendto(f"{clients[0][0]}:{clients[0][1]}".encode(), clients[1])

		clients.clear()
        print("Addresses exchanged, clients can now communicate directly")

if __name__ == "__main__":
    main()

```
[[Разделы памяти]]
[[Удаленная отладка]]

Для написания программы на Raspberry Pi, которая будет отправлять глобальный IP-адрес на внешний сервис, можно использовать язык Python. Программа будет выполнять следующие шаги:

1. Узнавать глобальный IP-адрес.
2. Отправлять этот IP-адрес на внешний сервис через HTTP-запрос.

Ниже приведен пример программы, которая выполняет эти шаги. Для выполнения HTTP-запросов используется библиотека `requests`.

#### Установка необходимых библиотек
```
pip install requests opencv-python
```

### Пример программы

Создайте файл `report_ip.py` и добавьте в него следующий код:

```python
import requests
import cv2
import numpy as np
import socket
import threading

def get_public_ip():
    """
    Получает глобальный IP-адрес, отправляя запрос к сервису ifconfig.me.

    Returns:
        str: Глобальный IP-адрес в виде строки, если запрос успешен.
        None: Если произошла ошибка при выполнении запроса.
    """
    try:
        # Отправка GET-запроса к сервису ifconfig.me
        response = requests.get('https://ifconfig.me')
        # Проверка успешности запроса 
        response.raise_for_status()
        # Возвращение IP-адреса как строки без лишних пробелов
        return response.text.strip()
    except requests.RequestException as e:
        # Вывод ошибки в случае неудачи
        print(f"Error getting public IP: {e}")
        return None

def report_ip_to_service(ip, device_id, service_url):
    """
    Отправляет глобальный IP-адрес на внешний сервис через POST-запрос.

    Args:
        ip (str): Глобальный IP-адрес.
        device_id (str): Идентификатор устройства.
        service_url (str): URL внешнего сервиса для отправки данных.
    """
    try:
        # Подготовка данных для отправки в формате JSON
        payload = {'ip': ip, 'id': device_id}
        # Отправка POST-запроса
        response = requests.post(service_url, json=payload)
        # Проверка успешности запроса
        response.raise_for_status()
        # Вывод сообщения об успешной отправке
        print(f"Successfully reported IP: {ip} with ID: {device_id}")
    except requests.RequestException as e:
        # Вывод ошибки в случае неудачи
        print(f"Error reporting IP to service: {e}")

def receive_video_udp(host='0.0.0.0', port=5555):
    """
    Функция для получения видеопотока по UDP и отображения его с минимальной задержкой.

    Args:
        host (str): Хост для прослушивания.
        port (int): Порт для прослушивания.
    """
    # Создание UDP-сокета
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind((host, port))
    print(f"Listening for video stream on {host}:{port}...")

    while True:
        # Получение данных от отправителя
        data, addr = sock.recvfrom(65536)
        if not data:
            break

        # Преобразование полученных данных в изображение
        frame = np.frombuffer(data, dtype=np.uint8)
        frame = cv2.imdecode(frame, cv2.IMREAD_COLOR)

        # Отображение изображения
        cv2.imshow('Received Video', frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    sock.close()
    cv2.destroyAllWindows()

if __name__ == "__main__":
    # URL внешнего сервиса для отправки данных
    service_url = "http://194.87.214.82:5001/report-ip"
    # Получение глобального IP-адреса
    public_ip = get_public_ip()
    # Идентификатор устройства
    device_id = "1"

    if public_ip:
        # Отправка IP-адреса и ID на внешний сервис
        report_ip_to_service(public_ip, device_id, service_url)

    # Запуск приема видеопотока в отдельном потоке
    video_thread = threading.Thread(target=receive_video_udp)
    video_thread.start()
  
```
[[UDP|Подробнее про UDP]]
### Настройка внешнего сервиса

[[Создание сервиса с помощью процесс менеджера systemd]]

Внешний сервис, на который отправляется IP-адрес, должен быть настроен для приема POST-запросов с JSON-полем `ip`, содержащим IP-адрес. Как пример, это может быть простой веб-сервер, написанный на Flask, который принимает и сохраняет IP-адреса.

Пример сервера на Flask:

```python
from flask import Flask, request, jsonify

# Создание экземпляра Flask приложения
app = Flask(__name__)

# В памяти хранится база данных для хранения IP-адресов с их идентификаторами
ip_database = {}

@app.route('/report-ip', methods=['POST'])
def report_ip():
    """
    Обрабатывает POST-запрос для записи IP-адреса с идентификатором.
    
    Ожидает JSON-данные в формате:
    {
        "ip": "IP-адрес",
        "id": "Идентификатор"
    }
    
    Возвращает:
        JSON-ответ с результатом операции.
    """
    data = request.get_json()  # Получение JSON данных из запроса
    ip = data.get('ip')  # Извлечение IP-адреса
    device_id = data.get('id')  # Извлечение идентификатора
    
    if ip and device_id:
	    # Сохранение IP-адреса в базе данных
        ip_database[device_id] = ip  
        # Вывод отладочной информации
        print(f"Received IP: {ip} for ID: {device_id}")
        # Возврат успешного ответа
        return jsonify({"status": "success", "id": device_id, "ip": ip}), 200  
    # Возврат ошибки, если данные не предоставлены
    return jsonify({"status": "error", "message": "IP or ID not provided"}), 400  

@app.route('/get-ip/<id>', methods=['GET'])
def get_ip(id):
    """
    Обрабатывает GET-запрос для получения IP-адреса по идентификатору.
    
    Параметры:
        id (str): Идентификатор устройства.
    
    Возвращает:
        JSON-ответ с IP-адресом, если найден, или сообщение об ошибке.
    """
    # Поиск IP-адреса по идентификатору в базе данных
    ip = ip_database.get(id)  
    if ip:
	    # Возврат успешного ответа, если IP-адрес найден
        return jsonify({"status": "success", "id": id, "ip": ip}), 200  
    # Возврат ошибки, если идентификатор не найден
    return jsonify({"status": "error", "message": "ID not found"}), 404  

if __name__ == "__main__":
    # Запуск Flask приложения на всех интерфейсах (0.0.0.0) и порту 5001
    app.run(host='0.0.0.0', port=5001)
```

[[Сетевой интерфейс|Что значит host='0.0.0.0']]

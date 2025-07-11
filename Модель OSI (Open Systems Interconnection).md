### 1. **Физический уровень (Physical Layer)**

- **Задача**: Передача данных по физическим каналам связи (кабели, волоконно-оптические линии и т. д.).
- **Протоколы**: На этом уровне не существует конкретных сетевых протоколов, это физические технологии передачи данных (например, Ethernet, Wi-Fi).

---

### 2. **Канальный уровень (Data Link Layer)**

- **Задача**: Обеспечение надежной передачи данных между двумя узлами в пределах одной сети (например, в локальной сети).
- **Протоколы**:
    - **Ethernet**: Стандарт канала передачи данных для локальных сетей (LAN).
    - **PPP (Point-to-Point Protocol)**: Протокол для установления связи между двумя устройствами.
    - **HDLC (High-Level Data Link Control)**: Протокол для синхронизации и передачи данных по каналу связи.

---

### 3. **Сетевой уровень (Network Layer)**

- **Задача**: Определение маршрута, по которому данные должны пройти от источника до получателя. Обеспечивает адресацию и маршрутизацию.
- **Протоколы**:
    - **IPv4 (Internet Protocol version 4)**: Протокол для маршрутизации данных в интернете, использующий 32-битные IP-адреса.
    - **IPv6 (Internet Protocol version 6)**: Расширение IPv4, использующее 128-битные IP-адреса.
    - **ICMP (Internet Control Message Protocol)**: Протокол для диагностики и управления сетевыми соединениями (например, используется командой `ping`).
    - **ARP (Address Resolution Protocol)**: Протокол для разрешения IP-адреса в MAC-адрес.

---

### 4. **Транспортный уровень (Transport Layer)**

- **Задача**: Обеспечение надежной доставки данных между двумя хостами. Разделяет данные на пакеты и управляет их доставкой.
- **Протоколы**:
    - **TCP (Transmission Control Protocol)**: Надежный протокол, обеспечивающий гарантированную доставку данных и контроль за их порядком.
    - **UDP (User Datagram Protocol)**: Протокол без установления соединения, используется для передачи данных, где важна скорость, но не гарантируется их порядок или доставка (например, для потокового видео).

---

### 5. **Сеансовый уровень (Session Layer)**

- **Задача**: Управление сеансами связи, синхронизация и восстановление соединений.
- **Протоколы**:
    - **NetBIOS**: Протокол для связи в локальных сетях, в частности в сетях Microsoft.
    - **RPC (Remote Procedure Call)**: Протокол для удаленного вызова процедур на другом компьютере.
    - **SMB (Server Message Block)**: Протокол для доступа к файлам, принтерам и другим ресурсам в сети.

---

### 6. **Уровень представления (Presentation Layer)**

- **Задача**: Преобразование данных в удобный для понимания и обработки вид, включая кодировку и сжатие данных.
- **Протоколы**:
    - **SSL/TLS**: Протоколы для обеспечения безопасности передачи данных (шифрование).
    - **JPEG, GIF, PNG**: Стандарты сжатия изображений.
    - **MPEG**: Стандарты сжатия видео.

---

### 7. **Прикладной уровень (Application Layer)**

- **Задача**: Обеспечение взаимодействия приложений с сетью, то есть реализация сетевых сервисов для конечных пользователей.
- **Протоколы**:
    - **HTTP (HyperText Transfer Protocol)**: Протокол для передачи веб-страниц.
    - **HTTPS (HyperText Transfer Protocol Secure)**: Зашифрованная версия HTTP.
    - **FTP (File Transfer Protocol)**: Протокол для передачи файлов.
    - **SMTP (Simple Mail Transfer Protocol)**: Протокол для отправки электронной почты.
    - **IMAP (Internet Message Access Protocol)**: Протокол для получения почты с сервера.
    - **POP3 (Post Office Protocol 3)**: Другой протокол для получения почты.
    - **DNS (Domain Name System)**: Протокол для преобразования доменных имен в IP-адреса.
    - [[WebSocket]]: Протокол для двустороннего обмена данными в реальном времени между клиентом и сервером.
    - **MQTT (Message Queuing Telemetry Transport)**: Легковесный протокол для обмена сообщениями в IoT.
    - **XMPP (Extensible Messaging and Presence Protocol)**: Протокол для обмена сообщениями и статусов присутствия.
    - **SNMP (Simple Network Management Protocol)**: Протокол для управления и мониторинга сетевых устройств.
    - **RDP (Remote Desktop Protocol)**: Протокол для удаленного управления рабочим столом.

---

### Разделение по уровням:

- **Уровни 1-3 (Физический, Канальный и Сетевой)** — это **сетевые** и **канальные** протоколы, которые занимаются передачей и маршрутизацией данных.
- **Уровни 4-6 (Транспортный, Сеансовый и Представления)** — обеспечивают **управление передачей** данных, синхронизацию сеансов и преобразование данных.
- **Уровень 7 (Прикладной)** — это **протоколы для приложений**, которые решают задачи взаимодействия конечных пользователей и приложений с сетью.

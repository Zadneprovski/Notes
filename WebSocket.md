### Что такое WebSocket?

WebSocket — это протокол, обеспечивающий **постоянное соединение** между клиентом и сервером, позволяя обмениваться данными в **режиме реального времени** без необходимости повторных HTTP-запросов.

Если какая-то технология или инструмент работает только с WebSocket-серверами, это значит, что:

- Он **не может** работать с обычными HTTP/HTTPS-серверами (REST API, например).
- Он требует **WebSocket-соединения** (`ws://` или `wss://`), а не стандартных HTTP-запросов (`http://` или `https://`).
- Он рассчитан на **долговременное соединение** вместо одноразовых запросов.
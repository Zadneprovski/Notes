
```bash
#!/bin/bash

# Путь к вашему Python-скрипту
PYTHON_SCRIPT_PATH="/path/to/your_script.py"
SERVICE_NAME="video.service"

# Проверяем, существует ли указанный скрипт
if [ ! -f "$PYTHON_SCRIPT_PATH" ]; then
  echo "Python-скрипт $PYTHON_SCRIPT_PATH не найден!"
  exit 1
fi

# Создаем файл сервиса
SERVICE_FILE="/etc/systemd/system/$SERVICE_NAME"

sudo tee $SERVICE_FILE > /dev/null <<EOF
[Unit]
Description=Video Service
After=network.target

[Service]
ExecStart=/usr/bin/python3 $PYTHON_SCRIPT_PATH
Restart=always
User=root
Group=root

[Install]
WantedBy=multi-user.target
EOF

# Перезагружаем конфигурацию systemd
sudo systemctl daemon-reload

# Включаем и запускаем сервис
sudo systemctl enable $SERVICE_NAME
sudo systemctl start $SERVICE_NAME

# Проверяем статус сервиса
sudo systemctl status $SERVICE_NAME
```

Сохраните этот скрипт, например, как `setup_video_service.sh`, затем сделайте его исполняемым и запустите:
```bash
chmod +x setup_video_service.sh
sudo ./setup_video_service.sh
```
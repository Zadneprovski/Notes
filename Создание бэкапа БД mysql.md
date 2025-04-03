Создание резервной копии базы данных MySQL на сервере Ubuntu LTS можно автоматизировать с помощью утилиты `mysqldump`, скрипта резервного копирования и планировщика задач `cron`. Вот пошаговое руководство:

### Шаг 1: Установка необходимых инструментов

Убедитесь, что MySQL установлен и работает:

```bash
sudo systemctl status mysql
```


### Шаг 2: Создание скрипта для резервного копирования базы данных

Создайте скрипт, который будет выполнять резервное копирование базы данных MySQL. Например, создадим скрипт `backup_mysql.sh`:

```bash
sudo nano /usr/local/bin/backup_mysql.sh
```


Добавьте в него следующий код:

```bash
#!/bin/bash

# Параметры MySQL
DB_USER="your_db_user"
DB_PASSWORD="your_db_password"
DB_NAME="your_db_name"
BACKUP_DIR="/mnt/backup/mysql"
DATE=$(date +%F-%H-%M-%S)
LOGFILE="/var/log/backup_mysql.log"

# Создаем директорию для резервных копий, если она не существует
mkdir -p $BACKUP_DIR

# Выполняем резервное копирование базы данных
mysqldump -u $DB_USER -p$DB_PASSWORD $DB_NAME > $BACKUP_DIR/$DB_NAME-$DATE.sql

# Проверяем успешность выполнения резервного копирования
if [ $? -eq 0 ]; then
    echo "Backup of database $DB_NAME completed successfully at $DATE" >> $LOGFILE
else
    echo "Backup of database $DB_NAME failed at $DATE" >> $LOGFILE
fi
```



Не забудьте заменить `your_db_user`, `your_db_password` и `your_db_name` на соответствующие значения вашей базы данных MySQL.

Сделайте скрипт исполняемым:

```bash
sudo chmod +x /usr/local/bin/backup_mysql.sh
```

### Шаг 3: Настройка cron для автоматизации

Откройте редактор [[crontab]] для редактирования заданий:

```bash
sudo crontab -e
```

Добавьте следующую строку, чтобы настроить ежедневное выполнение скрипта в 2 часа ночи:

```bash
0 2 * * * /usr/local/bin/backup_mysql.sh
```

### Шаг 4: Проверка настроек

Убедитесь, что скрипт работает правильно, запустив его вручную:

```bash
sudo /usr/local/bin/backup_mysql.sh
```


Проверьте лог-файл, чтобы убедиться, что резервное копирование прошло успешно:

```bash
cat /var/log/backup_mysql.log
```

### Дополнительные настройки

- **Шифрование**: Рассмотрите возможность шифрования резервных копий для дополнительной безопасности.
- **Сжатие**: Добавьте сжатие резервных копий для экономии дискового пространства, изменив команду `mysqldump` следующим образом:

```bash
mysqldump -u $DB_USER -p$DB_PASSWORD $DB_NAME | gzip > $BACKUP_DIR/$DB_NAME-$DATE.sql.gz
```

- **Удаление старых резервных копий**: Добавьте команду для удаления старых резервных копий, чтобы предотвратить переполнение диска. Например, для удаления резервных копий старше 7 дней добавьте следующую строку в скрипт:

```bash
find $BACKUP_DIR/* -mtime +7 -exec rm {} \;
```
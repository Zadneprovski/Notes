
# Основные команды Bash

## Вывод на экран
```bash
echo options string
```

## Навигация по файловой системе:
- `pwd` - Показать текущий рабочий каталог.
  ```bash
  pwd
  ```
- `ls` - Список файлов и каталогов в текущем каталоге.
  ```bash
  ls
  ```
- `cd` - Сменить текущий каталог.
  ```bash
  cd /path/to/directory
  ```

## Работа с файлами и каталогами:
- `touch` - Создать пустой файл.
  ```bash
  touch filename.txt
  ```
- `mkdir` - Создать новый каталог.
  ```bash
  mkdir new_directory
  ```
- `rm` - Удалить файл.
  ```bash
  rm filename.txt
  ```
- `rmdir` - Удалить пустой каталог.
  ```bash
  rmdir empty_directory
  ```
- `rm -r` - Удалить каталог и его содержимое рекурсивно.
  ```bash
  rm -r directory
  ```
- `cp` - Копировать файлы или каталоги.
```bash
  cp source_file destination
```
- `mv` - Переместить или переименовать файлы или каталоги.
  ```bash
  mv old_name new_name
  ```

## Просмотр и редактирование файлов:
- `cat` - Вывести содержимое файла.
  ```bash
  cat filename.txt
  ```
- `less` - Просмотр содержимого файла с возможностью прокрутки.
  ```bash
  less filename.txt
  ```
- `nano` или `vi` - Текстовые редакторы для редактирования файлов.
  ```bash
  nano filename.txt
  # или
  vi filename.txt
  ```

## Поиск и фильтрация:
- `grep` - Поиск строки в файле.
  ```bash
  grep 'search_term' filename.txt
  ```
- `find` - Поиск файлов и каталогов.
  ```bash
  find /path/to/search -name 'filename'
  ```

## Работа с процессами:
- `ps` - Показать список процессов.
  ```bash
  ps aux
  ```
- `top` - Показать текущие запущенные процессы и их использование ресурсов.
  ```bash
  top
  ```
- `kill` - Завершить процесс по идентификатору (PID).
  ```bash
  kill PID
  ```

## Управление пользователями и правами:
- [[chmod]] - Изменить права доступа к файлу или каталогу.
  ```bash
  chmod 755 filename
  ```
- `chown` - Изменить владельца файла или каталога.
  ```bash
  chown user:group filename
  ```

## Сетевые команды:
- `ping` - Проверить доступность хоста.
  ```bash
  ping example.com
  ```
- `ifconfig` - Показать конфигурацию сетевых интерфейсов (или `ip a` для более новых систем).
  ```bash
  ifconfig
  # или
  ip a
  ```

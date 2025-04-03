```bash
#!/bin/bash

# Определяем начальную директорию скрипта
SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

DIRECTORY="$SCRIPT_DIR"

while getopts ":p:" pathArg; do
  case ${pathArg} in
    p )
      DIRECTORY=$OPTARG
      ;;
    \? )
      echo "Некорректный аргумент: $OPTARG" 1>&2
	  exit 1
      ;;
    : )
	# Если аргумент не передан, используем папку, где расположен скрипт
	DIRECTORY="$SCRIPT_DIR"
      ;;
  esac
done
shift $((OPTIND -1))# Сдвигаем аргументы, чтобы оставшиеся были доступны в $@

# Проверяем, существует ли либа в данном каталоге
if [ ! -d "$DIRECTORY" ]; then
    echo "$DIRECTORY не найдена."
    exit 1
fi

# Переходим в указанную папку
echo "Переход в  $DIRECTORY."
cd "$DIRECTORY" || { echo "Ошибка: Невозможно перейти в директорию $DIRECTORY"; exit 1; }

# Перечисление названий всех подпапок
libraries=("uc22base" "RgBase" "RadioLink" "RgControls" "ModelLib" "RgSensors", "FreeRTOS")

declare -A libraryStatus

# Перебираем каждую подпапку и выполняем git pull
for library in "${libraries[@]}"
do
    if [ -d "$library" ]; then
		echo "Обновление библиотеки $library..."
		cd $library
		# Проверяем, является ли папка git репозиторием
		if git rev-parse --git-dir > /dev/null 2>&1; then
			git pull
			# Получаем имя текущей ветки
			branch=$(git rev-parse --abbrev-ref HEAD)
            libraryStatus[$library]="$branch"
		else
            libraryStatus[$library]="не является git репозиторием"
		fi
		cd ../
		else
            libraryStatus[$library]="не найдено"
    fi
done

# Выводим итоговый статус всех библиотек
echo "Статус библиотек:"
for library in "${libraries[@]}"
do
    echo "$library: ${libraryStatus[$library]}"
done

echo "Все библиотеки обновлены."
```
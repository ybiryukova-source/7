# Домашнє завдання 6

# Завдання 1
# Створення та відкриття файлу для редагування 
ubuntu@ubuntu:~$ touch backup.sh
ubuntu@ubuntu:~$ nano backup.sh

#Реалізація перевірки кількості аргументів
Додала код:
if [ $# -ne 2 ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

#Реалізація перевірки каталогів
Додала код:
LOG_DIR="$1"
BACKUP_DIR="$2"

if [ ! -d "$LOG_DIR" ] || [ ! -d "$BACKUP_DIR" ]; then
    echo "Usage: ./backup.sh <log_dir> <backup_dir>"
    exit 1
fi

#Перевірка:
#Запуск без аргументів
ubuntu@ubuntu:~/homework/module2$ ./backup.sh
Usage: ./backup.sh <log_dir> <backup_dir>

#Запуск з неіснуючим каталогом:
ubuntu@ubuntu:~/homework/module2$ ./backup.sh test backups
Usage: ./backup.sh <log_dir> <backup_dir>

# Завдання 2
#Додавання lock-файлу
ubuntu@ubuntu:~$ nano backup.sh
У скрипт додаю:

LOCK_FILE="/tmp/backup.lock"

if [ -f "$LOCK_FILE" ]; then
    echo "Backup already running"
    exit 1
fi
touch "$LOCK_FILE"

#Автоматичне видалення lock-файлу
Додаю:
trap "rm -f $LOCK_FILE" EXIT

#Перевірка:
Створюю lock-файл вручну:
ubuntu@ubuntu:~/homework/module2$ mkdir logs backups
ubuntu@ubuntu:~/homework/module2$ ./backup.sh logs backups

Запускаю скрипт:
ubuntu@ubuntu:~/homework/module2$ ./backup.sh logs backups
Backup already running

# Завдання 3
#Створення тестових логів
ubuntu@ubuntu:~/homework/module2$ echo "Error 1" > logs/app.log
ubuntu@ubuntu:~/homework/module2$ echo "Error 2" > logs/system.log
ubuntu@ubuntu:~/homework/module2$ ls logs
app.log  system.log

#Формування імені архіву
ubuntu@ubuntu:~$ nano backup.sh
Додала:
DATE=$(date +"%Y-%m-%d_%H-%M")

ARCHIVE_NAME="logs_backup_${DATE}.tar.gz"
ARCHIVE_PATH="${BACKUP_DIR}/${ARCHIVE_NAME}"

#Створення архіву
Додала:
tar -czf "$ARCHIVE_PATH" -C "$LOG_DIR" .

#Перевірка роботи
ubuntu@ubuntu:~/homework/module2$ ./backup.sh logs backups
ubuntu@ubuntu:~/homework/module2$ ls backups
logs_backup_2026-06-11_05-31.tar.gz

# Завдання 4
#Перевірка коду завершення архівації
Додала:
ubuntu@ubuntu:~$ nano backup.sh 
if [ $? -ne 0 ]; then
    echo "Backup failed"
    exit 2
fi

#Повідомлення про успішне завершення
Додала:
echo "Backup created: $ARCHIVE_PATH"

#Запуск:
ubuntu@ubuntu:~/homework/module2$ rm -f /tmp/backup.lock
ubuntu@ubuntu:~/homework/module2$ ./backup.sh logs backups
Backup created: backups/logs_backup_2026-06-11_05-42.tar.gz





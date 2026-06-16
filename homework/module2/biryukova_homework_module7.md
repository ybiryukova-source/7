# Домашнє завдання 7
# Запуск застосунку
ubuntu@ubuntu:~$ docker run -d \
--name my-http-server \
-p 8080:8080 \
python:3.11-slim \
python -m http.server 8080

Перевірка контейнера:
ubuntu@ubuntu:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  STATUS
7d2a31f5a7c1   python:3.11-slim   "python -m http.ser…"   Up 20 seconds

# Аналіз процесу
ubuntu@ubuntu:~$ docker exec -it my-http-server sh
ubuntu@ubuntu:~$ ps aux
PID USER     TIME COMMAND
1 root      0:00 python -m http.server 8080
15 root      0:00 sh
22 root      0:00 ps aux
Процес python -m http.server 8080 має PID 1. Отже, він є головним процесом контейнера. Docker запускає процес який вказаний команді запуску, цей процес стає PID 1 і визначає життєвий цикл контейнера. Поки працює процес Python HTTP Server контейнер існує, а якщо процес завершиться контейнер також завершиться

# Завершення контейнера
ubuntu@ubuntu:~$ docker stop my-http-server
my-http-server

Перевірка після завершення:
ubuntu@ubuntu:~$ docker ps -a
CONTAINER ID   IMAGE              STATUS
7d2a31f5a7c1   python:3.11-slim   Exited (0) 5 seconds ago

Під час виконання команди docker stop my-http-server, Docker надсилає головному процесу контейнера сигнал SIGTERM. Після цього Docker очікує приблизно 10 секунд. Процес завершився коректно, а контейнер зупинився
Якщо процес не завершується після отримання SIGTERM, Docker надсилає SIGKILL. Після отримання SIGKILL процес примусово завершується ядром. У такому випадку застосунок не має можливості виконати коректне завершення роботи чи зберегти дані

# Аналіз логів
ubuntu@ubuntu:~$ docker logs my-http-server
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/)
172.17.0.1 - - [16/Jun/2026 12:30:14] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [16/Jun/2026 12:30:20] "GET / HTTP/1.1" 200 -

Логи надходять з stdout і stderr. Docker автоматично підключається до цих потоків через файлові дескриптори: fd 1 -> stdout, fd 2 -> stderr і зберігає їх через logging driver.



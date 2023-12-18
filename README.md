# Задание 1
## Задание: Пользуясь терминалом на компьютере А перенести файл с компьютера B на компьютер C, находящиеся в одной локальной сети. Для выполнения задачи будем использовать три ноутбука с операционной системой Linux Ubuntu, установленной через VirtualBox.

Для переноса файла с компьютера B на компьютер С через компьютер А, будем использовать команду SCP (Secure Copy Protocol), пользуясь SSH сервером. Все компьютеры в момент работы подключены к одной локальной сети.
Для начала на всех компьютерах в настройках VirtualBox добавим новый адаптер сети.

![image](https://github.com/y0rikkk/ITMO/assets/113039134/a0aa2979-c23e-410e-a728-dba1c75acbfa)



Установим и настроим на компьютере B сервер OpenSSH.
Откроем терминал на компьютере А и введём команду: ssh username@ip_address_B. Здесь username - имя пользователя на компьютере B, ip_address_B - IP-адрес компьютера B.
На компьютере B введем пароль пользователя. 

![image](https://github.com/y0rikkk/ITMO/assets/113039134/2113a37e-7c18-4c51-b2e5-e27d3db3fa84)


IP адреса компьютеров B и С узнаем с помощью команды ip -c addr

![image](https://github.com/y0rikkk/ITMO/assets/113039134/0276c809-41a2-4dfe-8100-9216cc838493)
Нас интересует строка enp0s9 - это подключение через сетевой мост, настроенный ранее.
Посмотрим статус запущенного SSH сервера на компьютере Б. 

![image](https://github.com/y0rikkk/ITMO/assets/113039134/fbd2f618-49bd-4310-b698-3da20cb37a88)


На компьютере Б создадим текстовый файл file.txt 

![image](https://github.com/y0rikkk/ITMO/assets/113039134/89cc13de-ad40-478f-9b35-d93c7aecc74f)


После успешного подключения к компьютеру Б, используем команду SCP для копирования файла с компьютера B на компьютер С. Чтобы скопировать файл file.txt с компьютера Б в директорию /home/user/ на компьютере С, введём следующую команду: scp /path/to/file.txt username@ip_address_of_C:/home/user/. Здесь /path/to/file.txt - путь к файлу на компьютере B, ip_address_C - IP-адрес компьютера С, username - имя пользователя на компьютере С.

![image](https://github.com/y0rikkk/ITMO/assets/113039134/4f71133a-3237-4d44-9950-cf7b1b40eb48)

Осталось ввести пароль пользователя на компьютере С и после успешного выполнения команды, файл будет скопирован с компьютера B на компьютер С.

# Задание 2
## Задание: Написать два Dockerfile – плохой и хороший. Плохой должен запускаться и работать корректно, но в нём должно быть не менее 3 “bad practices”. В хорошем Dockerfile они должны быть исправлены. В Readme описать все плохие практики из кода Dockerfile и почему они плохие, как они были исправлены в хорошем  Dockerfile, а также две плохие практики по использованию этого контейнера.

Создадим директорию app с двумя Dockerfile и текстовым файлом sample.txt с текстом "hello world".

Напишем плохой Dockerfile.bad:

**
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y \
    vim

USER root

COPY sample.txt /root/sample.txt

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*
**


Теперь напишем хороший Dockerfile.good:

FROM ubuntu:20.04

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    vim && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

USER nobody

WORKDIR /app

COPY sample.txt /app/sample.txt

Соберём оба контейнера:

![image](https://github.com/y0rikkk/ITMO/assets/113039134/958523e3-8a36-4665-bea7-0c0061d1dd37)

Теперь узнаем id этих контейнеров и посмотрим на их размер:

![image](https://github.com/y0rikkk/ITMO/assets/113039134/9bf8e7ed-ffde-4d5c-a0fa-f3affdddbc54)

Как мы видим, размер хорошего конетейнера меньше на 43 Мб.

Убедимся, что наш файл успешно скопировался в оба контейнера:

![image](https://github.com/y0rikkk/ITMO/assets/113039134/47e70865-4e91-4392-824f-28ccf728e86d)

Рассмотрим плохие практики и их исправление:

• В плохом Dockerfile используется тег latest, предпочтительнее использовать конкретную версию, что мы и делаем в хорошем Dockerfile
• В плохом Dockerfile идёт установка всех зависимостей в одном слое, а в хорошем Dockerfile мы разделяем установку зависимостей по слоям
• В плохом Dockerfile мы не используем WORKDIR, а в хорошем используем
• В плохом Dockerfile мы используем root пользователя, а в хорошем нет

Ещё плохие практики по использованию этого контейнера:

• Копирование файлов без указания владельца и прав. Такая практка может доставить недобства с правами доступа в будущем
• Установка лишних завимостей, например, установка vim, когда он не использутся



   
# Задание 3
## Задание: сделать, чтобы после пуша в ваш репозиторий автоматически собирался докер образ и результат его сборки сохранялся куда-нибудь.
Для начала создадим в папке .github папку workflows с файлом расширения yml. В файле записан код с инструкциями для github actions. 

![image](https://github.com/aivanova2472/clouds/assets/112978016/d7a0e863-4ca4-44d7-93f6-3c8789571eee)

Этот код срабатывает при каждом пуше в опреденную папку вети main (в нашем случае мы создали папку pushhere специально для этой цели). Также мы получаем секрет с токеном, который впоследствии передается дальше в Dockerfile.

Создадим Dockerfile, который будет создавать новый текстовый документ при каждем пуше в ветку main репозитория в папку cloud/lab3/pushhere/ и загружать его в корневую папку репозитория. Для получения прав доступа используем секрет из docker-build.yml

![image](https://github.com/aivanova2472/clouds/assets/112978016/ec6eedcd-fd13-46cc-b562-aeae590dd26d)

Теперь при каждом пуше в ветку main автоматически проверяется наличие изменений в папке pushhere, и если они есть, создается текстовый документ hello.txt с текстом "hello". Каждый раз при запуске нашего workflow мы можем отследить прогресс работы во вкладке actions нашего репозитория

<img width="707" alt="image" src="https://github.com/y0rikkk/ITMO/assets/113039134/a75c6b22-d483-4feb-aa12-631a484f1732">

Конкретна эта работа была вызвана следующим коммитом в нужную папку:

<img width="893" alt="image" src="https://github.com/y0rikkk/ITMO/assets/113039134/157fe7a8-2327-47d7-89fa-b0c080d16797">

Причём следующий коммит (add hello.txt) не запускает workflow, так как изменений в папке pushhere не происходит. Это сделано для того, чтобы образ не собирался по кругу бесконечно.

# Домашнее задание к занятию 5. «Практическое применение Docker»

### Грибанов Антон. FOPS-31

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_001.png)


---

## Задача 1
1. Сделайте в своем GitHub пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python).

2. Создайте файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Используйте базовый образ ```python:3.12-slim```
   - Обязательно используйте конструкцию ```COPY . .``` в Dockerfile
   - Создайте `.dockerignore` файл для исключения ненужных файлов
   - Используйте ```CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]``` для запуска
   - Протестируйте корректность сборки 
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker, с помощью venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) Изучите код приложения и добавьте управление названием таблицы через ENV переменную.

#### Создаю Dockerfile.python

```bash
FROM python:3.12-slim

ENV DB_HOST=172.20.0.10
ENV DB_USER=${MYSQL_USER}
ENV DB_NAME=${MYSQL_DATABASE}
ENV DB_PASSWORD=${MYSQL_PASSWORD}
ENV DB_TABLE=requests

WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY main.py ./
CMD ["python", "main.py"]

# Запускаем приложение с помощью uvicorn, делая его доступным по сети
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"] 
```

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_002.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_003.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_004.png)


---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_005.png)

2. Настройте аутентификацию вашего локального docker в yandex container registry.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_006.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_007.png)

3. Соберите и залейте в него образ с python приложением из задания №1.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_008.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_009.png)

4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_010.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_011.png)

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

```bash
include:
  - proxy.yaml

services:
 web:
    build:
      context: ./                   # путь до докерфайла
      dockerfile: Dockerfile.python # имя докерфайла
    container_name: web             # имя контейнера
    ports:                          # проброс портов
      - '5000:5000'                 # номер порта
    restart: always                 # перезапуск контейнера
    env_file: .env                  # файл с переменными
    environment:                    # блок переменных
      - TZ=Europe/Moscow            # установка часового пояса МСК
      - DB_HOST=db
      - DB_USER=${MYSQL_USER}
      - DB_PASSWORD=${MYSQL_PASSWORD}
      - DB_NAME=${MYSQL_DATABASE}
      - DB_TABLE=requests
    networks:
      backend:                      # добавить в сеть backend
       ipv4_address: 172.20.0.5     # статический IPv4

 db:
    image: mysql:8   # версия снимка
    container_name: db # имя контейнера
    ports:
      - '3306:3306'
    restart: always
    env_file: .env
    volumes:           # том и проброс файла в директории
      - ./mysql/my.conf:/etc/mysql/my.cnf:ro
      - mysql_data:/var/lib/mysql
    environment:
      # Все параметры описываем в файле .env в папке проекта
      - TZ=Europe/Moscow
      - MYSQL_ROOT_HOST="%"
      - MYSQL_ROOT_PASSWORD:${MYSQL_ROOT_PASSWORD}
      - MYSQL_USER:${MYSQL_USER}
      - MYSQL_PASSWORD:${MYSQL_PASSWORD}
      - MYSQL_DATABASE:${MYSQL_DATABASE}
      - MYSQL_TABLE:requests
    networks:
      backend:
        ipv4_address: 172.20.0.10
volumes:
  mysql_data: {}
```


2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете информационную ошибку --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_012.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_015.png)

3. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;```.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_013.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_014.png)

4. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_016.png)

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_017.png)

2. Подключитесь к Вм по ssh и установите docker.

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_018.png)

3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.

```bash
#!/bin/bash
echo "Cloning the project from GitHub"
  git clone https://github.com/Qshar1408/shvirtd-example-python.git
echo "Done"

echo "Entering the project directory"
  cd shvirtd-example-python
echo "Done"

echo "Creating docker containers: db, app, reverse-proxy and ingress-proxy"
  sudo docker compose up -d
echo "Done"

echo "List of containers"
  sudo docker ps
```

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_019.png)

4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. Трафик должен пройти через цепочки: Пользователь → Internet → Nginx → HAProxy → FastAPI(запись в БД) → HAProxy → Nginx → Internet → Пользователь

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_020.png)
   
## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_021.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_022.png)

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_023.png)

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

![virtd_04](https://github.com/Qshar1408/virtd_04/blob/main/img/virtd_024.png)


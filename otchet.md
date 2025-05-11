## Part 1. Готовый докер

**Скачивание образ nginx при помощи `sudo docker pull nginx`**

![1](screen/p1%201.jpeg)

**Проверка командой `sudo docker images`**

![2](screen/p1%202.jpeg)

**Запуск докер-образ nginx через `sudo docker run -d [image id]`**

![3](screen/p1%203.jpeg)

**Проверка, что образ запустился командой `sudo docker ps`**

![4](screen/p1%204.jpeg)

**Просмотр информации о контейнере с помощью команды `sudo docker inspect [container id]`**

![5](screen/p1%205.jpeg)

**Определение размера контейнера с помощью команды `sudo docker inspect [container id] --size | grep Size`**

![6](screen/p1%2055.jpeg)

**Определение списка замапленных портов с помощью команды `sudo docker inspect [container id] --size | grep -A 1 Port`**

![7](screen/p1size.jpeg)

**Определение ip контейнера с помощью команды `sudo docker inspect [container id] --size | grep IPAddress`**

![8](screen/p1addr.jpeg)

+ размер контейнера 192005337
+ список замапленных портов "80/tcp":null
+ ip контейнера "127.17.0.2"

**Остановка контейнера командой `sudo docker stop [container id]` и проверка остановился ли контейнер командой`sudo docker stop ps`**

![9](screen/p16.jpeg)

**Запуск контейнера с портами 80 и 443 в контейнере, замапленными на такие же порты на локальной машине командой`sudo docker run -d -p 80:80 -p 443:443 nginx` и проверка запущенных контейнеров `sudo docker ps`**

![10](screen/p17.jpeg)

**Проверка, что в браузере по адресу localhost:80 доступна стартовая страница nginx**

![12](screen/p19.jpeg)


**Перезапуск докер контейнера командой `sudo docker restart [container id]` и проверка что он запустился  командой `sudo docker ps`**

![11](screen/p18.jpeg)

## Part 2. Операции с контейнером

**Чтобы прочитать конфигурационный файл nginx.conf внутри Docker контейнера через команду exec, использовала `sudo docker exec [container id] cat /etc/nginx/nginx.conf`**

![13](screen/p2%201.jpeg)

**Создание на локальной машине файл nginx.conf и настраиваем в нем отдачу страницы статуса сервера nginx по пути /status
Просмотр содержимого с помощью `cat nginx.conf`**

![14](screen/p2%202.jpeg)

**Скопировала созданный файл nginx.conf внутрь докер-образа через команду `docker cp nginx.conf [container id]:/etc/nginx` и проверила,  что файл скопировался командой `sudo docker exec [container id] cat /etc/nginx/nginx.conf`**

![15](screen/p2%203.jpeg)

**Перезапуск nginx внутри докер-образа через команду `sudo docker exec [container id] nginx -s reload`**


![17](screen/p2%205.jpeg)

**Проверка, что по адресу localhost:80/status выдается страничка со статусом сервера nginx**
![16](screen/p2%204.jpeg)

**Экспорт контейнера в архив container.tar командой `sudo docker export [container id] > container.tar`**

![18](screen/part2%201.jpeg)

**Удаление образа через`docker rmi -f nginx`**

![19](screen/part2%202.jpeg)

**Импорт контейнера обратно командой `docker import -c 'cmd ["nginx", "-g", "daemon off;"]' ./container.tar nginx:import`**

![20](screen/part2%203.jpeg)

**Запуск импортированного контейнера через `sudo docker run -d -p 80:80 -p 443:443 nginx` и  проверка запуска `sudo docker ps`**

![21](screen/part2%204.jpeg)

**Проверка, что по адресу localhost:80/status отдается страничка со статусом сервера nginx**

![22](screen/part2%205.jpeg)

## Part 3. Мини веб-сервер


**Изменила nginx.conf, который будет проксировать все запросы с 81 порта на 127.0.0.1:8080**

![26](screen/part3%204.jpeg)

**Проверяю и запускаю конфигруационный файл nginx.conf командами `nginx -t -c $(pwd)/nginx.conf` и `nginx -c $(pwd)/nginx.conf`**

![23](screen/p3%201.jpeg)

**Запуск FastCGI-сервера в фоновом режиме на порту 8080 командой `spawn-fcgi -p 8080 ./server`**

![24](screen/p3%202.jpeg)


**Написала мини сервер на C и FastCgi, который будет возвращать простейшую страничку с надписью Hello World!:**

![25](screen/p3%203.jpeg)


**Проверка, что в браузере по localhost:81 отдается написанная страничка с hello world**

![27](screen/p3%204.jpeg)

## Part 4. Свой докер
**Собрала написанный докер образ через docker build при этом указав имя и тег`docker build -t part4:v1 .`**

![28](screen/p4%201.jpeg)

**Проверка через docker images, что все собралось корректно командой`sudo docker images`**

![29](screen/p4%202.jpeg)

**Запустила собранный докер-образ с маппингом 81 порта на 80 на локальной машине и маппингом папки ./nginx внутрь контейнера по адресу, где лежат конфигурационные файлы nginx'а командой `sudo docker run -it -p 80:81 -v "$(pwd)/nginx.conf:/etc/nginx/nginx.conf [image id] bash`  и  проверка, что по localhost:80 доступна страничка написанного мини сервера с помощью утилиты `curl`**

![30](screen/p4%203.jpeg)

**Изменение раздела server в файле nginx.conf**

![31](screen/p4%205.jpeg)

**Перезапуск контейнера командой `docker restart [container id]` и проверка, что по localhost:80/status доступна страничка написанного мини сервера с помощью утилиты `curl`**

![32](screen/p4%204.jpeg)


## Part 5. Dockle


**Установка Dockle:**

![33](screen/p5%201.jpeg)

**Сканирование образа из предыдущего задания через `sudo dockle -ak NGINX_GRGKEY -ak NGINX_GRGKEY_PATH -ak NGINX_GRGKEYS part4:v1`**

![34](screen/p5%202.jpeg)

**Заново собираю исправленный образ  с помощью команды `sudo docker build -t burtonma:part5` и проверяю командой `sudo docker images`**

![35](screen/p5%203.jpeg)

**Проверка, что при проверке dockle исправленного образа нет ошибок и предупреждений через `sudo dockle burtonma:part5`**

![36](screen/p5%204.jpeg)

## Part6. Базовый Docker Compose

**файл docker-compose.yml**

![38](screen/p6%202.jpeg)

**Подняла командами `sudo docker-compose up -d part6` и `sudo docker-compose up -d proxy`**

![37](screen/p6%201.jpeg)


**Остановка всех запущенных контейнеров командой `sudo docker-compose down`**

**Собрала и запустила проект с командой `sudo docker-compose up --build -d`**

![39](screen/p6%203.jpeg)

**Проверка, что в браузере по localhost:80 отдается написанная страничка, как и ранее с помощью команд `curl http://localhost:80` и `curl http://localhost:80/status`**

![40](screen/p6%204.jpeg)

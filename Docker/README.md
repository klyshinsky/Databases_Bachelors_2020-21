## Тестовое приложение для Docker-контейнера

Смотрим на содержимое Dockerfile.
```
# На основе какого образа будет создаваться данный.
FROM python:3.10.7-alpine3.16
# Что надо сделать для инициализации образа.
RUN apk update && apk upgrade && pip install flask
# Копируем необходимый код и данные внутрь образа.
COPY src /my_app
# Какая команда будет выполняться при каждом запуске контейнера.
CMD cd /my_app && python app.py
#CMD tail | ls /my_app
# Показывать наружу порт номер 5000 контейнера.
EXPOSE 5000
```

Переходим в каталог с данными. Выполняем команду сборки образа:  
`sudo docker build --tag my_image .`  
Ключ `--tag my_image` говорит, что образ надо назвать `my_image`, точка обозначает, что файл с именем Dockerfile надо искать в текущем каталоге.

Смотрим список образов, убеждаемся, что появился образ:  
`sudo docker images`

Собираем контейнер из образа, запускаем контейнер:  
`sudo docker run -d -p 5001:5000 --name my_app my_image`  
`-d` означает, что надо запустить контейнер и отключиться от его консоли.  
`-p 5001:5000` означает, что порт контейнера 5000 надо подключить к порту 5001 локальной машины.  
`--name my_app` означает, что контейнер должен называться my_app, в противном случае docker присвоит ему случайное имя.  
Используется образ с именем my_image. Вместо имени можно использовать идентификатор (или его первые цифры) образа.

Убеждаемся, что контейнер запущен:  
`sudo docker ps`

В браузере идем по адресу `127.0.0.1:5001`, видим, что приложение работает.

Теперь хочется, чтобы контейнер брал данные с нашего жесткого диска, для этого связываем каталог на диске 
(относительные пути не поддерживаются, не забудьте заменить на путь на своем диске!!!)  
`/home/edward/papers/kourses/Advanced Python/Linguists/Databases/Docker/data/`  
с каталогом внутри контейнера  
`/my_app/templates`

Собираем контейнер из образа с учетом связи, запускаем контейнер:  
`sudo docker run -d -p 5002:5000 -v '/home/edward/papers/kourses/Advanced Python/Linguists/Databases/Docker/data':/my_app/templates --name my_app2 my_image`  
Ключ `-v` связывает каталоги между собой. Правильнее ыло бы создать тома (volume), но не сейчас.

В браузере идем по адресу `127.0.0.1:5002`, видим, что приложение работает и отдает данные с диска. Можно поменять данные,
перезагрузить контейнер при помощи  
`sudo docker stop my_app2`  
`sudo docker start my_app2`  
или  
`sudo docker restart my_app2`  

Останавливаем контейнеры, удаляем при помощи  
`sudo docker rm my_app`  
удаляем образ при помощи  
`sudo docker rmi my_image`  
убеждаемся, что приложение не работает.

### Что почитать

[Хабр](https://habr.com/ru/company/ruvds/blog/438796/), [ещё Хабр](https://habr.com/ru/post/659049/), 
[документация](https://docs.docker.com/engine/reference/commandline/run/).

0.0 Ставим Docker:
```
curl -sSL https://get.docker.com/ | sh # ставим
for us in $(ls /home/); do usermod -aG docker ${us}; done
```

0.1 Удалить docker images
```
$images = docker images -a -q
foreach ($image in $images) { docker image rm $image -f }
    # удалить все образы ч/з PowerShell
    
$images = docker images -q -f "dangling=true"
foreach ($image in $images) { docker image rm $image -f }
    # удалить только не используемые образы ч/з PowerShell
    
foreach ($image in $images) { echo $image }
```

1.0 Создаем Dockerfile:

```
FROM bigdatateam/spark-python2

USER root
WORKDIR /root
```

2.0 build
```
docker build -t lesson01 .

docker build -t lesson01 . --no-cache  
    # не учитываем cache
    
docker inspect lesson01
    # инспекция dockerfile
```

3.0 run в интерактивном режиме
```
docker run -it lesson01

docker run -it -v ${PWD}/dir:/dir -v ${PWD}/ro:/root/ro:ro lesson01
    # пробрасываем папку в контейнер
    # /root/ro:ro // :ro - доступна только на чтение

docker ps
docker cp competent_euclid:/test.txt test222.txt
    # копирование файла из контейнера на диск

docker run --rm -it velkerr/my_jupyter
```

4.0 run без интерактивного режима, покдлючение к контейнеру
```
docker run lesson01
docker ps
docker exec -it gallant_nightingale /bin/bash
    # /bin/bash открыли консоль bash в контейнере
```



5.0 Проброс портов
Добавляем в Dockerfile `EXPOSE 10000`
```
docker run --rm -it -v ${PWD}/dir:/dir -v ${PWD}/ro:/root/ro:ro -p 20000:10000 lesson01
    # http://127.0.0.1:20000 // станет доступен в браузере
```
6.0 Entrypoints - команды, кот. выполняются при запуске (после сборки) контейнера.

7.0 Итоговый Dockerfile
```
FROM ubuntu:18.04

RUN apt update && \
    echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections && \
    apt install git mc python3-dev python3-pip -y && \
    pip3 install jupyter
ADD my_file.txt /root/

ENV usual "noroot"
RUN useradd -m -d /home/${usual} ${usual} && \
    chown -R ${usual} /home/${usual} && \
    chmod -R 755 /home/${usual} && \
    chsh -s /bin/bash ${usual}
ADD entrypoint.sh /home/${usual}
RUN chown ${usual} /home/${usual}/entrypoint.sh && chmod +x /home/${usual}/entrypoint.sh

USER ${usual}
WORKDIR /home/${usual}
RUN echo ${USER} && whoami && pwd

ENTRYPOINT ["/home/noroot/entrypoint.sh"]
EXPOSE 10000

```

Файл `entrypoint.sh`:

```bash
#! /usr/bin/env bash

jupyter notebook --ip 0.0.0.0 --port 10000 --no-browser
```

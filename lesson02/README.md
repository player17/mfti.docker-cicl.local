http://gitlab.local/
http://gitlab.local.de/
https://gitlab.local/
https://gitlab.local.de/
### Старт проекта через docker-compose.yml
> `docker-compose up -d`

### Проброшенные папки
\\wsl$\docker-desktop-data\data\docker\volumes\

### Локальные паки через WSL
cd /mnt/d

### CI/CD and Runner on Window
#### CI/CD start or install on Windows
https://docs.gitlab.com/ee/install/docker.html
https://www.youtube.com/watch?v=MEUD4SsMqOs
docker run --name gitlab --detach \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --hostname gitlab.local \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --restart always \
  --volume gitlab-config:/etc/gitlab \
  --volume gitlab-logs:/var/log/gitlab \
  --volume gitlab-data:/var/opt/gitlab \
  --shm-size 256m gitlab/gitlab-ee:latest

### Пароль админка
root
`docker ps`
`docker exec -it lesson02-gitlab-1 grep 'Password:' /etc/gitlab/initial_root_password`

#### Runner install on Window 
https://www.youtube.com/watch?v=KzjnZSOm_Uo
https://docs.gitlab.com/runner/install/windows.html
https://www.youtube.com/watch?v=jAIhhULc7YA
https://stackoverflow.com/questions/44458410/gitlab-ci-runner-ignore-self-signed-certificate
- https://www.youtube.com/watch?v=jAIhhULc7YA
  - https://github.com/RomNero/YouTube-Infos/blob/main/03-GitLabCICD.md

> `https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe`
>> `переименовать в c:\GitLab-Runner\gitlab-runne.exe `

> `cmd от administrator`
>> `cd c:\GitLab-Runner\`
>>> `gitlab-runner.exe register`
>>> `gitlab-runner.exe register --tls-ca-file="c:\GitLab-Runner\sert.pem"` // регистрация со своим сертификатом (серт выгрузить с сайта)
>>>> `wsl openssl x509 -in sert.cer -text`
>>>> `wsl openssl x509 -outform der -in sert.cer -out sert.pem`
>>>> `https://www.leaderssl.ru/tools/ssl_converter` // конвертация онлайн сертификатов, если не будет рабоать правильно
>>>>> `... Enter an executor: Shell`

> `gitlab-runner.exe install`
>> `gitlab-runner.exe start`
>> `gitlab-runner.exe restart`
>>> `Win+R services.msc - найти проверить`
>>>> `gitlab-runner.exe stop`
>>>>> `gitlab-runner.exe uninstall`
>>>>> `sc delete gitlab-runner`
>>>>>> `Папка с билдами Runner C:\GitLab-Runner\builds\`

### Config config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "win"
  url = "http://gitlab.local/"
  token = "C2y12c8978798jvdpQ8AF"
  executor = "shell"
  shell = "pwsh"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]

#### Создать сеть между контейнерами
- `sudo docker network create gitlab-network`
- `sudo docker network connect gitlab-network gitlab`
- `sudo docker network connect gitlab-network gitlab-runner`

### Пример docker-compose.yml для  CI/CD
- `http://snakeproject.ru/rubric/article.php?art=gitlab_docker_03_02_2022`
- `https://github.com/ElisDN/demo-project-manager/`
- `docker-compose up -d`
- `docker-compose restart`

### Переменные окружения env CI/CD
> `script: 'ls env:' // Все переменные с префиксом CI_`

### Локальный Dokerhub (Container registry)
- `https://www.youtube.com/watch?v=G-WmX1um5cc`
- `https://www.youtube.com/watch?v=n_21ya2MoKg`
- `docker ps`
- `docker exec -it lesson02-gitlab-1 /bin/bash`  --> `cd etc/gitlab/` // Конфигируруем (см.видео), можно также через проброшеные volume \\wsl$\docker-desktop-data\data\docker\volumes\gitlab-config`
- `gitlab.rb` // Конфигурирование GitLab
  - `https://docs.gitlab.com/omnibus/settings/ssl.html#lets-encrypt-integration`
  - `external_url 'https://registry.gitlab.local' ... и другие параметры`
  - `docker exec -it lesson02-gitlab-1 /bin/bash`
    - `gitlab-ctl reconfigure`
    - `gitlab-ctl renew-le-certs`
    - `https://www.youtube.com/watch?v=Zx1MF5fDjzc` // установка сертификата в центр дов.источников
 
### Настройка локальных сертифкатов
- https://habr.com/ru/company/globalsign/blog/435476/
- https://www.youtube.com/watch?v=DyfyYRst3bg
  - 1. openssl genrsa -des3 -out server.key.secure 2048
  - 2. openssl rsa -in server.key.secure -out server.key
  - 3. openssl req -new -key server.key -out server.csr
  - 4. openssl x509 -req -days 9365 -in server.csr -signkey server.key -out server.crt

- https://www.youtube.com/watch?v=jAIhhULc7YA&t=2129s

02 в
03-26 - прогон тестов на контейнере из репо gitlab, необходимо ранер настроить на докер и его сконфигурировать
https://www.youtube.com/watch?v=jAIhhULc7YA&t=1691s



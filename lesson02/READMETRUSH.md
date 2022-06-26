http://gitlab.local/
`docker exec -it lesson02-gitlab-1 grep 'Password:' /etc/gitlab/initial_root_password`
https://gitlab.local/
### Старт проекта через docker-compose.yml локально без ssl
> `docker-compose up -d`
> `docker-compose restart`
> `docker-compose up -d --no-deps --build`

> `docker exec -it lesson02-gitlab-1 /bin/bash`
> `gitlab-ctl reconfigure`

> `docker exec -it lesson02-gitlab-runner-1 /bin/bash`
>  `echo '172.28.112.1 gitlab.local' >> /etc/hosts`
>  `cat /etc/hosts`
> `apt-get update && \
apt-get -y install sudo`
> `sudo gitlab-runner register --docker-privileged=true`
https://gitlab.local/
http://gitlab.local/
ubuntu:20.04
> `apt-get update && \
apt-get install iputils-ping -y`
> `sudo gitlab-runner start`
`sudo gitlab-runner restart`


docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lesson02-gitlab-1
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lesson02-gitlab-runner-1

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
- docker-runner устанавливаем через compose
- регистрируем через wsl
- https://github.com/RomNero/YouTube-Infos/blob/main/03-GitLabCICD.md
- https://www.youtube.com/watch?v=jAIhhULc7YA&t=1262s
- https://stackoverflow.com/questions/61105333/cannot-connect-to-the-docker-daemon-at-tcp-localhost2375-is-the-docker-daem
- https://gitlab.com/gitlab-org/gitlab-runner/-/issues/6295
    - openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -nodes -subj '/CN=gitlab.local' -days 8000


` openssl genrsa -out ca.key 2048
openssl req -new -x509 -days 365 -key ca.key -subj "/C=CN/ST=GD/L=SZ/O=Acme, Inc./CN=Acme Root CA" -out ca.crt
openssl req -newkey rsa:2048 -nodes -keyout gitlab.local.key -subj "/C=CN/ST=GD/L=SZ/O=Acme, Inc./CN=*.gitlab.local" -out gitlab.local.csr
openssl x509 -req -extfile <(printf "subjectAltName=DNS:gitlab.local,DNS:registry.gitlab.local,DNS:mattermost.gitlab.local") -days 365 -in gitlab.local.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out gitlab.local.crt
`

openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes \
-keyout gitlab.local.key -out gitlab.local.crt -subj '/CN=gitlab.local' \
-addext 'subjectAltName=DNS:gitlab.local,DNS:registry.gitlab.local,DNS:mattermost.gitlab.local'


`openssl req -new -key certs/key.pem \
-subj "/CN=gitlab.local" \
-addext "subjectAltName = DNS:gitlab.local" \
-out certs/cert.csr \
-config certs/foo-bar_config.txt`

- копируем их в ssl в gitlab-config
    - cp /home/key.pem gitlab.local.key
    - cp /home/cert.pem gitlab.local.crt
    - `docker exec -it lesson02-gitlab-1 /bin/bash`
        - `gitlab-ctl restart`

    - ## Регистрация ранера
    - https://www.youtube.com/watch?v=jAIhhULc7YA&t=1058s
    - https://translated.turbopages.org/proxy_u/en-ru.ru.4e94ba44-62a8392b-2906648b-74722d776562/https/stackoverflow.com/questions/59422889/error-job-failed-system-failure-cannot-connect-to-the-docker-daemon-at-unix
    - https://gitlab.com/gitlab-org/gitlab-runner/-/issues/28841
    - docker ps
    - docker exec -it lesson02-gitlab-runner-1 /bin/bash
      `
      SERVER=gitlab.local
      PORT=443
      CERTIFICATE=/etc/gitlab-runner/certs/${SERVER}.crt
      # Create the certificates hierarchy expected by gitlab
      mkdir -p $(dirname "$CERTIFICATE")
          cat /etc/gitlab-runner/certs/${SERVER}.crt
          cat ${CERTIFICATE}

      # Get the certificate in PEM format and store it
      apt-get update && \
      apt-get -y install sudo

      openssl s_client -connect ${SERVER}:${PORT} -showcerts </dev/null 2>/dev/null | sed -e '/-----BEGIN/,/-----END/!d' | sudo tee "$CERTIFICATE" >/dev/null
      openssl s_client -connect ${SERVER}:${PORT} -showcerts </dev/null 2>/dev/null | sed -e '/-----BEGIN/,/-----END/!d' | tee "$CERTIFICATE" >/dev/null
      # Register your runner
      gitlab-runner register --tls-ca-file="$CERTIFICATE"

      sudo gitlab-runner register --tls-ca-file="$CERTIFICATE" --docker-privileged=true
      gitlab-runner register --docker-privileged=true
      https://gitlab.local/
      http://gitlab.local/
      ubuntu:20.04

      `
        - `gitlab-runner start`
        - `gitlab-runner restart`

### Переменные окружения env CI/CD
> `script: 'ls env:' // Все переменные с префиксом CI_`

### Локальный Dokerhub (Container registry)
- `https://docs.gitlab.com/omnibus/settings/ssl.html`
- `https://docs.gitlab.com/ee/user/packages/container_registry/`
- `https://www.youtube.com/watch?v=G-WmX1um5cc`
- `https://www.youtube.com/watch?v=n_21ya2MoKg`
- `docker ps`
- `docker exec -it lesson02-gitlab-1 /bin/bash`  --> `cd etc/gitlab/` // Конфигируруем (см.видео), можно также через проброшеные volume \\wsl$\docker-desktop-data\data\docker\volumes\gitlab-config`
- `gitlab.rb` // Конфигурирование GitLab
    - `https://docs.gitlab.com/omnibus/settings/ssl.html#lets-encrypt-integration`
    - `external_url 'https://registry.gitlab.local' ... и другие параметры, для коректной работы letsencrypt подключить для https' `
    - `docker exec -it lesson02-gitlab-1 /bin/bash`
        - `docker container inspect lesson02-gitlab-1 | grep "IPAddress"`
        - `docker container inspect lesson02-gitlab-runner-1 | grep "IPAddress"`
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






https://stackoverflow.com/questions/59422889/error-job-failed-system-failure-cannot-connect-to-the-docker-daemon-at-unix

ХОСТ: 11.22.33.44

GITLAB_IP: 55.66.77.88

// Хост
172.30.160.1

// Runner
172.18.0.3
docker run -p 2375:2375 -d --name gitlab.runner --env DOCKER_HOST=tcp://172.30.160.1:2375 --restart always -v C:/temp/srv/gitlab-runner/config/:/etc/gitlab.runner -v C:/temp/var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest

docker exec gitlab.runner gitlab-runner register -n --url=http://55.66.77.88:9000/ --registration-token=sEcrEttOkEnfOrgItlAb --description="Shared Docker Runner" --executor=docker --docker-image=docker --docker-privileged=true

// GITLAB_IP:
172.18.0.2


netsh interface portproxy add v4tov4 listenport=2375 listenaddress=172.30.160.1 connectport=2375 connectaddress=127.0.0.1



curl http://gitlab.local/gitlab-instance-4831cf98/Monitoring.git

curl http://gitlab.local/root/test.git/


https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#define-an-image-from-a-private-container-registry
DOCKER_AUTH_CONFIG + config.toml
base 64 encoded username:password

https://stackoverflow.com/questions/65962567/docker-login-before-pulling-image-for-gitlab-runner

https://github.com/gitlabhq/gitlab-runner/blob/master/docs/faq/README.m
https://www.reddit.com/r/gitlab/comments/m5oqpj/failed_to_connect_to_gitlab_local_instance_on/


docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lesson02-gitlab-1

https://username:password@github.com/org/project.git
172.18.0.2 gitlab.local

[[runners]]
...
[runners.docker]
...
network_mode = "gitlab_net"

[runners.docker]
extra_hosts = ["gitlab.lab01.ng:172.16.10.100"]

[[runners]]
tls-ca-file = "/etc/gitlab-runner/certs/gitlab.local.crt"
[runners.docker]
extra_hosts = ["gitlab.local:172.18.0.2"]
host = "tcp://172.30.160.1:2375"
volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock", "/etc/gitlab-runner/certs:/etc/gitlab-runner/certs"]
privileged = true


docker exec [container-id or container-name] cat /etc/hosts

`docker exec -it lesson02-gitlab-runner-1 /bin/bash`
echo '172.18.0.2 gitlab.local' >> /etc/hosts
cd

      - `gitlab-runner start`
      - `gitlab-runner restart`


// HOST
172.22.208.1
192.168.0.101
// GITLAB_IP:
172.19.0.2
// Runner
172.19.0.3

https://stackoverflow.com/questions/59422889/error-job-failed-system-failure-cannot-connect-to-the-docker-daemon-at-unix
netsh advfirewall firewall add rule name="gitlab" protocol="TCP" localport=2375 action=allow dir=IN
netsh interface portproxy add v4tov4 listenport=2375 listenaddress=172.18.0.3 connectport=2375 connectaddress=127.0.0.1


docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lesson02-gitlab-1
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lesson02-gitlab-runner-1

`docker exec -it lesson02-gitlab-runner-1 /bin/bash`

apt-get update
apt-get install iputils-ping -y


echo '172.30.160.1 gitlab.local' >> /etc/hosts
echo '192.168.0.101 gitlab.local' >> /etc/hosts

sed -i '$ d' /etc/hosts

cat /etc/hosts
apt update
apt upgrade
apt install nano
nano /etc/hosts


`docker exec -it lesson02-gitlab-1 /bin/bash`

`gitlab-ctl reconfigure`
`gitlab-ctl renew-le-certs`

docker exec -it lesson02-gitlab-runner-1


https://username:password@github.com/org/project.git
curl http://root:rootroot@gitlab.local
curl http://root:rootroot@gitlab.local/http://gitlab.local/root/test/.git


### Перенос папки образов на другой диск
https://predvoditelev.ru/notes/change-docker-data-location-in-windows/
wsl --list -v
wsl --export docker-desktop-data "f:\docker-desktop-data.tar"
wsl --unregister docker-desktop-data
wsl --import docker-desktop-data "f:\docker\wsl" "f:\docker-desktop-data.tar" --version 2



external_url 'http://gitlab.local'
registry_external_url 'http://registry.gitlab.local'
mattermost_external_url 'http://mattermost.gitlab.local'
nginx['listen_port'] = 80
nginx['listen_https'] = false


`docker exec -it lesson02-gitlab-runner-1 /bin/bash`

apt-get update && \
apt-get -y install sudo

USER docker
CMD /bin/bash

docker exec -it --user root lesson02-gitlab-runner-1 /bin/bash

chmod 770 /etc/hosts




c:\Windows\System32\drivers\etc\hosts
прописать IP host через 172.ххх  // 172.28.112.1 + на Runner тоже самое прописать и проверить


В gitlab.rb достаточно
external_url 'http://gitlab.local'
registry_external_url 'http://registry.gitlab.local'
mattermost_external_url 'http://mattermost.gitlab.local'

если ошибка сокета перезагружаем комп, удаляем контейнеры (как понимаю сеть между контейнерами багуется)
и бегуна в контейнере через sudo устанавливает


[[runners]]
name = "doc"
url = "http://gitlab.local/"
token = "R_ZsHsnfxAgUtZc6xt8L"
executor = "docker"
[runners.custom_build_dir]
[runners.cache]
[runners.cache.s3]
[runners.cache.gcs]
[runners.cache.azure]
[runners.docker]
tls_verify = false
image = "ubuntu"
privileged = true
disable_entrypoint_overwrite = false
oom_kill_disable = false
disable_cache = false
volumes = ["/cache"]
shm_size = 0




В gitlab.rb https достаточно
external_url 'https://gitlab.local'
registry_external_url 'https://registry.gitlab.local'
mattermost_external_url 'https://mattermost.gitlab.local'
#nginx['listen_port'] = 80
#nginx['listen_https'] = false

letsencrypt['enable'] = true
letsencrypt['auto_renew_hour'] = "12"
letsencrypt['auto_renew_minute'] = "30"
letsencrypt['auto_renew_day_of_month'] = "*/7"
letsencrypt['auto_renew'] = false




// SAN-сертификат
san.cnf
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
stateOrProvinceName         = State or Province Name (full name)
localityName               = Locality Name (eg, city)
organizationName           = Organization Name (eg, company)
commonName                 = Common Name (e.g. server FQDN or YOUR name)
[ req_ext ]
subjectAltName = @alt_names
[alt_names]
DNS.1   = gitlab.local

openssl req -out sslcert.csr -newkey rsa:2048 -nodes -keyout private.key -config san.cnf

openssl req -noout -text -in sslcert.csr | grep DNS


// Ошибка runner Failed to remove network
    apt-get install sudo
    runner регистрировать через sudo

    docker exec -it lesson02-gitlab-runner-1 /bin/bash
    apt-get update
    apt-get install -y docker.io
    docker -v
    gitlab-runner restart



netsh interface portproxy add v4tov4 listenport=2375 listenaddress=192.168.0.101 connectport=2375 connectaddress=127.0.0.1
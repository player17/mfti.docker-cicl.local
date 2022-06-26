http://gitlab.local/
`docker exec -it lesson02-gitlab-1 grep 'Password:' /etc/gitlab/initial_root_password`
https://gitlab.local/
### Старт проекта через docker-compose.yml локально без ssl
> `IP Host (та на которой ОС установлена) машины желательно оставить постоянным 192.168.0.101, и etc/host прописать домены на данный IP`

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

### Проброшенные папки из Windows 11
\\wsl$\docker-desktop-data\data\docker\volumes\

### Локальные паки через WSL
cd /mnt/d


### Проблемы с SSl сертификатами (Проверять на репо докере, потом пробовать регать ранер чрезе https)

- `docker exec -it lesson02-gitlab-1 /bin/bash`
- `gitlab-ctl reconfigure`
- `gitlab-ctl restart`


` 
openssl genrsa -out ca.key 2048
openssl req -new -x509 -days 365 -key ca.key -subj "/C=CN/ST=GD/L=SZ/O=Acme, Inc./CN=Acme Root CA" -out ca.crt
openssl req -newkey rsa:2048 -nodes -keyout gitlab.local.key -subj "/C=CN/ST=GD/L=SZ/O=Acme, Inc./CN=*.gitlab.local" -out gitlab.local.csr
openssl x509 -req -extfile <(printf "subjectAltName=DNS:gitlab.local,DNS:registry.gitlab.local,DNS:mattermost.gitlab.local") -days 365 -in gitlab.local.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out gitlab.local.crt
`
- Добавить в корневые сертификаты
- Перезагрузить докер

`docker exec -it lesson02-gitlab-1 /bin/bash`
- `gitlab-ctl reconfigure`
- `gitlab-ctl restart`

- `docker login registry.gitlab.local` проверка на корректность SSl

// gitlab.rb
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


### Регистрация ранера
- runner регистрировать через sudo
  
- docker ps
- docker exec -it lesson02-gitlab-runner-1 /bin/bash
  SERVER=gitlab.local
  PORT=443
  CERTIFICATE=/etc/gitlab-runner/certs/${SERVER}.crt
  cat ${CERTIFICATE}
  openssl s_client -connect ${SERVER}:${PORT} -showcerts </dev/null 2>/dev/null | sed -e '/-----BEGIN/,/-----END/!d' | tee "$CERTIFICATE" >/dev/null

  apt-get update
  apt-get install -y sudo
  apt-get install -y docker.io
  sudo gitlab-runner register --tls-ca-file="$CERTIFICATE" --docker-privileged=true
    sudo gitlab-runner register --docker-privileged=true
    ubuntu:20.04
  
### Ошибка прослушивания Demons
    если ошибка сокета перезагружаем комп, удаляем контейнеры (как понимаю сеть между контейнерами багуется)
    и бегуна в контейнере через sudo устанавливает + gitlab-runner restart (tls_verify = true / false в  config.toml) 
    и tls-ca-file = "/etc/gitlab-runner/certs/gitlab.local.crt" в config.toml
    и удалить из certmgr.msc все сертификаты связанные с gitlab + кеш в браузере

    apt-get install sudo
    runner регистрировать через sudo

    docker exec -it lesson02-gitlab-runner-1 /bin/bash
    apt-get update
    apt-get install -y docker.io
    docker -v
    gitlab-runner restart

    // config.toml
    extra_hosts = ["gitlab.local:192.168.0.101"]
    network_mode = "lesson02_gitlab_net"
    volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock", "/etc/gitlab-runner/certs:/etc/gitlab-runner/certs"]
    tls_verify = true / false

    // при https  убрать записть extra_hosts = ["gitlab.local:192.168.0.101"] (но это не точно) + tls_verify = true и перегрузить runner


# Пример конфига config.toml
[[runners]]
name = "doc"
url = "https://gitlab.local/"
token = "gpEaYv4ZaN6rUA5UeEiQ"
tls-ca-file = "/etc/gitlab-runner/certs/gitlab.local.crt"
executor = "docker"
[runners.custom_build_dir]
[runners.cache]
[runners.cache.s3]
[runners.cache.gcs]
[runners.cache.azure]
[runners.docker]
tls_verify = true
image = "ubuntu:20.04"
privileged = true
disable_entrypoint_overwrite = false
oom_kill_disable = false
disable_cache = false
shm_size = 0
volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock", "/etc/gitlab-runner/certs:/etc/gitlab-runner/certs"]
network_mode = "lesson02_gitlab_net"

# Пример конфига gitlab.rb
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





### Примеры с GIT
git init
git remote add origin https://gitlab.local/gitlab-instance-de0effa8/Monitoring.git
git config http.sslVerify false
git fetch origin
git reset --mixed origin/main
git add
git commit -m "firts commit"
git push -u origin master

git checkout main
git pull origin main --allow-unrelated-histories
git merge master --allow-unrelated-histories

git push -d origin master

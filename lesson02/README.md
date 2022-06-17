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
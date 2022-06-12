http://gitlab.local/
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
`docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password`

#### Runner install on Window 
https://www.youtube.com/watch?v=KzjnZSOm_Uo
https://docs.gitlab.com/runner/install/windows.html

> `https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe`
>> `переименовать в c:\GitLab-Runner\gitlab-runne.exe `

> `cmd от administrator`
>> `cd c:\GitLab-Runner\`
>>> `gitlab-runner.exe register`
>>>> `... Enter an executor: Shell`

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
sudo docker network create gitlab-network
sudo docker network connect gitlab-network gitlab
sudo docker network connect gitlab-network gitlab-runner

### Пример docker-compose.yml
> `http://snakeproject.ru/rubric/article.php?art=gitlab_docker_03_02_2022`
> `https://github.com/ElisDN/demo-project-manager/`
> `docker-compose up -d`



02-48

### Установка GitLab на local for Windows
#### Через docker
https://docs.gitlab.com/ee/install/docker.html
https://www.youtube.com/watch?v=MEUD4SsMqOs
docker run --name gitlab --detach \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --restart always \
  --volume gitlab-config:/etc/gitlab \
  --volume gitlab-logs:/var/log/gitlab \
  --volume gitlab-data:/var/opt/gitlab \
  --shm-size 256m gitlab/gitlab-ee:latest

// Папка docker в wsl
\\wsl$\docker-desktop-data\version-pack-data\community\docker

sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

	login: root
	pass:  ...

		http://127.0.0.1/users/sign_in

#### Через Ubuntu WSL (не получилось)
https://docs.gitlab.com/ee/install/docker.html

https://www.youtube.com/watch?v=_Nhg6EPMeF4
	sudo EXTERNAL_URL="http://gitlab-hub.local" apt-get install -f gitlab-ee
			systemctl restart gitlab-runsvdir.service
			sysv restart gitlab-runsvdir.service
			init restart gitlab-runsvdir.service
		sudo apt-cache search gitlab-ce
		sudo apt-cache search gitlab-ee
		sudo dpkg --configure -a



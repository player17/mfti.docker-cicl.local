FROM ubuntu:18.04

RUN apt update && \
    echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections && \
    apt install git mc bash python3-dev python3-pip -y && \
    pip3 install jupyter
ADD my_file.txt /root/my_file.txt

# переменная
ENV varUser "noroot"

RUN useradd -m -d /home/${varUser} ${varUser} && \
    chown -R ${varUser} /home/${varUser} && \
    chmod -R 755 /home/${varUser} && \
    chsh -s /bin/bash ${varUser}

ADD entrypoint.sh /home/${varUser}
RUN chown ${varUser} /home/${varUser}/entrypoint.sh && \
    chmod +x /home/${varUser}/entrypoint.sh

USER ${varUser}
WORKDIR /home/${varUser}
RUN echo ${USER} && whoami && pwd

# Запуск исполняемого файла в контейнере
ENTRYPOINT ["/home/noroot/entrypoint.sh"]
# Открываем порт в контейнере
EXPOSE 10000

#USER root
#WORKDIR /root

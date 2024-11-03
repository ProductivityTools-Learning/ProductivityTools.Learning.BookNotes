Dockerfile

```Dockerfile
# Originally forked from: git@github.com:gasi/docker-node-hello.git

FROM docker.io/node:18.13.0

ARG email="anna@example.com"
LABEL "maintainer"=$email
LABEL "rating"="Five Stars" "class"="First Class"

USER root

ENV AP /data/app
ENV SCPATH /etc/supervisor/conf.d

RUN apt-get -y update

# The daemons
RUN apt-get -y install supervisor
RUN mkdir -p /var/log/supervisor

# Supervisor Configuration
COPY ./supervisord/conf.d/* $SCPATH/

# Application Code
COPY *.js* $AP/

WORKDIR $AP

RUN npm install

CMD ["supervisord", "-n"]
```

- supervisord - jest odpowiedzialny za managowanie procesami. Jego config odpala node

```bash
[program:docker-node-hello]
directory=/data/app
command=/usr/local/bin/node /data/app/index.js
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stderr_logfile=/var/log/supervisor/%(program_name)s.log
autorestart=true
```

### Container:
- Zleca się odpalanie tylko jednego procesu w kontenerze

### Budowanie obrazu:
```docker image build -t pwlearning/docker-node-hello:latest .```

- -t to jest tag
- . mowi ze należy skopoiwać plik z bieżącego folderu
Jeżeli nie chcemy aby docker używał cache to możemy dodać  ```--no-cache```
możemy pominąć image w komencie i dać tylko docker build

### Uruchamianie:
```docker container run --rm -d -p 6070:6080 pwlearning/docker-node-hello:latest```

- -d chyba w backgroundzie
- -p mapowanie portu w kontenerze do hosta, host:docker curl localhost:6070

### Listowanie
- docker container ls
- docker ps


### Detale image
- inspect pwlearning/docker-node-hello:latest |grep maintainer




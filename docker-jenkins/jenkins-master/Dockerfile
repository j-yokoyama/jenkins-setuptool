FROM jenkinsci/blueocean:latest

ARG DOCKER_GID

RUN echo ${DOCKER_GID}

USER root

# install glibc
RUN apk add ca-certificates wget && \
    wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
    GLIBC_VERSION='2.27-r0' && \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk && apk add glibc-${GLIBC_VERSION}.apk && \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-bin-${GLIBC_VERSION}.apk && apk add glibc-bin-${GLIBC_VERSION}.apk

# jenkins user setting
RUN apk add --update shadow && \
    groupadd -f -g ${DOCKER_GID} docker-host && \
    usermod -aG docker-host jenkins

# install docker compose
RUN DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | \
                               grep tag_name | \
                               sed -e 's/[^0-9\.]//g') && \
    curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" \
         -o /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose

USER jenkins

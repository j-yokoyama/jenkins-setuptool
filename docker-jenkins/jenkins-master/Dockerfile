FROM jenkinsci/blueocean:latest

ARG DOCKER_GID

RUN echo ${DOCKER_GID}

USER root
RUN apk add --update shadow && \
    groupadd -f -g ${DOCKER_GID} docker-host && \
    usermod -aG docker-host jenkins

RUN DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | \
                               grep tag_name | \
                               sed -e 's/[^0-9\.]//g') && \
    curl -L --fail https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/run.sh \
         -o /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose

USER jenkins

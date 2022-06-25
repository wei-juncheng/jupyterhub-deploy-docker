ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION
RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION jupyter-resource-usage
USER root
RUN usermod -a -G root jovyan
RUN apt-get update && apt-get install -yqq \
    git \
    curl \
    zip \
    unzip \
    iputils-ping \
    vim \
    wget \
    htop

COPY docker-entrypoint.sh /srv/docker-entrypoint.sh
ENTRYPOINT ["tini", "--", "/srv/docker-entrypoint.sh"]
CMD ["start-singleuser.sh"]
WORKDIR /home/jovyan/
COPY ./DGX環境說明書.ipynb /docs/DGX環境說明書.ipynb 
COPY ./.bashrc_profile /docs/.bashrc_profile
ENV TZ=Asia/Taipei
ENV MEM_LIMIT=32000000000

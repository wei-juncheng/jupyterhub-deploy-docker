ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION

#---code server install-------
ARG code_server_proxy_wheel="jupyter_codeserver_proxy-1.0b3-py3-none-any.whl"

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# prerequisites ----
# hadolint ignore=DL3008
RUN apt-get update && apt-get install -yq --no-install-recommends \
    curl \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

#TODO: replace by wget already installed in the imag
RUN curl -fsSL https://code-server.dev/install.sh | sh && \
    rm -rf "${HOME}/.cache"

# USER $NB_UID

# set the home directory
ENV CODE_WORKINGDIR="${HOME}"

# code-server proxy copy package ----
COPY ./dist/${code_server_proxy_wheel} /${HOME}/

# jupyter-server-proxy + code-server proxy install ----
# hadolint ignore=DL3013
RUN conda install --quiet --yes \
    'jupyter-server-proxy' && \
    jupyter labextension install @jupyterlab/server-proxy && \
    pip install --quiet --no-cache-dir "${HOME}/${code_server_proxy_wheel}" && \
    rm "${HOME}/${code_server_proxy_wheel}" && \
    jupyter lab clean -y && \
    npm cache clean --force && \
    conda clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"
#---end of code server install-------


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

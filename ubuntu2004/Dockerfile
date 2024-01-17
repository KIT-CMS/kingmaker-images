FROM ubuntu:20.04
LABEL AUTHOR="Sebastian Brommer <brommer.sebastian@gmail.com>"
ARG MINIFORGE_NAME=Miniforge3
ARG MINIFORGE_VERSION=23.11.0-0
ARG TARGETPLATFORM
ARG ENV_NAME=env
ENV CONDA_DIR=/opt/conda

# Create Server directory
RUN mkdir -p /srv

# Add configurations
# they will be picked from https://raw.githubusercontent.com/KIT-CMS/KingMaker during build
ADD centos8/conda_env.yml /srv/conda_env.yml

RUN apt -y update && apt -y upgrade && apt -y autoremove && apt -y autoclean
RUN apt -y install wget python3 python3-pip tini && apt -y autoremove && apt -y autoclean
RUN python3 -m pip install --upgrade pip
# Install conda
RUN wget --quiet https://github.com/conda-forge/miniforge/releases/download/${MINIFORGE_VERSION}/${MINIFORGE_NAME}-${MINIFORGE_VERSION}-Linux-$(uname -m).sh -O /tmp/miniforge.sh && \
    /bin/bash /tmp/miniforge.sh -b -p ${CONDA_DIR} && \
    rm /tmp/miniforge.sh
RUN . ${CONDA_DIR}/etc/profile.d/conda.sh && conda env create -f /srv/conda_env.yml -n ${ENV_NAME}

RUN echo ". ${CONDA_DIR}/etc/profile.d/conda.sh && conda activate ${ENV_NAME}" >> /etc/skel/.bashrc && \
    echo ". ${CONDA_DIR}/etc/profile.d/conda.sh && conda activate ${ENV_NAME}" >> ~/.bashrc

ENTRYPOINT ["tini", "--"]
CMD [ "/bin/bash" ]
FROM cern/cc7-base:latest
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
ADD centos7/conda_env.yml /srv/conda_env.yml

# Switch to alternative epel source as CERN CentOS7 address is no longer available
RUN sed -i s@http://linuxsoft.cern.ch/@https://archives.fedoraproject.org/pub/archive/@ /etc/yum.repos.d/epel.repo

RUN yum -y update && yum clean all
RUN yum -y install epel-release && yum clean all
RUN yum -y install wget python3 python3-pip tini redhat-lsb-core which gcc-c++ strace perf && yum clean all

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

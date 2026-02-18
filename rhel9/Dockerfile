FROM cern/alma9-base:latest

LABEL author="Tim Voigtländer <tim.voigtlaender@kit.edu>"

# ------------------------------------------------------------------------------
# Build arguments
# ------------------------------------------------------------------------------
ARG MINIFORGE_NAME=Miniforge3
ARG MINIFORGE_VERSION=23.11.0-0
ARG TARGETPLATFORM
ARG ENV_NAME=env
# ARG ENV_FILE_NAME=None

# ------------------------------------------------------------------------------
# Environment
# ------------------------------------------------------------------------------
ENV CONDA_DIR=/opt/conda

# ------------------------------------------------------------------------------
# Prepare filesystem
# ------------------------------------------------------------------------------
RUN mkdir -p /srv

# ------------------------------------------------------------------------------
# System packages
# ------------------------------------------------------------------------------
RUN dnf -y update && \
    dnf -y install epel-release && \
    dnf -y install \
        wget \
        python3 \
        python3-pip \
        tini \
        which \
        zlib-devel \
        gcc \
        glibc-devel \
        kernel-headers \
        pcre2-utf16 \
        libSM \
        strace \
        perf \
        openssh \
        crypto-policies-scripts && \
    dnf clean all

# ------------------------------------------------------------------------------
# Allow self-signed certificates (ROOT / grid compatibility)
# ------------------------------------------------------------------------------
RUN update-crypto-policies --set DEFAULT:SHA1

# ------------------------------------------------------------------------------
# Python tooling
# ------------------------------------------------------------------------------
RUN python3 -m pip install --upgrade pip

# ------------------------------------------------------------------------------
# Install Miniforge (Conda)
# ------------------------------------------------------------------------------
RUN wget -q https://github.com/conda-forge/miniforge/releases/download/${MINIFORGE_VERSION}/${MINIFORGE_NAME}-${MINIFORGE_VERSION}-Linux-$(uname -m).sh -O /tmp/miniforge.sh && \
    bash /tmp/miniforge.sh -b -p ${CONDA_DIR} && \
    rm -f /tmp/miniforge.sh

# ------------------------------------------------------------------------------
# Conda environment specification
# (picked from https://raw.githubusercontent.com/KIT-CMS/KingMaker during build)
# ------------------------------------------------------------------------------
ADD rhel9/conda_env.yml /srv/conda_env.yml

# ------------------------------------------------------------------------------
# Create conda environment (build-time only)
# ------------------------------------------------------------------------------

RUN (source ${CONDA_DIR}/bin/activate "" && conda env create -f /srv/conda_env.yml -n ${ENV_NAME})

# ------------------------------------------------------------------------------
# Runtime
# ------------------------------------------------------------------------------
ENTRYPOINT ["tini", "--"]
CMD ["/bin/bash"]

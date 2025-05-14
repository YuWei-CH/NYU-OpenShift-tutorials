FROM nvidia/cuda:12.6.0-devel-ubuntu22.04

RUN apt-get update && apt-get install -y \
    wget git curl bzip2 \
    && rm -rf /var/lib/apt/lists/*

# Install Miniconda
RUN curl -sLO https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda && \
    rm Miniconda3-latest-Linux-x86_64.sh

ENV PATH=/opt/conda/bin:$PATH
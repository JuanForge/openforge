FROM docker.io/nvidia/cuda:13.3.1-cudnn-devel-ubuntu24.04
# 12.8.1-devel-ubuntu24.04

ARG AI_TOOLKIT_COMMIT=0e4b6e8695ae836f463eacfde16de695337492e2

RUN userdel -r ubuntu \
    && useradd -m -u 1000 app \
    && mkdir -p /app && chown -R app:app /app

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/app/.venv/bin:/home/app/.local/bin:$PATH"

# ref https://en.wikipedia.org/wiki/CUDA
# ENV TORCH_CUDA_ARCH_LIST="8.0 8.6 8.9 9.0 10.0 12.0"

RUN apt-get update && apt-get install --no-install-recommends -y \
    git \
    curl \
    build-essential \
    cmake \
    wget \
    python3.12 \
    python3-pip \
    python3-dev \
    python3-wheel \
    python3-venv \
    ffmpeg \
    python3-opencv \
    openssl \
    unzip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /tmp
RUN curl -sL https://deb.nodesource.com/setup_23.x | bash \
    && apt-get update \
    && apt-get install -y nodejs \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* || true

USER app

WORKDIR /app
RUN git clone https://github.com/ostris/ai-toolkit.git . \
    && git checkout ${AI_TOOLKIT_COMMIT}

RUN python3.12 -m venv .venv

RUN /app/.venv/bin/python -m pip install --no-cache-dir  setuptools
RUN /app/.venv/bin/python -m pip install --no-cache-dir \
    torch==2.13.0 \
    torchvision==0.28.0 \
    torchaudio==2.11.0 --index-url https://download.pytorch.org/whl/cu130

RUN /app/.venv/bin/python -m pip install --no-cache-dir -r requirements.txt

RUN /app/.venv/bin/python -c "import torch; print(f'torch : {torch.__version__}')" \
    && /app/.venv/bin/python -m pip install -U --no-cache-dir \
    torchcodec==0.15.0 \
    natten==0.21.7+torch2130cu130 --find-links https://whl.natten.org \
    https://github.com/mjun0812/flash-attention-prebuild-wheels/releases/download/v0.9.47/flash_attn-2.8.3+cu130torch2.13-cp312-cp312-manylinux_2_24_x86_64.manylinux_2_28_x86_64.whl \
    && python -c "import flash_attn, natten, torchcodec; print('accelerators OK:', flash_attn.__version__, natten.__version__, torchcodec.__version__)"

WORKDIR /app/ui
RUN npm ci \
    && npm run update_db \
    && npm run build

EXPOSE 8675

CMD ["npm", "run", "start"]
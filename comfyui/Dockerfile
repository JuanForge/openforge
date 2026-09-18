FROM docker.io/nvidia/cuda:13.3.1-cudnn-devel-ubuntu24.04
# 13.3.1-cudnn-runtime-ubuntu24.04
# 13.0.3-cudnn-runtime-ubuntu22.04

ARG COMFYUI_VERSION=v0.36.0

RUN userdel -r ubuntu \
    && useradd -m -u 1000 app \
    && mkdir -p /app && chown -R app:app /app

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/app/.venv/bin:/home/app/.local/bin:$PATH"

RUN apt update && apt-get install -y \
    python3 \
    python3-pip \
    python3-venv \
    git \
    build-essential \
    libgl1 \
    libglib2.0-0


# build-essential
#   fix : RuntimeError: Failed to find C compiler. Please specify via CC environment variable or set triton.knobs.build.impl.

USER app

WORKDIR /app

RUN git clone https://github.com/comfyanonymous/ComfyUI.git . && \
    git fetch --tags && git checkout ${COMFYUI_VERSION}

# RUN git clone https://github.com/comfyanonymous/ComfyUI.git . \
# && git fetch --tags && git checkout "$(git tag --sort=-v:refname | head -n 1)"

RUN python3 -m venv /app/.venv

RUN git clone https://github.com/Comfy-Org/ComfyUI-Manager ./custom_nodes/ComfyUI-Manager

RUN /app/.venv/bin/python -m pip install --no-cache-dir torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu130

RUN /app/.venv/bin/python -m pip install --no-cache-dir -r requirements.txt
RUN /app/.venv/bin/python -m pip install --no-cache-dir -r ./custom_nodes/ComfyUI-Manager/requirements.txt

EXPOSE 8188

CMD ["/app/.venv/bin/python", "main.py", "--listen", "0.0.0.0", "--enable-manager"]

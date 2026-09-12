# unstable version

FROM docker.io/nvidia/cuda:13.3.1-cudnn-devel-ubuntu24.04

RUN mkdir app && cd app

RUN apt-get update && \
    apt-get install python3 python3-venv python3-pip -y && \
    apt-get install sox libsox-fmt-all ffmpeg -y

RUN python3 -m venv .venv

RUN .venv/bin/pip install qwen-tts
RUN .venv/bin/pip install https://github.com/mjun0812/flash-attention-prebuild-wheels/releases/download/v0.10.0/flash_attn-2.8.3+cu130torch2.14-cp312-cp312-linux_x86_64.whl

CMD ["qwen-tts-demo", "Qwen/Qwen3-TTS-12Hz-1.7B-Base", "--device", "cuda:0", "--dtype", "bfloat16", "--flash-attn", "--ip", "127.0.0.1", "--port", "8000", "--concurrency", "1"]
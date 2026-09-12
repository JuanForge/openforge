# unstable version

FROM docker.io/nvidia/cuda:13.3.1-cudnn-devel-ubuntu24.04

RUN mkdir app && cd app

RUN apt-get update && \
    apt-get install -y git cmake build-essential python3 python3-pip

RUN git clone --recurse-submodules https://github.com/rodrigomatta/s2.cpp.git

RUN cd s2.cpp

RUN cmake -B build     -DCMAKE_BUILD_TYPE=Release     -DS2_CUDA=ON

RUN cmake --build build --parallel $(nproc)

RUN pip install -U huggingface_hub --break-system-packages

RUN mkdir -p models && hf download rodrigomt/s2-pro-gguf     --include 's2-pro-q8_0.gguf'     --local-dir models

CMD ["./build/s2   --model models/s2-pro-q8_0.gguf   --tokenizer tokenizer.json   --cuda 0   --gpu-layers 36   --server   --host 127.0.0.1   --port 3030"]
FROM ubuntu:22.04

WORKDIR /build
COPY . ./rust

RUN apt-get update && \
    apt-get install -y git && \
    apt-get install -y build-essential && \
    apt-get install -y curl && \
    apt-get install -y python3 && \
    apt-get install -y python3-pip

RUN python3 -m pip install cmake
ENV PATH="/opt/cmake/bin:${PATH}"

RUN curl --proto '=https' --tlsv1.2 -sSf "https://sh.rustup.rs" | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"
RUN rustup target add x86_64-unknown-linux-musl

RUN mkdir -p /build/onnxruntime && if [ ! "$(ls -A /build/onnxruntime)" ]; then \
    git clone "https://github.com/microsoft/onnxruntime"

WORKDIR /build/onnxruntime
RUN git fetch --all --tags
RUN git checkout v1.17.0 -b build
RUN cmake cmake -S cmake -B "build/Linux/Release"
RUN ./build.sh --build --config Release --compile_no_warning_as_error --skip_submodule_sync --allow_running_as_root

WORKDIR /build/rust
ENV ORT_LIB_LOCATION="$(dirname $(pwd))/onnxruntime/build/Linux/Release"
RUN cargo build --target=x86_64-unknown-linux-musl --release --features=bin --bin magika

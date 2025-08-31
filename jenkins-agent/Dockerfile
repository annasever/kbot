FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y curl git make build-essential software-properties-common docker.io && \
    rm -rf /var/lib/apt/lists/*

RUN curl -LO https://go.dev/dl/go1.21.2.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go1.21.2.linux-amd64.tar.gz && \
    rm go1.21.2.linux-amd64.tar.gz

ENV PATH=$PATH:/usr/local/go/bin

RUN go install golang.org/x/lint/golint@latest

WORKDIR /workspace
CMD ["/bin/bash"]

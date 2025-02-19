# Copyright 2018, 2019, 2020 the Velero contributors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM --platform=linux/amd64 golang:1.21-bookworm

ARG GOPROXY

ENV GO111MODULE=on
# Use a proxy for go modules to reduce the likelihood of various hosts being down and breaking the build
ENV GOPROXY=${GOPROXY}

# kubebuilder test bundle is separated from kubebuilder. Need to setup it for CI test.
RUN curl -sSLo envtest-bins.tar.gz https://go.kubebuilder.io/test-tools/1.22.1/linux/amd64 && \
    mkdir /usr/local/kubebuilder && \
    tar -C /usr/local/kubebuilder --strip-components=1 -zvxf envtest-bins.tar.gz

RUN wget --quiet https://github.com/kubernetes-sigs/kubebuilder/releases/download/v3.2.0/kubebuilder_linux_amd64 && \
    mv kubebuilder_linux_amd64 /usr/local/kubebuilder/bin/kubebuilder && \
    chmod +x /usr/local/kubebuilder/bin/kubebuilder

# get controller-tools
RUN go install sigs.k8s.io/controller-tools/cmd/controller-gen@v0.12.0

# get goimports (the revision is pinned so we don't indiscriminately update, but the particular commit
# is not important)
RUN go install golang.org/x/tools/cmd/goimports@11e9d9cc0042e6bd10337d4d2c3e5d9295508e7d

# get protoc compiler and golang plugin
WORKDIR /root
RUN apt-get update && apt-get install -y unzip
RUN wget --quiet https://github.com/protocolbuffers/protobuf/releases/download/v3.14.0/protoc-3.14.0-linux-x86_64.zip && \
    unzip protoc-3.14.0-linux-x86_64.zip && \
    mv bin/protoc /usr/bin/protoc && \
    mv include/google /usr/include && \
    chmod a+x  /usr/include/google && \
    chmod a+x  /usr/include/google/protobuf && \
    chmod a+r -R /usr/include/google && \
    chmod +x /usr/bin/protoc
RUN go install github.com/golang/protobuf/protoc-gen-go@v1.4.3

# get goreleaser
RUN wget --quiet https://github.com/goreleaser/goreleaser/releases/download/v1.15.2/goreleaser_Linux_x86_64.tar.gz && \
    tar xvf goreleaser_Linux_x86_64.tar.gz && \
    mv goreleaser /usr/bin/goreleaser && \
    chmod +x /usr/bin/goreleaser

# get golangci-lint
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v1.54.2

# install kubectl
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
RUN chmod +x ./kubectl
RUN mv ./kubectl /usr/local/bin

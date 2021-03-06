FROM golang:1.14-alpine as build

ARG VERSION=v1.0.0
ARG TARGETPLATFORM

ENV GO111MODULE=on \
    CGO_ENABLED=0

RUN apk add --no-cache git

WORKDIR /go/src/github.com/vmware-tanzu/velero-plugin-for-gcp

RUN export GOOS=$(echo ${TARGETPLATFORM} | cut -d / -f1) && \
    export GOARCH=$(echo ${TARGETPLATFORM} | cut -d / -f2) && \
    GOARM=$(echo ${TARGETPLATFORM} | cut -d / -f3); export GOARM=${GOARM:1} && \
    git clone --depth 1 -b ${VERSION} https://github.com/vmware-tanzu/velero-plugin-for-gcp.git . && \
    go mod init && \
    go build -mod=mod -o velero-plugin-for-gcp ./velero-plugin-for-gcp

FROM alpine:3.11

WORKDIR /plugin

RUN apk add --no-cache bash ca-certificates

COPY --from=build /go/src/github.com/vmware-tanzu/velero-plugin-for-gcp/velero-plugin-for-gcp ./velero-plugin-for-gcp

ENTRYPOINT ["/bin/bash", "-c", "cp /plugins/* /target/."]

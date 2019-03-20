FROM alpine:3.8 as certs
RUN apk add -U --no-cache ca-certificates

FROM golang:1.11 as xray-build
ENV XRAY_VERSION=3.0.0
RUN curl https://glide.sh/get | sh && \
    curl --location --silent --show-error -O https://github.com/aws/aws-xray-daemon/archive/V${XRAY_VERSION}.tar.gz && \
    tar -xzvf V${XRAY_VERSION}.tar.gz && \
    mkdir -p ${GOPATH}/src/github.com/aws && \
    mv ./aws-xray-daemon-${XRAY_VERSION} ${GOPATH}/src/github.com/aws/aws-xray-daemon

WORKDIR ${GOPATH}/src/github.com/aws/aws-xray-daemon

RUN glide install
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags "-s -w" -o /xray ./daemon/daemon.go ./daemon/tracing.go

FROM scratch
WORKDIR /
EXPOSE 2000/udp
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=xray-build /xray /usr/bin/
ADD ./3.0/cfg.yaml /
ENTRYPOINT ["/usr/bin/xray", "--config", "/cfg.yaml"]
# CMD ["--region", "us-west-2"]

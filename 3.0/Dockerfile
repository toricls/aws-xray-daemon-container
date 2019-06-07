# This Dockerfile should be processed from the root directory of the repository like:
# $ docker build -t toricls/aws-xray-daemon:3.0.0 -f 3.0/Dockerfile .

FROM amazonlinux:2 as certs
RUN yum install -y ca-certificates

FROM amazonlinux:2 as xray-build
RUN yum install -y tar gzip which git golang
# Install glide to build xray binary
ENV GOPATH=/go
RUN mkdir -p ${GOPATH}/{bin,src,pkg} \
    && export PATH="${PATH}:${GOPATH}/bin" \
    && curl https://glide.sh/get | sh
# Download xray source code form the official repository
ENV XRAY_VERSION=3.0.0
ENV XRAY_ARCHIVE_NAME=V${XRAY_VERSION}.tar.gz
ENV XRAY_ARCHIVE_SHA256SUM=35d219eb41b621742325a2142fd142fdd7f4d2af96c8683142439cb063e5262d
RUN curl --location --silent --show-error -O https://github.com/aws/aws-xray-daemon/archive/${XRAY_ARCHIVE_NAME} \
    && echo "${XRAY_ARCHIVE_SHA256SUM} ${XRAY_ARCHIVE_NAME}" | sha256sum --check \
    && tar -xzvf ${XRAY_ARCHIVE_NAME} \
    && mkdir -p ${GOPATH}/src/github.com/aws \
    && mv ./aws-xray-daemon-${XRAY_VERSION} ${GOPATH}/src/github.com/aws/aws-xray-daemon
# Build xray binary
WORKDIR ${GOPATH}/src/github.com/aws/aws-xray-daemon
RUN ${GOPATH}/bin/glide install
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags "-s -w" -o /xray ./daemon/daemon.go ./daemon/tracing.go

FROM scratch
WORKDIR /
EXPOSE 2000/udp
EXPOSE 2000/tcp
COPY --from=certs /etc/ssl/certs/ca-bundle.crt /etc/ssl/certs/
COPY --from=xray-build /xray /usr/bin/
ADD ./3.0/default-cfg.yaml /default-cfg.yaml
ENTRYPOINT ["/usr/bin/xray", "--local-mode"]
CMD ["--config", "/default-cfg.yaml"]

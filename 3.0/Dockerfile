FROM alpine:3.8 as certs
RUN apk add -U --no-cache ca-certificates

FROM amazonlinux:2 as xray-bin
ENV XRAY_VERSION=3.0.0
RUN yum install -y unzip
RUN curl --location --silent --show-error -O https://s3.dualstack.us-east-2.amazonaws.com/aws-xray-assets.us-east-2/xray-daemon/aws-xray-daemon-linux-${XRAY_VERSION}.zip && \
    # A 'xray' binary should be extracted in the root directory by the following command
    unzip aws-xray-daemon-linux-${XRAY_VERSION}.zip

FROM scratch
WORKDIR /
EXPOSE 2000/udp
EXPOSE 2000/tcp
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=xray-bin /xray /usr/bin/
ADD ./3.0/cfg.yaml /
ENTRYPOINT ["/usr/bin/xray", "--config", "/cfg.yaml"]
# CMD ["--region", "us-west-2"]

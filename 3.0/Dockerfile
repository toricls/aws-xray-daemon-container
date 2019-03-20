FROM alpine:3.8 as certs
RUN apk add -U --no-cache ca-certificates

FROM golang:1.11 as xray-build
ENV REPO=github.com/aws/aws-xray-daemon/
RUN go get -u ${REPO}...
WORKDIR ${GOPATH}/src/${REPO}
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags "-s -w" -o /xray ./daemon/daemon.go ./daemon/tracing.go

FROM scratch
WORKDIR /
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=xray-build /xray /usr/bin/
EXPOSE 2000/udp
ADD cfg.yaml /
ENTRYPOINT ["/usr/bin/xray", "--config", "/cfg.yaml"]
# CMD ["--region", "us-west-2"]

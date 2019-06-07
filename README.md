# aws-xray-daemon-container

A containerized lightweight AWS X-Ray daemon.

## Use pre-built docker images

All pre-built docker images are stored in a Docker Hub repo, [toricls/aws-xray-daemon](https://hub.docker.com/r/toricls/aws-xray-daemon).

You may run this image with some runtime arguments like:
```shell
# Just run it
$ docker run -d toricls/aws-xray-daemon:3.0 --region us-west-2

# Use your own configuration file
$ docker run -d -v $(pwd)/my-custom-config.yaml:/my/custom/cfg.yaml toricls/aws-xray-daemon:3.0 --config /my/custom/cfg.yaml
```

[See all available tags here](https://hub.docker.com/r/toricls/aws-xray-daemon/tags).

### 3.0.x

toricls/aws-xray-daemon:3.0 [Dockerfile](3.0/Dockerfile)

### < v3.0.0

WIP

## Build your own docker image

### 3.0.x

Make some changes on the files in the `3.0` directory, then build it.
```
$ docker build -t YOUR_NAME/aws-xray-daemon:3.0.0 -f 3.0/Dockerfile .
```

### < v3.0.0

WIP

## Contribution

1. Fork ([https://github.com/toricls/aws-xray-daemon-container/fork](https://github.com/toricls/aws-xray-daemon-container/fork))
1. Create a feature branch
1. Commit your changes
1. Rebase your local changes against the master branch
1. Create a new Pull Request

## Licence

[Apache 2.0](LICENSE)

## Author

[toricls](https://github.com/toricls)

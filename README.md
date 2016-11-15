# Collectd Docker Image

This image provides a Dockerized collectd configuration to gather operating system statistics from the underlying Docker host instead of a container where it's launched.

The collectd agent started within the container is automatically configured to send metrics into your Axibase Time Series Database instance using the [write_atsd](https://github.com/axibase/atsd-collectd-plugin) plugin. The target ATSD instance is specified in the `--atsd-url` argument.

## Prepare Image

### Docker Hub

* Use an existing image published on [Docker Hub](https://hub.docker.com/r/axibase/collectd/)

```
docker pull axibase/collectd
```

### Build Image from Sources

* Download [Dockerfile](Dockerfile) to a Docker host connected to hub.docker.com

* Build image

```
docker build -t axibase/collectd .
```

### Download Image

* Download a pre-built image file from [axibase.com](https://axibase.com/public/docker-axibase-collectd.tar.gz)

* Import the image into the Docker host

```
docker load < docker-axibase-collectd.tar.gz
```

## Start Container

### Basic Configuration

* Start Docker container

```ls
docker run -d -v /:/rootfs:ro --pid=host --net=host \
    --name=collectd axibase/collectd \
    --atsd-url=tcp://atsd_host:tcp_port
```

### `lvs` Configuration

> This configuration gathers data from the `lvs` command and therefore requires additional privileges.

```ls
docker run -d -v /:/rootfs:ro --privileged=true \
    --pid=host --net=host \
    --name=collectd axibase/collectd \
    --atsd-url=tcp://atsd_host:tcp_port \
    --lvs
```

#### Tags encoding

Script used in `exec` plugin send plain text protocol `PUTVAL` commands to Collectd in the following format:


```ls
PUTVAL $HOSTNAME/exec-plugin/gauge-tag_key1=tag_value1;tag_key1=tag_value1 N:1479189638358
```

* tag keys and values are specified in `type_instance` field
* each pair of key and value inside should be delimited by equals sign
* pairs between each other should be separated by semicolons
* if there is at least one pair without equal sign then type_instance set to one tag with key `instance`

### Credits

* [Carles Amigó](https://github.com/fr3nd/docker-collectd) for the idea to re-mount rootfs.

# bypass4netns: Accelerator for slirp4netns using `SECCOMP_IOCTL_NOTIF_ADDFD` (Kernel 5.9)

bypass4netns is as fast as `--net=host` and _almost_ as secure as traditional slirp4netns.

The current version of bypass4netns needs to be used in conjunction with slirp4netns,
however, future version may work without slirp4netns.

The project name is still subject to change.

## Benchmark

Workload: `iperf3 -c HOST_IP` from `podman run`

- `--net=host` (insecure): 57.9 Gbps
- **bypass4netns**: **56.5 Gbps**
- slirp4netns: 7.56 Gbps

## How it works

To be documented. See the code :)

## Requirements
- kernel >= 5.9
- crun >= 0.15
- libseccomp >= 2.5
- Rootless Docker or Rootless Podman

## Compile

```console
$ LIBSECCOMP_PREFIX=/opt/libseccomp ./make.sh
```

## Usage

```console
$ ./bin/bypass4netns
```

```console
$ podman run -it --rm --runtime $(pwd)/test/crun-bypass4netns --security-opt seccomp=$(pwd)/test/seccomp.json alpine
```

## :warning: Caveats :warning:
Accesses to host abstract sockets and host loopback IPs (127.0.0.0/8) from containers are designed to be rejected.

However, it is probably possible to connect to host loopback IPs by exploiting [TOCTOU](https://elixir.bootlin.com/linux/v5.9/source/include/uapi/linux/seccomp.h#L81)
of `struct sockaddr *` pointers.

## TODOs
- Stop hard-coding `docker network create` CIDR (172.0.0.0/8) and `podman network create` CIDR (10.0.0.0/8)
- Accelerate port forwarding (`docker run -p` and `podman run -p`) as well
- Rewrite in Go, perhaps

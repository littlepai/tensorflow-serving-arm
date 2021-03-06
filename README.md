TensorFlow Serving on ARM
=========================

TensorFlow Serving cross-compile project targeting linux on common arm cores from
a linux amd64 (x86_64) host.

## Overview

**Upstream Project:** [tensorflow/serving](https://github.com/tensorflow/serving)

**Usage Documentation:** [TensorFlow Serving with Docker](https://www.tensorflow.org/tfx/serving/docker)

This project is basically a giant build wrapper around [tensorflow/serving](https://github.com/tensorflow/serving)
with the intention of making it easy to cross-build CPU optimized model server
docker images targeting common linux arm platforms. Additonally, a set of docker
images is produced for some of the most popular linux arm platforms and hosted on
Docker Hub.

## The Docker Images

**Hosted on Docker Hub:** [emacski/tensorflow-serving](https://hub.docker.com/r/emacski/tensorflow-serving)

### Quick Start

On many consumer / developer 64-bit and 32-bit arm platforms you can simply:
```sh
docker pull emacski/tensorflow-serving:latest
# or
docker pull emacski/tensorflow-serving:2.1.0
```

Refer to [TensorFlow Serving with Docker](https://www.tensorflow.org/tfx/serving/docker) for usage.

### Images

`emacski/tensorflow-serving:[Tag]`

| **Tag** | **ARM Core Compatability** |
|---------|----------------------------|
| <nobr>`[Version]-linux_amd64_avx_sse4.2`</nobr>| N/A |
| <nobr>`[Version]-linux_arm64_armv8-a`</nobr> | Cortex-A35 / A53 / A57 / A72 / A73 |
| <nobr>`[Version]-linux_arm64_armv8.2-a`</nobr> | Cortex-A55 / A75 / A76 |
| <nobr>`[Version]-linux_arm_armv7-a_neon_vfpv3`</nobr> | Cortex-A8 |
| <nobr>`[Version]-linux_arm_armv7-a_neon_vfpv4`</nobr> | Cortex-A7 / A12 / A15 / A17 |

Example
```bash
# on beaglebone black
docker pull emacski/tensorflow-serving:2.1.0-linux_arm_armv7-a_neon_vfpv3
```

### Aliases

`emacski/tensorflow-serving:[Alias]`

| **Alias** | **Tag** | **Notes** |
|-----------|---------|-----------|
| <nobr>`[Version]-linux_amd64`</nobr> | <nobr>`[Version]-linux_amd64_avx_sse4.2`</nobr> | default `linux_amd64` image |
| <nobr>`[Version]-linux_arm64`</nobr> | <nobr>`[Version]-linux_arm64_armv8-a`</nobr> | Should work on _most_ 64-bit<br/>raspberry pi and compatible<br/>platforms |
| <nobr>`[Version]-linux_arm`</nobr> | <nobr>`[Version]-linux_arm_armv7-a_neon_vfpv4`</nobr> | Should work on _most_ 32-bit<br/>raspberry pi and compatible<br/>platforms |
| <nobr>`latest-linux_amd64`</nobr> | <nobr>`[Latest-Version]-linux_amd64`</nobr> | |
| <nobr>`latest-linux_arm64`</nobr> | <nobr>`[Latest-Version]-linux_arm64`</nobr> | |
| <nobr>`latest-linux_arm`</nobr> | <nobr>`[Latest-Version]-linux_arm`</nobr> | |

Example
```bash
# on Raspberry PI 3 B+
docker pull emacski/tensorflow-serving:2.1.0-linux_arm64
# or
docker pull emacski/tensorflow-serving:latest-linux_arm64
```

### Manifest Lists

#### `emacski/tensorflow-serving:latest`

| **Image** | **OS** | **Arch** |
|-----------|:------:|:--------:|
| <nobr>`emacski/tensorflow-serving:latest-linux_arm`</nobr> | `linux` | `arm` |
| <nobr>`emacski/tensorflow-serving:latest-linux_arm64`</nobr> | `linux` | `arm64` |
| <nobr>`emacski/tensorflow-serving:latest-linux_amd64`</nobr> | `linux` | `amd64` |

Example
```bash
# on Raspberry PI 3 B+
docker pull emacski/tensorflow-serving
# the actual image used is emacski/tensorflow-serving:latest-linux_arm64
# itself actually being emacski/tensorflow-serving:[Latest-Version]-linux_arm64_armv8-a
```

#### `emacski/tensorflow-serving:[Version]`

| **Image** | **OS** | **Arch** |
|-----------|:------:|:--------:|
| <nobr>`emacski/tensorflow-serving:[Version]-linux_arm`</nobr> | `linux` | `arm` |
| <nobr>`emacski/tensorflow-serving:[Version]-linux_arm64`</nobr> | `linux` | `arm64` |
| <nobr>`emacski/tensorflow-serving:[Version]-linux_amd`</nobr> | `linux` | `amd64` |

Example
```sh
# on Raspberry PI 3 B+
docker pull emacski/tensorflow-serving:2.1.0
# the actual image used is emacski/tensorflow-serving:2.1.0-linux_arm64
# itself actually being emacski/tensorflow-serving:2.1.0-linux_arm64_armv8-a
```

### Debug Images

As of version 2.1.0, debug images are also built and published to docker hub.
These images are identical to the non-debug images with the addition of busybox
utils. The utils are located at `/busybox/bin` which is also included in the
image `PATH` env variable.

For any image above, add `debug` after the `[Version]` and before the platform
suffix (if one is required) in the image tag.

Examples
```sh
# multi-arch
docker pull emacski/tensorflow-serving:2.1.0-debug
# specific image
docker pull emacski/tensorflow-serving:2.1.0-debug-linux_arm64_armv8-a
# specific alias
docker pull emacski/tensorflow-serving:latest-debug-linux_arm64
```

```sh
# start a new container with an interactive ash (busybox) shell
docker run -ti --entrypoint /busybox/bin/sh emacski/tensorflow-serving:latest-debug-linux_arm64
# with an interactive dash (system) shell
docker run -ti --entrypoint sh emacski/tensorflow-serving:latest-debug-linux_arm64
# start an interactive ash shell in a running debug container
docker exec -ti my_running_container /busybox/bin/sh
```

## Building from Source

**Host Build Requirements:**
* git
* docker

### Build / Development Environment

```bash
git clone git@github.com:emacski/tensorflow-serving-arm.git

cd tensorflow-serving-arm

docker pull emacski/tensorflow-serving:latest-devel
# or
docker build -t emacski/tensorflow-serving:latest-devel -f tensorflow_model_server/tools/docker/Dockerfile .
```

### Build Examples

The following examples assume that the commands are executed within the `devel` container:
```bash
# interactive shell
docker run --rm -ti \
    -w /code -v $PWD:/code \
    -v /var/run/docker.sock:/var/run/docker.sock \
    emacski/tensorflow-serving:latest-devel /bin/bash
# non-interactive
docker run --rm \
    -w /code -v $PWD:/code \
    -v /var/run/docker.sock:/var/run/docker.sock \
    emacski/tensorflow-serving:latest-devel [example_command]
```

#### Build Project Docker Images
```bash
bazel build //tensorflow_model_server:linux_amd64_avx_sse4.2 --config=linux_amd64_avx_sse4.2
bazel build //tensorflow_model_server:linux_arm64_armv8-a --config=linux_arm64_armv8-a
bazel build //tensorflow_model_server:linux_arm64_armv8.2-a --config=linux_arm64_armv8.2-a
bazel build //tensorflow_model_server:linux_arm_armv7-a_neon_vfpv3 --config=linux_arm_armv7-a_neon_vfpv3
bazel build //tensorflow_model_server:linux_arm_armv7-a_neon_vfpv4 --config=linux_arm_armv7-a_neon_vfpv4
# each build creates a docker loadable image tar in bazel's output dir
```

#### Build and Load Project Images

```bash
bazel run //tensorflow_model_server:linux_amd64_avx_sse4.2 --config=linux_amd64_avx_sse4.2
bazel run //tensorflow_model_server:linux_arm64_armv8-a --config=linux_arm64_armv8-a
bazel run //tensorflow_model_server:linux_arm64_armv8.2-a --config=linux_arm64_armv8.2-a
bazel run //tensorflow_model_server:linux_arm_armv7-a_neon_vfpv3 --config=linux_arm_armv7-a_neon_vfpv3
bazel run //tensorflow_model_server:linux_arm_armv7-a_neon_vfpv4 --config=linux_arm_armv7-a_neon_vfpv4
```

**Note:** host docker must be available to the build container for final images
to be available on the host automatically.

#### Build Project Binaries
It's not recommended to use these binaries as standalone executables as they are built specifically to run in their respective containers.
```bash
bazel build //tensorflow_model_server --config=linux_amd64_avx_sse4.2
bazel build //tensorflow_model_server --config=linux_arm64_armv8-a
bazel build //tensorflow_model_server --config=linux_arm64_armv8.2-a
bazel build //tensorflow_model_server --config=linux_arm_armv7-a_neon_vfpv3
bazel build //tensorflow_model_server --config=linux_arm_armv7-a_neon_vfpv4
```

#### Build Docker Image for Custom ARM target
Just specify the `image.tar` target and base arch config group and custom copile options.

For `linux_arm64` and `linux_arm` options see: https://releases.llvm.org/9.0.0/tools/clang/docs/CrossCompilation.html

Example building an image tuned for Cortex-A72
```bash
bazel build //tensorflow_model_server:image.tar --config=linux_arm64 --copt=-mcpu=cortex-a72
# resulting image tar: bazel-bin/tensorflow_model_server/image.tar
```

## Disclaimer

* Not an ARM expert
* Not a Bazel expert (but I know a little bit more now)
* Not a TensorFlow expert
* Personal project, so testing is minimal

Should any of those scare you, I recommend NOT using anything here.
Additionally, any help to improve things is always appreciated.

## Legacy Builds

### Legacy GitHub Tags (prefixed with `v`)
* `v1.11.1`
* `v1.12.0`
* `v1.13.0`
* `v1.14.0`

**Note:** a tag exists for both `v1.14.0` and `1.14.0` as this was the current upstream tensorflow/serving version when this project was refactored

### Legacy Docker Images
The following tensorflow serving versions were built using the legacy project
structure and are still available on DockerHub.
* `emacski/tensorflow-serving:[Version]-arm64v8`
* `emacski/tensorflow-serving:[Version]-arm32v7`
* `emacksi/tensorflow-serving:[Version]-arm32v7_vfpv3`

Versions: `1.11.1`, `1.12.0`, `1.13.0`, `1.14.0`

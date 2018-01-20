---
layout: post
title: Building multi-arch docker images
date: 2018-01-18
categories:
tags:
comments: true
---
You built some Docker images for your laptop, but also for bunch of those Raspberry/Orange/Banana Pies you got around? You hate building an image per platform, tagging them, remembering tag and then matching tag to your architecture... All of this sound too complicated?  
It is! But, it can be simpler.

# TL; DR

```
cd /usr/src
git clone https://github.com/lobradov/docker-multiarch-builder.git
cd docker-multiarch-builder
./run-once.sh


cd /usr/src/
mkdir my-new-docker-project
/usr/src/docker-multiarch-builder/init-repo.sh my-new-docker-project/

cd my-new/docker-project
vi build.config
vi Dockerfile.cross
./build.sh
```

# How does it work?

There's a number of highly technical explanations out there that describe how whole multi-arch thing works in Docker, so I'm not going to waste your time on that.

What you should know is following:
- Docker has something called "fat manifest", which is a "virtual image" covering multiple architectures and pointing to real images.
- If you just want to **run** multi-arch images, you don't need to worry: Automatic selection of images is supported by Docker registry and Docker client/daemon, so you are good. But this is not why you are here.
- In order to **build** images, you'd need a special docker CLI that supports new `docker manifest` command. Hopefully, this will get merged to upstream soon, but until then, you have to build it yourself.
- Building multi-arch images require push rights to some docker registry, either your own or public (hub.docker.com). Make sure you are logged in.

I'm assuming you are using X86_64 machine to as **build host**, and it can run either Linux or Mac OS (Darwin).

# Getting necessary tools

## Building a "manifest enabled" Docker CLI

```bash
git clone -b manifest-cmd https://github.com/clnperez/cli.git
cd cli
make -f docker.Makefile cross
export PATH=${PATH}:`pwd`/build
```
This will give you number of docker CLI binaries in `build/` directory and add them to your path.

> Update: Seems of last night, PR for merging this functionality into upstream got accepted, so official docker client will have `manifest` command. Still, as of today, instructions to build and enable experimental are complicated than the above, so let's keep it as such.

## `binfmt_misc` for target architectures

In order to run and build images for other architectures, we're going to use a trick called `binfmt_misc`.  
There's a lot of materials on "why", but here's a quick "how".

### Registering handlers

You can skip this section if you are running on a Mac OS - Docker for Mac already has these handlers registered.

You need to do this step only once per host (build) machine.

```bash
docker run --rm --privileged multiarch/qemu-user-static:register
```

### Getting handlers

In the Docker build directory of your project, do:
```bash
for target_arch in aarch64 arm x86_64; do
  wget -N https://github.com/multiarch/qemu-user-static/releases/download/v2.9.1-1/x86_64_qemu-${target_arch}-static.tar.gz
  tar -xvf x86_64_qemu-${target_arch}-static.tar.gz
done
```
Feel free to customize the list in `target_arch` according to how many target architectures are you going to build for.
Complete list of available handlers (and their versions) is here:
https://github.com/multiarch/qemu-user-static/releases

You will very likely either run this section for every build project, or you will smartly play with links/hard links to save some space and bandwidth.

# Creating your Dockerfile(s)

## Differnet Dockerfile per architecture

You can create separate `Dockerfile.<arch>` for each of the architectures. This would allow you to have different build procedures for each of the archs (so you can push different package repositories). For example:

Dockerfile.amd64:
```
FROM amd64/alpine:3.7
# Not necessary for the arch where host and target are the same
# COPY qemu-x86_64-static /usr/bin/
RUN apk --no-cache --update add nginx
EXPOSE 80
CMD ["nginx", "-g", "deamon off;"]
```

Dockerfile.arm32v6:
```
FROM arm32v6/alpine:3.7
COPY qemu-arm-static /usr/bin/
RUN apk --no-cache --update add nginx
EXPOSE 80
CMD ["nginx", "-g", "deamon off;"]
```

Dockerfile.arm64v8:
```
FROM arm64v8/alpine:3.7
COPY qemu-aarch64-static /usr/bin/
RUN apk --no-cache --update add nginx
EXPOSE 80
CMD ["nginx", "-g", "deamon off;"]
```

You'll notice 2 things:
- we added static Qemu binary to the image.
- docker notation of architecture is different than Qemu (and `uname -m`) notation, so we might need to apply some translations. Hopefully, following table would help:

| Docker architecture | `uname -m` architecture | Note             |
| ------------------- | ----------------------- | ---------------- |
| amd64               | x86_64                  |                  |
| arm32v6             | armhf, arm7l            | Raspberry Pis    |
| arm64v8             | aarch6                  | A53, H3, H5 ARMs |

Take it with a grain of salt - some Docker baseimages don't provide all variants, and arm32v7 is backwards compatible with arm32v6, so for `library/alpine`, you will actually use arm32v6 variant for all Raspberry Pies.  
I said things were complicated and could be made simpler, but not completely simple.

## Same Dockerfile template

Example above wasn't good, because every image builds the same, so we can do it smarter by creating a template and building actual Dockerfile.<arch> from them:

Dockerfile.cross:
```
FROM __BASEIMAGE_ARCH__/alpine:3.7

__CROSS_COPY qemu-__QEMU_ARCH__-static /usr/bin/
RUN apk --no-cache --update add nginx
EXPOSE 80
CMD ["nginx", "-g", "deamon off;"]
```

and a simple build.sh:
```bash
for docker_arch in amd64 arm32v6 arm64v8; do
  case ${docker_arch} in
    amd64   ) qemu_arch="x86_64" ;;
    arm32v6 ) qemu_arch="arm" ;;
    arm64v8 ) qemu_arch="aarch64" ;;    
  esac
  cp Dockerfile.cross Dockerfile.${docker_arch}
  sed -i "" "s|__BASEIMAGE_ARCH__|${docker_arch}|g" Dockerfile.${docker_arch}
  sed -i "" "s|__QEMU_ARCH__|${qemu_arch}|g" Dockerfile.${docker_arch}
  if [ ${docker_arch} == 'amd64' ]; then
    sed -i "" "/__CROSS_/d" Dockerfile.${docker_arch}
  else
    sed -i "" "s/__CROSS_//g" Dockerfile.${docker_arch}
  fi
done
```

This selects appropriate base images and Qemu archs (but doesn't yet check if they all exist and all - that's your homework ;) and also removes unnecessary qemu for amd64.

# Building and tagging individual images

Now that we have individual Dockerfiles, it's easy to build images, tag them and push them:
```bash
for arch in amd64 arm32v6 arm64v8; do
  docker build -f Dockerfile.${arch} -t yourrepo/nginx:${arch}-latest .
  docker push yourrepo/nginx:${arch}-latest
done
```

With this, you'll have yourrepo/nginx:amd64-latest, yourrepo/nginx:arm32v6-latest and yourrepo/nginx:arm64v8-latest in your repo.

# Building a multi-arch manifest

Now, we want to create a single yourrepo/nginx:latest that would magically work in any of the archs. To do that we need to create a manifest:
```bash
docker-linux-amd64 manifest create yourrepo/nginx:latest yourrepo/nginx:amd64-latest yourrepo/nginx:arm32v6-latest yourrepo/nginx:arm64v8-latest
docker-linux-amd64 manifest annotate yourrepo/nginx:latest yourrepo/nginx:arm32v6-latest --os linux --arch arm
docker-linux-amd64 manifest annotate yourrepo/nginx:latest yourrepo/nginx:arm64v8-latest --os linux --arch arm64 --variant armv8
docker-linux-amd64 manifest push yourrepo/nginx:latest
```
(use docker-darwin-amd64 if you are on a Mac)

You will notice that we have to annotate some images, to give hints to Docker client/daemon to understand which images to pull. Again, some translations needed, so here's the upper table once again:

| Docker architecture | `uname -m` architecture | Annotate flage   | Boards           |
| ------------------- | ----------------------- | ---------------- | ---------------- |
| amd64               | x86_64                  | (none)           |                  |
| arm32v6             | armhf                   | Raspberry Pis   | `--os linux --arch arm` |
| arm64v8             | aarch6                  | A53, H3, H5 ARMs | `--os linux --arch arm64 --variant armv8` |

If you compiled for other architectures and know other combinations, let me know!

# Result

If everything went well, you should have multi-arch `yourrepo/nginx:latest` that should be deployable on any of the 3 architecture using a same command:
```bash
docker run -d --rm yourrepo/nginx:latest
```

It will just pull necessary image depending on your host arch and just run. You can even create a multi-instance docker service on a multi-arch swarm, and each swarm member would get same service from a different image (check out "From ARM to Z" DockerCon17 talk on Youtube.)


# References for further reading

- [Christy Perez of IBM](https://developer.ibm.com/linuxonpower/2017/07/27/create-multi-architecture-docker-image/)
- [From ARM to Z - talk from DockerCon17](https://youtu.be/nrBYUw1Pz5I)
- [Maartje Eyskens blog](https://eyskens.me/multiarch-docker-images/)

---
title: Building multiarch images with Buildah
subtitle: See how to build container images for platforms other than amd64

# Summary for listings and search engines
summary: With the growth and popularization of other platforms beyond amd64, especially the ARM platform, it becomes a concern for those maintaining container images, the construction of these tailored for these platforms.

# Link this post with a project
projects: []

# Date published
date: '2023-09-22T19:00:00Z'

# Date updated
lastmod: '2023-09-22T19:00:00Z'

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Logo from Buildah (a dog wearing a constructor hat), an icon representing the OCI (Open Container Initiative) standard and an icon representing a built container image'
  focal_point: ''
  placement: 2
  preview_only: false

authors:
  - admin

tags:
  - containers
  - docker
  - podman
  - buildah

categories:
  - SRE
---

With the growth and popularization of platforms beyond amd64, especially the ARM platform, it becomes a concern for those maintaining container images to build them tailored for these platforms as well.

In this article, I will show you how to build these images using [Buildah](https://buildah.io/).

Buildah is an open-source project led by RedHat (but receives contributions from other companies) that aims to simplify the construction of container images that adhere to and are compatible with the [OCI (Open Container Initiative)](https://opencontainers.org/) standard.

So let's get to the steps.

## Prerequisites

- Installation of the Buildah package
- Installation of the `qemu-user-static` package (Ubuntu/RedHat) or `qemu-arch-extra` package (Arch Linux);

`sudo dnf install -y buildah qemu-user-static` (Fedora and derivatives)

`sudo apt-get install -y buildah qemu-user-static` (Ubuntu/Debian and derivatives)

`pacman -Sy buildah qemu-arch-extra` (Arch Linux and derivatives)

## Building the images

The workflow you need to follow to build the images is as follows:

1. Create the manifest

```bash
export MANIFEST="multiarch-image" # manifest name, can be any name
export IMAGE="registry/repository/image:tag" # image name, e.g., docker.io/ozorest/example:latest
buildah manifest create "$MANIFEST"
```

2. Run the build for each architecture

In the directory with your Dockerfile, you can run the following loop for each architecture:

```bash
for arch in amd64 arm64; do
  buildah build --arch $arch --tag "$IMAGE" --manifest "$MANIFEST" .
  # buildah build --arch $arch --tag "$IMAGE" --manifest "$MANIFEST" -f path_to_Dockerfile, in case the Dockerfile is not in the current directory
done
```

3. Push the manifest to the registry

```bash
buildah manifest push --all "$MANIFEST" "docker://$IMAGE"
```

And that's it, folks! Stay tuned for more content!
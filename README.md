# Reproduction for Buildkit Issue 2204

This repo contains two Dockerfiles (boringly named `Dockerfile.1` and `Dockerfile.2`) the first builds an image, and the second builds an image from a provided upstream.  This works fine if you do something like:

```
$ docker build -t first-image -f Dockerfile.1 .
$ docker build --build-arg UPSTREAM=first-image -f Dockerfile.2 .
```

But fails if you do:

```
$ docker build --iidfile=./image_id -f Dockerfile.1 .
$ docker build --build-arg UPSTREAM=$(cat ./image_id) -f Dockerfile.2 .
[+] Building 1.2s (4/4) FINISHED
 => [internal] load build definition from Dockerfile.2                                                                                                                                                                                  0.0s
 => => transferring dockerfile: 102B                                                                                                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                       0.1s
 => => transferring context: 2B                                                                                                                                                                                                         0.0s
 => ERROR [internal] load metadata for docker.io/library/sha256:06017009f887cf12ab636d362e9c0170acac7735a1cc0ee786e720385a37d619                                                                                                        1.0s
 => [auth] library/sha256:pull token for registry-1.docker.io                                                                                                                                                                           0.0s
------
 > [internal] load metadata for docker.io/library/sha256:06017009f887cf12ab636d362e9c0170acac7735a1cc0ee786e720385a37d619:
------
error: failed to solve: rpc error: code = Unknown desc = failed to solve with frontend dockerfile.v0: failed to create LLB definition: pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
```

Both models work fine with the buildkit builder disabled (e.g. `DOCKER_BUILDKIT=0`).

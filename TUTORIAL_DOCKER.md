# Minimal Docker Tutorial

Docker is a system for running containers: lightweight, isolated environments that share the host kernel. Compared to virtual machines (which emulate a full OS), containers are usually more lightweight.

If you are new to Docker, start with the official intro: [Docker 101 Tutorial](https://www.docker.com/101-tutorial/).

## Core Concepts
- **Image**: a read-only template used to create containers (application code, libraries, environment variables, configuration, ...). Images are built from a `Dockerfile`.
- **Container**: a running instance of an image. You can think of it as a “small computer process”. Containers are typically ephemeral (not meant for persistent storage).
- **Volume**: persistent storage that can be attached (“mounted”) into a container.
- **Docker Compose**: defines multi-container applications (via `docker-compose.yml`). Often not required for single-container development setups.
- **Registry**: storage/distribution for images (default: Docker Hub).

## Dockerfiles and Building Images
Images (and thus containers) are built from Dockerfiles. A `Dockerfile` is a recipe; common instructions are:
- `FROM`: base image
- `RUN`: execute build steps (install packages, compile, ...)
- `COPY`: copy files into the image

To build an image you run `docker build`: 

    docker build -t <image_name> <build_folder>

where `<image_name>` is a tag that is given to the image and `<build_folder>` is the folder containing the `Dockerfile`. Docker uses a build cache. This is great for iterating on Dockerfiles because earlier steps stay cached. The cache is invalidated when relevant inputs change (e.g. the input files for `COPY` commands) or when the commands in the Dockerfile change. However, for some external inputs the cache cannot know whether they have changed, e.g. calls to `apt install` or `git clone/pull`. In this case, if you want to force a clean build, use `docker build --no-cache`.

After building, the container can be started and run with 

    docker run <image_name>

A more feature-rich example (X11 GUI pass-through, host networking, privileged mode):

    docker run -it --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host --privileged <image_name>

Common flags:
- `-it`: interactive + TTY (opens a terminal inside the container)
- `-e KEY=VALUE`: set environment variables (e.g., `DISPLAY` for X11)
- `-v <host_path>:<container_path>`: bind-mount a volume from the host filesystem
- `--net=host`: share the network namespace with the host
- `--privileged`: run in privileged mode (required for some hardware/RT setups)
- `--rm`: delete the container after it exits (useful for one-off testing or if you don't need persistent files)
- `--runtime=nvidia --gpus all `: enable GPUs (requires nvidia container toolkit)
- `--user $(id -u):$(id -g)`: run with the host UID/GID (helps with file permissions)

### Naming Containers
You can name containers when starting them:

    docker run --name <container_name> <image_name>

or rename later:

    docker rename <old_container_name> <new_container_name>

## Managing Existing Containers
List containers:

    docker ps -a

The ID or name found with this command can be used to start an existing container (e.g. after a reboot):

    docker start <container-id/name>

Open an interactive shell in a running container:

    docker exec -it <container-id> /bin/bash

## Disk Usage and Cleanup
Building and running containers sometimes produces a large amount of disk usage.
Disk usage overview:

    docker system df

Remove containers/images:

    docker rm <CONTAINER-ID>
    docker image list
    docker image remove <image-id>


General cleanup commands to free disk space:

    docker system prune

    docker image prune
    docker container prune
    docker volume prune
    docker builder prune

## Good-to-Know Quirks
- The local image repository is shared across Docker users on the machine. If two users tag images with the same name, the tag will point to the newest one.
- The build cache does not detect “upstream” changes (e.g., a new package version available via `apt`, or a changed `git clone` target). In those cases, rebuild with `docker build --no-cache`.


## Building Environments / Software Stacks with Dockerfiles
Often you want to package a software stack (for example, building and installing a GitHub project). This can require specific versions, ordering, environment variables, config files, etc.

The typical workflow is:
1. Start from a suitable `FROM` base image.
2. Translate your manual setup commands into `RUN` steps.
3. Build (`docker build ...`), fix errors, iterate.

Advantages of Docker images:
- The build process is replicable and mostly deterministic (with caveats around moving package repositories and unpinned Git repos).
- The build cache enables fast iteration.
- The Dockerfile acts like a “notebook” of what was needed to get a working environment.
- You avoid hidden artifacts from previous failed installs.
- If an image works today, it tends to keep working (as long as inputs are stable/pinned).




## Tips

### Running graphical applications (X11)
Add this to your `~/.bashrc` to enable X11 forwarding:

```bash
if command -v xhost >/dev/null 2>&1 && [ -n "$DISPLAY" ]; then
    xhost +local:docker >/dev/null 2>&1
fi
```

Then run containers with the X11-related flags `-e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix`, e.g.:

    docker run -it --rm -v /cshome:/cshome -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host --privileged deoxys_autostart


### Making SSH keys available at build time (TODO)
Minimal BuildKit example:

    DOCKER_BUILDKIT=1 docker build --ssh default -t molmo_grasping docker_molmo_grasping_pipeline/

In the Dockerfile:

```dockerfile
# syntax=docker/dockerfile:1.4

# Add github.com to known_hosts
RUN mkdir -p -m 0700 ~/.ssh && \
    ssh-keyscan -H github.com >> ~/.ssh/known_hosts

RUN --mount=type=ssh git clone git@github.com:David0tt/franka_pipeline.git
```



## Developing inside containers with VS Code
The `VS Code Remote Development` extension is great for development inside containers.

Basic workflow:
1. Install the “Remote Development” extension in VS Code.
2. Make sure the container is running.
3. In VS Code, click the blue connection indicator `><` in the bottom-left.
4. Select “Attach to Running Container”, then open the folder you want.

Important note: changes stored only in the container filesystem are lost if the container is removed.
- Semi-permanent storage: keep using the same container for development (do not delete it), i.e., run without the `--rm` flag.
- Permanent storage: commit important changes to a git repository.
- If you lose the container: rebuild the image and re-clone your Git repo.

### Pitfall: naive bind-mount development
You can bind-mount the source code into a container via `-v`, but this can cause permission issues because the container user may not match the host user.

A common fix is to create a container user with the same UID/GID as the host. However, this can create additional privilege issues (e.g., real-time privileges not propagating cleanly).



## Realtime / Lowlatency Systems
In general a Docker container inherits the kernel of the host. Therefore, if realtime or lowlatency is available on the host, it is also available in the container.

Whether the realtime capabilities suffice can be tested by examining the kernel in the container using 

    uname -a


One naive test:
- `cyclictest` measures latency for a high-priority task.
- Running `stress-ng` in parallel adds CPU load.
- Compare latency:
  - with and without `stress-ng`
  - inside and outside the container

Commands:

    sudo apt install rt-tests
    sudo cyclictest --smp --priority=99 --interval=1000 --distance=0 --duration=60

    sudo apt install stress-ng
    sudo stress-ng --cpu 4 --io 4 --vm 2 --vm-bytes 512M --timeout 60s




More detailed descriptions and testing are shown in https://github.com/David0tt/RealtimeSystemsAndTesting and more background on realtime systems is presented in https://github.com/2b-t/linux-realtime


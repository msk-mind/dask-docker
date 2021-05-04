# Dask docker images

[![Build Status](https://travis-ci.com/dask/dask-docker.svg?branch=main)](https://travis-ci.com/dask/dask-docker)

Docker images for dask-distributed.

1. Base image to use for dask scheduler and workers
2. Jupyter Notebook image to use as helper entrypoint

This images are built primarily for the [Dask Helm Chart](https://github.com/dask/helm-chart)
but they should work for more use cases.

## How to use / test

A helper docker-compose file is provided to test functionality.

```
docker-compose up
```

If you want to scale to 4 workers:
```
docker-compose up --scale worker=4 -d
```

# To do a grid deployment using docker swarm
To run workers in a docker container on a different machine, its easist to use docker networks such that ports are automatically visible to the scheduler.

On main node…
```
docker swarm init --advertise-addr=<your machine's IP>
```
On additional worker nodes
```
docker swarm join … (using the token and IP)
```
Check for nodes you added
```
docker node ls
```
Create overlay network based on swarm network (on leader node):
```
docker network create --driver=overlay --attachable dask-network
```
Bring up main services:
```
docker-compose -f main/docker-compose.yml up -d
```
Bring up worker service to test:
```
docker-compose -f workers/docker-compose.yml up
```
Start worker service:
```
docker stack deploy --compose-file workers/docker-compose.yml grid
```
This will bring up 3 worker processes, with large and small definitions having 4, 64; and 16, 16; processes and memory, each; respectively. 

Scale to e.g., 3 services, or one for each machine in a 3 node cluster
```
docker service scale grid_worker-large=3
docker service scale grid_worker-small=3
```

Open the notebook using the URL that is printed by the output so it has the token.

On a new notebook run:

```python
from dask.distributed import Client
client = Client('scheduler:8786')
client.ncores()
```

It should output something like this:

```
{'tcp://172.23.0.4:41269': 12}
```

## Environment Variables

The following environment variables are supported for both the base and notebook images:

* `$EXTRA_APT_PACKAGES` - Space separated list of additional system packages to install with apt.
* `$EXTRA_CONDA_PACKAGES` - Space separated list of additional packages to install with conda.
* `$EXTRA_PIP_PACKAGES` - Space separated list of additional python packages to install with pip.

The notebook image supports the following additional environment variables:

* `$JUPYTERLAB_ARGS` - Extra [arguments](https://jupyter-notebook.readthedocs.io/en/stable/config.html) to pass to the `jupyter lab` command.


## Building images

Docker compose provides an easy way to building all the images with the right context

```
docker-compose build

# Just build one image e.g. notebook
docker-compose build notebook
```

## Releasing

Building and releasing new image versions is done automatically via Travis CI. When new commits are
pushed to the ``main`` branch images are built with the `dev` tag and pushed to Docker Hub.

When a new version of Dask is released a PR should be raised to bump the versions in
the `Dockerfile`s and then once that has been merged a new tag matching the Dask version
should be pushed. Travis will then build the images and push them with version tags and update
`latest` too.

```console
$ git commit --allow-empty -m "bump version to x.x.x"
$ git tag -a x.x.x -m 'Version x.x.x'
$ git push upstream main --tags
```

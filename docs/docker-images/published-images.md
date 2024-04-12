# Published docker images

Below you can find list of our repositories and images that are inside them. 

Everything is hosted on our public docker hub organisation: [Smart Squid](https://hub.docker.com/orgs/smartsquid/repositories)

### Build Server - Mini back-end to compile files locally

Docker Hub link: https://hub.docker.com/r/smartsquid/build-server/tags

Image: 
```bash
docker pull smartsquid/build-server
```

How to run locally:
```bash
docker run -it -p 8080:8080 smartsquid/build-server
```

--------

### IDE Web - main web implementation of the ide app

Docker Hub link: https://hub.docker.com/r/smartsquid/ide-web/tags

Image:
```bash
docker pull smartsquid/ide-web:latest
```

------

### Rust builder toolset - image with rust and other tools preinstalled for ink! development

Docker Hub link: https://hub.docker.com/r/smartsquid/smartsquid-builder-slim-buster/tags

Image:
```bash
docker pull smartsquid/smartsquid-builder-slim-buster:1.77.0
```

We support many different versions of the rust toolchain, make sure that you have selected proper image tag




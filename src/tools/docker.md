# Docker

## Installation

```sh
curl -fsSL get.docker.com | sudo bash
```

## None-Root

```sh
sudo usermod -aG docker $USER
```

## Snippets
```sh
# Run container then enter shell
docker run -it --rm -v `pwd`/data:/data -p 3000:3000 node:16 bash

# Run container as service
docker run -d --name redis -p 6379:6379 redis:5

# Exec in container
docker exec -it redis bash
docker exec redis bash -c 'redis-cli set k1 a && redis-cli get k1'

# View log
docker logs --tail 50 -f redis

# Clean
docker system prune -f

# Clean dead images
docker rmi $(docker images --filter "dangling=true" -q --no-trunc)
```

## Use Mirror

- WIN: Docker Desktop -> Preferences -> Docker Engine
- LINUX: /etc/docker/daemon.json

```json
{
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```

## Dockerfile
### COPY vs ADD

- `ADD` copy files, download remote resources, handle zip/tar.gz
- `COPY` only copy local files

Do not use `ADD` unless `COPY` not works.

```DOckerfile
ADD /source/file/path  /destination/path
ADD http://source.file/url  /destination/path
ADD source.file.tar.gz /destination

COPY /source/file/path  /destination/path
```

### ENTRYPOINT vs CMD

- No ENTRYPOINT

| Cmd        | Effect             |
| :--------- | :----------------- |
|            | error, not allowed |
| CMD ["c1"] | c1                 |
| CMD c1     | /bin/sh -c c1      |

- ENTRYPOINT e1 

| Cmd        | Effect        |
| :--------- | :------------ |
|            | /bin/sh -c e1 |
| CMD ["c1"] | /bin/sh -c e1 |
| CMD c1     | /bin/sh -c e1 |

- ENTRYPOINT ["e1"]

| Cmd        | Effect           |
| :--------- | :--------------- |
|            | e1               |
| CMD ["c1"] | e1 c1            |
| CMD c1     | e1 /bin/sh -c e1 |

### Conconcate commands

```Dockerfile
RUN set -ex \
    # comment
    && apt-get update && apt-get install -y curl --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* 
```

### Layers

```Dockerfile
FROM sigoden/node:native as builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --prod

FROM sigoden/node:slim
WORKDIR /app
COPY --from=builder /app .
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

### Heredoc

```Dockerfile
RUN printf '#!/bin/bash\n\
echo 1\n\
echo 2'\
>> /tmp/hello
```

### Enrypoint file

```sh
#!/bin/sh
set -e

if [ "${1#-}" != "${1}" ] || [ -z "$(command -v "${1}")" ]; then
  set -- app "$@" # change app to your binary
fi

exec "$@"
```

### Normal User

```Dockerfile
RUN groupadd --gid 1000 user \
  && useradd --uid 1000 --gid user --shell /bin/bash --create-home user
```

### Multi-Arch Build

```
docker buildx create --name mybuilder --driver docker-container
docker buildx use mybuilder
```

```
docker buildx build \
  --platform linux/amd64,linux/arm64,linux/arm/v7,linux/386 \
  -t demo . 
```
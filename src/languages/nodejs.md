# Nodejs

## Installation

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

nvm i --lts

nvm i --lts --reinstall-packages-from=node
```

## 

| name              | explain                   |
| ----------------- | ------------------------- |
| npm-check-updates | check updates             |
| pkg               | pack as single executable |
| pm2               | process managment         |
| zx                | js like bash              |
| prettier          | code style                |
| yargs             | cli framework             |
| leran             | monorepo                  |

## NPM

```sh
# list all packages
npm ls -g --depth=0
# init projects
npm i -y
```

## Run node service

1. Use pm2
```sh
pm2 start --name app npm start
```

2. Use systemd

```
[Unit]
After=network.target

[Service]
Environment=NODE_PORT=3000
Type=simple
User=ubuntu
ExecStart=/usr/bin/node /home/ubuntu/app.js
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```
sudo cp app.service /etc/systemd/system/
sudo systemd daemon-reload
sudo systemd start app
```

3. Use docker

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

## Native addon

1. INstall deps like gcc, make, python

```sh
# ubuntu
apt install build-essential

# alpine
apk add make gcc g++ python3 git
```

2. Test native addon

```
npm i --build-from-source bcrypt
```

## ES features

[https://node.green](https://node.green)

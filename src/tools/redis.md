# Redis

## Clear all

```sh
EVAL "return redis.call('del', unpack(redis.call('keys', ARGV[1])))" 0 prefix:*
```

## Enter redis container

```sh
#!/bin/bash -e
server=$(docker ps | grep redis | awk  '{print $1}')
docker exec -it $server redis-cli -a ${1:-pass}
```
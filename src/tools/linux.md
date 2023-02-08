# Linux 

## Inspect

| Aspect     | Command                         |
| ---------- | ------------------------------- |
| distro     | `lsb_release -a`                |
| kernel     | `uname -a`                      |
| mem        | `free -m`                       |
| fs         | `df -h`                         |
| net        | `ip -4 a`                       |
| disk       | `lsblk`                         |
| CPU        | `lscpu`                         |
| port       | `netstat -tulpn \| grep LISTEN` |
| process    | `top`                           |
| trafic     | `vnstat -i eth0 -l`             |
| ip         | `curl ipinfo.io`                |
| timezone   | `cat /etc/timezone`             |
| login user | `whoami`                        |

## File

```sh
# List files
ls -alh file

# Modify user group
chown -R alice file
chown -R alice:www-data file

# Modify perms
chmod 600 file
chmod -w file
chmod +x file
chmod o-x file
chmod u+w file

# Open with default app
xdg-open file

# link file
ln -s /user/bin/vim /usr/bin/vi
# Link file relative
ln -sr node_modules/eslint/bin/eslint.js node_modules/.bin/eslint

# Zip
zip -r data.zip data
# Inspect zip files
unzip -t data.zip
# Unzip file
unzip data.zip
# Unzip to specific folder
unzip -d output-dir data.zip
# Unzip with pass
zip -er data.zip data

# Tar
tar -cf data.tar.gz data
# Inspect tar files
tar -tf data.tar.gz
# Untar
tar -xf data.tar.gz
# Untar to specific folders
tar -xf data.tar.gz -C output-dir

# bz format compression (slower but smaller)
tar -cjf data.tar.bz2 data

# Download
wget $url
wget -O /tmp/file $curl
curl -o /tmp/file $curl

# Find
find -name README.md
find / -name sfz
find -type -f -name '*.log' -exec rm -rf {} \;

# split by number of copies
split -d -n 5 data.zip data.zip.part
# split by size
split -d -b 256M data.zip data.zip.part
# Combine split files
cat data.zip.part* > data.zip

# generate file
dd if=/dev/zero of=/tmp/1G.bin bs=1M count=1000
dd if=/dev/urandom of=/tmp/1G.bin bs=10M count=1000
```
## User/Group

```sh
# add users (more complete, similar to interface operations)
adduser alice
# add user (recommended version)
useradd -m alice
# add user, specify user directory
useradd -d /d/alice alice
# add user, specify uid, gid
useradd -u 1001 -g 1001 nodejs
# add program account
useradd -r -s /usr/sbin/nologin nodejs

# MODIFY THE USER SHELL
usermod -s /usr/bin/bash alice

# user join group
usermod -a -G docker alice

# modify user password
passwd alice

# delete users
userdel nodejs

# add group
groupadd -g 344 linuxde
# delete group
groupdel linuxde
```

## Process

```sh
# find process
ps aux | grep nginx

# kill process
kill $pid
# kill process forcelly
kill -9 $pid
# kill process by name
pkill nginx
pkill '^ssh$'
pkill -9 nginx

# find process by port
lsof -i:3000
netstat -utlpn | grep 3000
# find port by process
netstat -utlpn | grep nginx

cat /proc/$pid/status | grep VmRSS
cat /proc/$pid/cmdline 
cat /proc/$pid/environ 

ls -l /proc/$pid/cwd
ls -l /proc/$pid/exe
ls -l /proc/$pid/root

# run in backgroud
nohup server &
nohup server > server.log 2>&1 &
nohup server > server.log 2> server.err &
```

## Text

```sh
# count lines
cat file | wc -l

# first n lines
cat file | head -3
# last n lists
cat file | tail -3

cat file | more

# hex
cat file | xxd 
cat file | xxd -p > file.hex

# split columns with `:`, only show first, third columns
cat /etc/group | cut -d: -f1,3
# only show 5-10 charactors of each row
cat file | cut -c5-10
# only show first 10 charactors of each row
cat file | cut -c-10
# only show 10th and subsequent of each line
cat file | cut -c10-

# replace charactor
cat file | tr '\t' ' '
# delete charactor
cat file | tr -d '\r'

# remove duplicates
cat file | sort | uniq 
# count duplicates
cat file | sort | uniq -c
# show duplicates
cat file | sort | uniq -d
```

### sed

```sh
# delete first line
cat file | sed '1d'
# delete empty line
cat file | sed '/^$/d'
# insert at first line
cat file | sed '1i#!/usr/bin/env node\n'
# append on last line
cat file | sed '$a\\n'
# replace functions
cat file | sed '/foo() {/,/}/c foo() { }'
# replace path
cat file| sed 's|/bin/sh|/bin/bash|g'
# replace with matched parts
cat file | sed 's|\(\w\+\) \(\w\+\) \(\w\+\)|\2 \3 \1|'
# change file
sed -i 's/\w\+.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list
```

### awk

```sh
# print specific column
docker images | awk '{print $1}'
# specific delimeter
cat /etc/paswd | awk -F: '{print $1}'
# combine columns
cat file | awk '{print $1,$3}'
```

## Regex

| Code   | Explain           |
| ------ | ----------------- |
| `^`    | start             |
| `$`    | end               |
| `*`    | zero or many      |
| `\?`   | zero or one       |
| `\+`   | at least one      |
| `\{\}` | match n times     |
| `\|`   | choices           |
| `\(\)` | group             |
| `[]`   | match options     |
| `[^]`  | not match options |
| `[-]`  | options hyper     |
| `.`    | any char          |
| `\w`   | word char         |
| `\s`   | empty char        |
| `\S`   | none-empty char   |
| `\b`   | boundary          |
| `\<\>` | boundary          |

## GLOB

| Code          | Explain           |
| ------------- | ----------------- |
| `*`           | any               |
| `[]`          | match options     |
| `[^]`         | not match options |
| `{str1,str2}` | choices           |

## Terminal

| hotkey | desc                 |
| ------ | -------------------- |
| ctrl+a | to head of line      |
| ctrl+e | to end of line       |
| alt+f  | move forward a word  |
| alt+b  | move backward a word |
| ctrl+w | delete word backward |
| alt+d  | delete word forward  |
| ctrl+u | delete backward      |
| ctrl+k | delete forward       |
| ctrl+l | clear screen         |
| ctrl+r | search for history   |

- `!!` last command
- `!$` last postional parameter of last command

## Set

```
set -euxo pipefail
```

- `-e`: Exit on none-zero status
- `-x`: Print a trace 
- `-u`: Treat unset variables as error
- `-o pipefail`: Return none-zero if last pipeline return none-zero

## Xargs

Capture the output of one command and pass it to another command

```sh
# Batch renames
ls -1 . | sed 's/\.mdx$//' | xargs -I{} mv {}.mdx {}.md
```

## Cron Syntax

```
Min  Hour Day  Mon  Weekday
*    *    *    *    *  command to be executed
┬    ┬    ┬    ┬    ┬
│    │    │    │    └─  Weekday  (0=Sun .. 6=Sat)
│    │    │    └──────  Month    (1..12)
│    │    └───────────  Day      (1..31)
│    └────────────────  Hour     (0..23)
└─────────────────────  Minute   (0..59)
```

| expr           | description                 |
| -------------- | --------------------------- |
| `0 * * * *`    | every hour                  |
| `*/15 * * * *` | every 15 mins               |
| `0 */2 * * *`  | every 2 hours               |
| `0 18 * * 0-6` | every week Mon-Sat at 6pm   |
| `10 2 * * 6,7` | every Sat and Sun on 2:10am |
| `0 0 * * 0`    | every Sunday midnight       |
| `@reboot`      | every reboot                |


## Common Snippets

### Script Directory

```sh
SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )"
```

### Sudo Without Pass

```sh
cat << EOF | sudo tee -a /etc/sudoers.d/$USER
$USER ALL=(ALL) NOPASSWD:ALL
EOF 
```

### Ulimit

```sh
cat <<EOF | sudo tee -a /etc/security/limits.d/$USER.conf
$USER soft nproc 100000
$USER hard nproc 100000
$USER soft nofile 100000
$USER hard nofile 100000
EOF
```

## Change repo source

### ubuntu

```sh
sed -i -E 's/\w+.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
```

### Alpine

```sh
sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
```

### Load environment variables from .env

```sh
eval $(cat .env | sed 's/^/export /')
```
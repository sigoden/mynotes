# Git

## Stages

| CMD                    | NOTE                   | UNCHANGED | CHANGED | STAGED | COMMITTED |
| :--------------------- | :--------------------- | :-------: | :-----: | :----: | :-------: |
| git commit             | commit files           |           |         | \|---  | ------->  |
| git reset --soft HEAD^ | undo commit files      |           |         | <----  | ------\|  |
| git add file           | add file to stage      |           |  \|---  | ---->  |           |
| git add .              | add all files to stage |           |  \|---  | ---->  |           |
| git reset -- file      | unstaged file          |           |  <----  | ---\|  |           |
| git reset              | unsatged all files     |           |  <----  | ---\|  |           |
| git checkout -- file   | unchanged file         |  <-----   |  ---\|  |        |           |
| git checkout -f        | unchanged all files    |  <-----   |  ---\|  |        |           |

## Clone

```sh
# branch
git clone -b dev https://github.com/org/repo.git
# target folder
git clone https://github.com/org/repo.git myrepo 
# contains submodule
git clone --recurse-submodules https://github.com/org/repo.git
# abaddon history
git clone --depth=1 https://github.com/org/repo.git
```

## Branch

```sh
# list branches
git branch
# list remote branches
git branch -r
# list all branches
git branch -a
# create branch
git checkout -b feat1
# rename a branch
git branch -m newname
# checkout branch
git checkout feat1
# checkout remote branch
git checkout -t origin/dev
# delete branch
git branch -d feat1
# delete branch forcedly
git branch -D feat1
# delete branch of remote repo
git push origin :feat1
```

## Tag

```sh
# list tags
git tag
# create tag
git tag v1.0.0
# delete tag
git tag -d v1.0.0
# delete tag forcedly
git tag -D v1.0.0
# delete tag of remote repo
git push origin :v1.0.0
```

## Fetch

```sh
# pull changes
git fetch
# pull github pr
git fetch origin pull/ID/head:BRANCH_NAME
# pull changes and prune none-exist remote branches
git fetch --purge
```

## Snippets

```sh
# merge last commit
git commit --amend
# sync submodule
git submodule update --init --recursive
# inspect remote url
git remote -v
# change remote url
git remote set-url origin $new_repo
```
  

## Config

### User

```sh
git config --global user.name $user
git config --global user.email $email
```

### Ignore

```
# linux/macos
git config --global core.excludesFile '~/.gitignore'
# windows
git config --global core.excludesFile "$env:USERPROFILE\.gitignore"
```

### Cjk path
```
git config --global core.quotepath false
```

### CRLF

```sh
git config --global core.eol lf
git config --global core.autocrlf input
```

## Credentials

### WSL2

```sh
git config --global credential.helper \
  "/mnt/c/Program\\ Files/Git/mingw64/bin/git-credential-manager.exe"
```

## SSH Repo

```sh
sudo useradd -m -r -U -d /home/git-repos git
sudo su - git

mkdir -p ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
touch ~/.ssh/authorized_keys

git init --bare ~/repo.git
```

```sh
git remote add origin git@<your-server-ip>:repo.git
```

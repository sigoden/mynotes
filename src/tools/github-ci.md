# Github CI

## Triggers

```yaml
on: 
  push:
    tags: 
      - v[0-9]+.[0-9]+.[0-9]+*

on
  push:
    branches:
    - main
  pull_request:

on:
  workflow_dispatch:
  schedule:
    - cron:  '0 2 * * *'
```

## Environment Variables

| Name                | Explain                                    |
| ------------------- | ------------------------------------------ |
| `CI`                | `true`，detect CI environment              |
| `GITHUB_REPOSITORY` | owner/name，e.g. `nodejs/node`             |
| `GITHUB_REF`        | tag or branch, `refs/tags/v1.0.0`          |
| `RUNNER_OS`         | os info, e.g. `Linux`, `Windows`, `macOS`. |

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    env:
      FOO: foo
    steps:
      - if: ${{ env.FOO == 'foo' }}
        run: echo "$BAR"
        env:
          BAR: bar
```

## Debug Tips

### Print all environment variables

```yaml
    steps:
      - run: env
```

### Print github variables
```yaml
    steps:
      - run: echo "$GITHUB_CONTEXT"
        env:
          GITHUB_CONTEXT: ${{ toJSON(github) }}
```

### Print shell evalutaion
```yaml
    steps:
      - run: |
          set -x 

          src=`pwd`
          dist=$src/dist
        shell: bash
```

### ssh into runner

```yaml
    steps:
    - name: Setup upterm session
      uses: lhotari/action-upterm@v1
```

RUNNER will log:
```
=== S1WV8GFLUQTD7SN7TAKM                                                                 
Command:                tmux new -s upterm -x 132 -y 43                                 
Force Command:          tmux attach -t upterm                                           
Host:                   ssh://uptermd.upterm.dev:22                                     
SSH Session:            ssh s1wv8gfLuqTd7sn7taKm:MTAuMjQ0LjAuNzY6MjI=@uptermd.upterm.dev
```


## Get Tag info

```yaml
jobs:
  release:
    outputs:
      rc: ${{ steps.check-tag.outputs.rc }}
    steps:
      - name: Get Tag
        id: get_tag
        shell: bash
        run: |
          tag=${GITHUB_REF##*/}
          if [[ "$tag" =~ [0-9]+.[0-9]+.[0-9]+$ ]]; then
            echo "rc=false" >> $GITHUB_OUTPUT
          else
            echo "rc=true" >> $GITHUB_OUTPUT
          fi
```

## Add environment variable

```yaml
jobs:
  job1:
    steps:
      - name: Set env var
        run: |
          echo "foo=bar" >> $GITHUB_ENV

```

## Pass variables between jobs

```yaml
jobs:
  job1:
    runs-on: ubuntu-20.04
    outputs:
      tag: ${{ steps.get_tag.outputs.tag }}

  job2:
    needs: ["job1"]
    if: needs.job1.outputs.tag != 'null'
```

## Pass artificats between jobs

```yaml
jobs:
  job1:
    steps:
      - uses: actions/upload-artifact@v4
        with:
          name: app
          path: target/release/app

  job2:
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: app
          path: ${{ github.workspace }}/target/release/app 
```

## Build and push docker image

```yaml
jobs:
  docker:
    name: Publish to Docker Hub
    if: startsWith(github.ref, 'refs/tags/')
    runs-on: ubuntu-latest
    needs: release
    steps:
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          build-args: |
            REPO=${{ github.repository }}
            VER=${{ github.ref_name }}
          platforms: |
            linux/amd64
            linux/arm64
            linux/386
            linux/arm/v7
          push: ${{ needs.release.outputs.rc == 'false' }}
          tags: ${{ github.repository }}:latest, ${{ github.repository }}:${{ github.ref_name }}
```

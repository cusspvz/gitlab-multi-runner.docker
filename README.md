# gitlab-ci-multi-runner.docker

:smile_cat: GitLab CI multi runner configurable via Environment Variables

I've choosed a cat emoji because GitLab logo remembers me a cat, cats are
independent, and you don't need to tell them where to go when they run!
Coincidence? Hmm... Think not, but its cool, so use it! :smile:

## Motivation
Docker is portable, Docker volumes data mounts are not, [at least by now](https://github.com/rancher/convoy).
GitLab CI Multi Runner is portable, although it needs a configuration file,
which is passed by a volume data mount.

Turns out that on Large Scalling environments it is a real hassle! (Try to run
them with `docker-compose` or [rancher](https://rancher.com))

As we at [findhit](https://findhit.com) need it, I've decided to build this!
Configuration should be passed with

## Usage

```bash
docker run -d \
    --rm \
    # [environment variables here] \
    #-e VARIABLE=VALUE \
    -e CONCURRENT=2 \
    -e DEFAULT_RUNNER_URL=https://gitlab.domain.com \
    -e DEFAULT_RUNNER_EXECUTOR=docker \
    cusspvz/gitlab-ci-multi-runner:latest
```

```yaml

#gitlab:
#    ...

gitlab-ci-multi-runner:
  scale: 40
  image: cusspvz/gitlab-ci-multi-runner
  environments:
  # [environment variables here]
  - CONCURRENT=2
  - DEFAULT_RUNNER_URL=https://gitlab.domain.com
  - DEFAULT_RUNNER_EXECUTOR=docker

```

## Examples

### Generating 3 runners: 2 docker and 1 ssh

```
docker run -d \
    --rm \
    -e CONCURRENT=3 \
    -e DEFAULT_RUNNER_URL=https://gitlab.domain.com \
    -e DEFAULT_RUNNER_EXECUTOR=docker \
    -e RUNNER_1_IMAGE=alpine \
    -e RUNNER_2_IMAGE=ubuntu:latest \
    -e RUNNER_3_EXECUTOR=ssh \
    -e RUNNER_3_HOST=my-tests-host \
    -e RUNNER_3_USER=whale \
    -e RUNNER_3_PASSWORD=døCk-mY-S0f[ware]lty \ # Being funny :)
    cusspvz/gitlab-ci-multi-runner:latest
```

## Environment Variables

`CONCURRENT`
Number of runners to be configured

### Defaults

`DEFAULT_RUNNER_URL`
Defaults to: `https://CI/`

`DEFAULT_RUNNER_TOKEN`
Required

`DEFAULT_RUNNER_LIMIT`
Defaults to: `0`

`DEFAULT_RUNNER_EXECUTOR`
Defaults to: `shell`

`DEFAULT_RUNNER_BUILDS_DIR`
Defaults to: ``


### Runner variables
`X` represents the runner instance, if you want to set runner 4 to connect to
a different gitlab server, use `RUNNER_4_URL`.

`RUNNER_X_URL`
Defaults to: `$DEFAULT_RUNNER_URL`

`RUNNER_X_TOKEN`
Defaults to: `$DEFAULT_RUNNER_TOKEN`

`RUNNER_X_LIMIT`
Defaults to: `$DEFAULT_RUNNER_LIMIT`

`RUNNER_X_EXECUTOR`
Defaults to: `$DEFAULT_RUNNER_EXECUTOR`

`RUNNER_X_BUILDS_DIR`
Defaults to: `$DEFAULT_RUNNER_BUILDS_DIR`


### Executor specific variables

#### shell
`RUNNER_X_SHELL`
Defaults to: `bash`

#### docker
`RUNNER_X_HOST`
Defaults to: ``

`RUNNER_X_IMAGE`
Defaults to: `ubuntu-upstart:14.04`

`RUNNER_X_PRIVILEGED`
Defaults to: `false`

`RUNNER_X_DISABLE_CACHE`
Defaults to: `false`

`RUNNER_X_CACHE_DIR`
Defaults to: ``

#### ssh
`RUNNER_X_HOST`
Required

`RUNNER_X_PORT`
Defaults to: `22`

`RUNNER_X_USER`
Defaults to: `root`

`RUNNER_X_PASSWORD`
Required

#### docker-ssh
`RUNNER_X_HOST`
Defaults to: ``

`RUNNER_X_IMAGE`
Defaults to: `ubuntu-upstart:14.04`

`RUNNER_X_PRIVILEGED`
Defaults to: `false`

`RUNNER_X_DISABLE_CACHE`
Defaults to: `false`

`RUNNER_X_CACHE_DIR`
Defaults to: ``

`RUNNER_X_PORT`
Defaults to: `22`

`RUNNER_X_USER`
Defaults to: `root`

`RUNNER_X_PASSWORD`
Defaults to: `docker.io`

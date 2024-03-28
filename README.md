# docker-shell-action

[![GitHub Actions Status](https://github.com/actionsh/docker-shell-action/actions/workflows/tests.yml/badge.svg)](https://github.com/actionsh/docker-shell-action/actions/workflows/tests.yml)

GitHub action that starts a background container where chosen workflow steps can run shell commands by specifying `shell: docker-shell {0}`.
A runner user with `sudo` access is created in the container, and the workspace parent directory, docker socket, and RUNNER_TEMP are mounted in the container at the same path.

It is useful for cases where you can't [run the whole workflow job in your container](https://docs.github.com/en/actions/using-jobs/running-jobs-in-a-container) as the container is [not compatible with GitHub Runner](https://github.com/actions/runner/issues/2906).

The action tries to provide transparent support for the following third-party actions:

* [hendrikmuhs/ccache-action](https://github.com/hendrikmuhs/ccache-action)

## Inputs

* `image`

  The container image to use.

* `options`

  Additional options to pass to `docker run`.

## Example

```yaml
name: Run a command in CentOS 7 container
on: [push]
jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code (can't run this in a glibc 2.17 container)
        uses: actions/checkout@v4

      - name: Start background container
        uses: actionsh/docker-shell-action@v0
        with:
          image: 'centos:7'
          options: >-
            -e MY_VAR=value

      - name: Run commands in the container
        shell: docker-shell {0}
        run: |
          pwd
          sudo yum list
```

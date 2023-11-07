Apko installs "apk" packages onto an alpine image with minimal extras.

The config files are declarative and don't support "RUN" statements, which likely makes this a "hard sell" for most software development.

But go read their website and see melange and other stuff you could do.

https://github.com/chainguard-dev/apko

For now, create a build config file `git.yaml` (from : https://github.com/chainguard-images/images/blob/main/images/git/configs/latest.alpine.nonroot.apko.yaml ):

```
#nolint:tf-minimal
contents:
  repositories:
    - https://dl-cdn.alpinelinux.org/alpine/edge/main
    - https://dl-cdn.alpinelinux.org/alpine/edge/community
  packages:
    - git
    - git-lfs
    - openssh-client

entrypoint:
  command: /usr/bin/git

work-dir: /home/git

accounts:
  groups:
    - groupname: git
      gid: 65532
  users:
    - username: git
      uid: 65532
      gid: 65532
  run-as: 65532
archs:
  - amd64
```

There is a git.yaml for you in this directory.

And build the image :
```
docker run -v "$PWD":/work -w /work cgr.dev/chainguard/apko build git.yaml apko-git:test apko-git.tar
```

You'll see you get a tar file you can load into docker with
```
docker load -i apk-git.tar
```

And then run it:

```
 docker run -it --entrypoint sh apko-git:test-amd64
~ $ git
usage: git [-v | --version] [-h | --help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           [--config-env=<name>=<envvar>] <command> [<args>]
....
```

Hooray, we have git. But hold on a sec, we ALSO have a shell still.

The good news is, coming from Alpine, the image is very small now :
```
docker images| grep git
git                       latest       4242324e0305   2 hours ago      179MB
smallgit                  latest       45a916f46fe2   3 hours ago      142MB
apko-git                  test-amd64   b0fa697b9598   6 hours ago      34.9MB
```

How's our security score?

Starting with the "better git" (and using the cache from the previous exercise)
```
docker run -v "../trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image smallgit
2023-11-07T17:58:08.610Z        INFO    Vulnerability scanning is enabled
2023-11-07T17:58:08.610Z        INFO    Secret scanning is enabled
2023-11-07T17:58:08.610Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T17:58:08.610Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T17:58:10.842Z        INFO    Detected OS: redhat
2023-11-07T17:58:10.842Z        INFO    Detecting RHEL/CentOS vulnerabilities...
2023-11-07T17:58:10.902Z        INFO    Number of language-specific files: 0

smallgit (redhat 9.3)
=====================
Total: 68 (UNKNOWN: 0, LOW: 22, MEDIUM: 46, HIGH: 0, CRITICAL: 0)
```


And now the Alpine image :
```
docker run -v "`pwd`/../03 nginx/trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image apko-git:test-amd64
2023-11-07T17:59:41.756Z        INFO    Vulnerability scanning is enabled
2023-11-07T17:59:41.756Z        INFO    Secret scanning is enabled
2023-11-07T17:59:41.756Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T17:59:41.756Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T17:59:42.131Z        INFO    Detected OS: alpine
2023-11-07T17:59:42.131Z        INFO    Detecting Alpine vulnerabilities...
2023-11-07T17:59:42.133Z        INFO    Number of language-specific files: 0

apko-git:test-amd64 (alpine edge)
=================================
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)
```

Zero vulnerabilities. You don't see that every day!!

You may also have noticed some other files appeared when you did your apko build. Apko generates SBOMs for you. And there may be sigstore integration in its future. More on sigstore and attestation in a later example.

Chainguard make their base images available, the git image is actually used by Tekton in the git-cli task. (Tekton use their own Tekton made image for the git clone for some reason.)

```
docker run --entrypoint sh -it cgr.dev/chainguard/git
~ $ 
```



Wolfi is Chainguard's new base image. Designed to work with apko.

Create a git-wolfi.yaml file, like you'll find in this directory and run this command to build :
```
docker run -v "$PWD":/work -w /work cgr.dev/chainguard/apko build git-wolfi.yaml apko-git-wolfi:test apko-git-wolfi.tar

```

The yaml file is based on https://github.com/chainguard-images/images/blob/main/images/git/configs/latest.wolfi.nonroot.apko.yaml

With some add-ons (which Chainguard provide with their Makefile, here : https://github.com/chainguard-images/images/blob/ed6e076f1c56444289dcab461e9e848923153630/Makefile#L6 )

It creates a slightly larger image but still manages to have ZERO vulnerabilities.

```
docker images | grep git
git                       latest       4242324e0305   3 hours ago         179MB
smallgit                  latest       45a916f46fe2   3 hours ago         142MB
apko-git                  test-amd64   b0fa697b9598   6 hours ago         34.9MB
apko-git-wolfi            test-amd64   962a766f8a78   24 hours ago        49.6MB

docker run -v "../trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image apko-git-wolfi:test-amd64
2023-11-07T18:20:59.149Z        INFO    Vulnerability scanning is enabled
2023-11-07T18:20:59.149Z        INFO    Secret scanning is enabled
2023-11-07T18:20:59.149Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T18:20:59.149Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T18:20:59.670Z        INFO    Detected OS: wolfi
2023-11-07T18:20:59.670Z        INFO    Detecting Wolfi vulnerabilities...
2023-11-07T18:20:59.671Z        INFO    Number of language-specific files: 0

apko-git-wolfi:test-amd64 (wolfi 20230201)
==========================================
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)
```

But, what's this?
```
docker run -it --entrypoint sh apko-git-wolfi:test-amd64
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "sh": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container:                 
$ docker run -it --entrypoint bash apko-git-wolfi:test-amd64
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "bash": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container:  
```

That's right, there is _NO_ shell in the image.

I suggest you confirm this to yourself with `dive` a tool for diving into the content of containers.

```
docker run -v /var/run/docker.sock:/var/run/docker.sock -it --rm wagoodman/dive apko-git-wolfi:test-amd64
```

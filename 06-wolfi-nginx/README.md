```
docker run -v "$PWD":/work -w /work cgr.dev/chainguard/apko build nginx-wolfi.yaml nginx-wolfi:test nginx-wolfi.tar
```

Then docker load/etc..
```
docker run -v "../trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image  nginx-wolfi:test-amd64
2023-11-07T18:37:04.053Z        INFO    Vulnerability scanning is enabled
2023-11-07T18:37:04.053Z        INFO    Secret scanning is enabled
2023-11-07T18:37:04.053Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T18:37:04.053Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T18:37:04.353Z        INFO    Detected OS: wolfi
2023-11-07T18:37:04.353Z        INFO    Detecting Wolfi vulnerabilities...
2023-11-07T18:37:04.354Z        INFO    Number of language-specific files: 0

nginx-wolfi:test-amd64 (wolfi 20230201)
=======================================
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)
```

nginx with ZERO vulnerabilities. What is this, black magic?

And the build process made a load of SBOM files for us. 

And the image is the smallest nginx.

```
docker images | grep nginx
smallnginx                latest       84e7c02a4c8f   2 hours ago    95.4MB
nginx-wolfi               test-amd64   4750ce695f18   25 hours ago   21.5MB
nginx                     latest       c20060033e06   6 days ago     187MB
```


This is a tiny, secure, quick to build image.

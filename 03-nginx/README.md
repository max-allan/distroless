
```
docker build -t smallnginx .
echo "<html><body>Hello world</body></html>" > index.html
docker run -v "`pwd`":/usr/share/nginx/html/ -p 80:80 -d --rm smallnginx
curl localhost:80
<html><body>Hello world</body></html>
```

Does _work_ but still suffers from including a lot of "OS" and bash.

It is a lot smaller than the public nginx distro.
```
docker images | grep nginx
smallnginx               latest    84e7c02a4c8f   20 minutes ago   95.4MB
nginx                    latest    c20060033e06   6 days ago       187MB
```

And :

```
mkdir ../trivycache
docker run -v "../trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image smallnginx
2023-11-07T17:19:50.526Z        INFO    Vulnerability scanning is enabled
2023-11-07T17:19:50.526Z        INFO    Secret scanning is enabled
2023-11-07T17:19:50.526Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T17:19:50.526Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T17:19:52.163Z        INFO    Detected OS: redhat
2023-11-07T17:19:52.163Z        INFO    Detecting RHEL/CentOS vulnerabilities...
2023-11-07T17:19:52.178Z        INFO    Number of language-specific files: 0

smallnginx (redhat 9.3)
=======================
Total: 30 (UNKNOWN: 0, LOW: 14, MEDIUM: 16, HIGH: 0, CRITICAL: 0)

```

Compare that to the public image:
```
docker run -v "../trivycache":/root/.cache/  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nginx
2023-11-07T17:21:38.618Z        INFO    Vulnerability scanning is enabled
2023-11-07T17:21:38.618Z        INFO    Secret scanning is enabled
2023-11-07T17:21:38.618Z        INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-11-07T17:21:38.618Z        INFO    Please see also https://aquasecurity.github.io/trivy/v0.47/docs/scanner/secret/#recommendation for faster secret detection
2023-11-07T17:21:38.628Z        INFO    Detected OS: debian
2023-11-07T17:21:38.628Z        INFO    Detecting Debian vulnerabilities...
2023-11-07T17:21:38.645Z        INFO    Number of language-specific files: 0

nginx (debian 12.2)
===================
Total: 103 (UNKNOWN: 0, LOW: 78, MEDIUM: 19, HIGH: 5, CRITICAL: 1)
```

So we have wiped out a number of security issues without a huge amount of work.

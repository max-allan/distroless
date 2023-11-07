
```
docker build -t smallgit .
docker run -v "`pwd`":/workspace smallgit clone https://github.com/ChainGuard/ChainGuard.git
```

Does _work_ but :
```
docker run -it --entrypoint bash smallgit
bash-5.1# pwd
/workspace
```

In theory the Dockerfile should have installed a bit less this time. What did it install?

```
[root@021c842f1beb]# dnf -y install --installroot /foo --setopt install_weak_deps=false --nodocs -y git
Unable to detect release version (use '--releasever' to specify release version)
Updating Subscription Management repositories.
Unable to read consumer identity
Subscription Manager is operating in container mode.

This system is not registered with an entitlement server. You can use subscription-manager to register.

Last metadata expiration check: 0:00:40 ago on Tue Nov  7 15:20:38 2023.
Dependencies resolved.
===================================================================================================================
 Package                       Architecture Version                               Repository                  Size
===================================================================================================================
Installing:
 git                           x86_64       2.39.3-1.el9_2                        ubi-9-appstream-rpms        66 k
Installing dependencies:
 alternatives                  x86_64       1.24-1.el9                            ubi-9-baseos-rpms           42 k
 audit-libs                    x86_64       3.0.7-104.el9                         ubi-9-baseos-rpms          120 k
 basesystem                    noarch       11-13.el9                             ubi-9-baseos-rpms          8.0 k
 bash                          x86_64       5.1.8-6.el9_1                         ubi-9-baseos-rpms          1.7 M
...
...

Transaction Summary
===================================================================================================================
Install  159 Packages
```
So we saved only 3 packages and a tiny bit of space.

```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
git          latest    4242324e0305   3 seconds ago   179MB
smallgit     latest    45a916f46fe2   2 minutes ago   142MB
```

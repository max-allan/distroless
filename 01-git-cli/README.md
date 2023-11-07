
```
docker build -t git .
docker run -v "`pwd`":/workspace git clone https://github.com/ChainGuard/ChainGuard.git
```

Does _work_ but :
```
docker run -it --entrypoint bash git
bash-5.1# pwd
/workspace
bash-5.1# ls /
afs  bin  boot	dev  etc  home	lib  lib64  media  mnt	opt  proc  root  run  sbin  srv  sys  tmp  usr	var  workspace
bash-5.1# sh
sh-5.1# 
exit
```

In theory the Docker file should only have installed the bare minimum to run git into /foo and then only copied that to the new image.

Instead, we have an image with a full shell environment.

That isn't to say the environment is fully functional... There is no package manager for example.

```
bash-5.1# microdnf
bash: microdnf: command not found
bash-5.1# dnf
bash: dnf: command not found
bash-5.1# yum
bash: yum: command not found
bash-5.1# 
```

It has saved metadata from the package manager though. (And we have grep installed)

```
bash-5.1#  ls -lR /var | grep dnf
drwxr-xr-x 5 root root 4096 Nov  7 15:10 dnf
/var/cache/dnf:
/var/cache/dnf/ubi-9-appstream-rpms-b5efbddad0d332c6:
/var/cache/dnf/ubi-9-appstream-rpms-b5efbddad0d332c6/packages:
/var/cache/dnf/ubi-9-appstream-rpms-b5efbddad0d332c6/repodata:
/var/cache/dnf/ubi-9-baseos-rpms-716755c064625c80:
/var/cache/dnf/ubi-9-baseos-rpms-716755c064625c80/packages:
/var/cache/dnf/ubi-9-baseos-rpms-716755c064625c80/repodata:
/var/cache/dnf/ubi-9-codeready-builder-782a821fb45abb17:
/var/cache/dnf/ubi-9-codeready-builder-782a821fb45abb17/repodata:
drwxr-xr-x 2 root root 4096 Nov  7 15:10 dnf
/var/lib/dnf:
-rw-r--r-- 1 root root 30600 Nov  7 15:10 dnf.librepo.log
-rw-r--r-- 1 root root 70014 Nov  7 15:10 dnf.log
-rw-r--r-- 1 root root 12595 Nov  7 15:10 dnf.rpm.log
```

We can see why if we look at the dependencies of git :
```
[root@021c842f1beb foo]# dnf install git --installroot=/core
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
 bzip2-libs                    x86_64       1.0.8-8.el9                           ubi-9-baseos-rpms           42 k
 ca-certificates               noarch       2023.2.60_v7.0.306-90.1.el9_2         ubi-9-baseos-rpms          846 k
 coreutils                     x86_64       8.32-34.el9                           ubi-9-baseos-rpms          1.2 M
 coreutils-common              x86_64       8.32-34.el9                           ubi-9-baseos-rpms          2.0 M
 cracklib                      x86_64       2.9.6-27.el9                          ubi-9-baseos-rpms           99 k
 cracklib-dicts                x86_64       2.9.6-27.el9                          ubi-9-baseos-rpms          3.6 M
 crypto-policies               noarch       20230731-1.git94f0e2c.el9_3.1         ubi-9-baseos-rpms           87 k
 cyrus-sasl-lib                x86_64       2.1.27-21.el9                         ubi-9-baseos-rpms          774 k
 emacs-filesystem              noarch       1:27.2-9.el9                          ubi-9-appstream-rpms       9.6 k
 expat                         x86_64       2.5.0-1.el9                           ubi-9-baseos-rpms          120 k
 filesystem                    x86_64       3.16-2.el9                            ubi-9-baseos-rpms          4.7 M
 gawk                          x86_64       5.1.0-6.el9                           ubi-9-baseos-rpms          1.0 M
 gdbm-libs                     x86_64       1:1.19-4.el9                          ubi-9-baseos-rpms           56 k
 git-core                      x86_64       2.39.3-1.el9_2                        ubi-9-appstream-rpms       4.3 M
 git-core-doc                  noarch       2.39.3-1.el9_2                        ubi-9-appstream-rpms       2.9 M
 glibc                         x86_64       2.34-83.el9_3.7                       ubi-9-baseos-rpms          1.9 M
 glibc-common                  x86_64       2.34-83.el9_3.7                       ubi-9-baseos-rpms          321 k
 glibc-minimal-langpack        x86_64       2.34-83.el9_3.7                       ubi-9-baseos-rpms           37 k
...
...

Transaction Summary
===================================================================================================================
Install  162 Packages

```


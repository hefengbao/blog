---
title: "dpkg: warning: files list file for package 'xxxx' missing; assuming package has no files currently installed"
date: 2020-08-23 17:17:17
tags:
  - dpkg
categories: Linux
---
# dpkg: warning: files list file for package 'xxxx' missing; assuming package has no files currently installed


最新的 Ubuntu 系统可以字形解决这些问题👍

---

2021-06-18，找到最佳的解决办法：

新建 `dpkg-warning-fix.sh` 文件，编辑内容：

```
#!/bin/bash
set -e


# Clean out /var/cache/apt/archives
apt-get clean
# Fill it with all the .debs we need
apt-get --reinstall -dy install $(dpkg --get-selections | grep '[[:space:]]install' | cut -f1)


DIR=$(mktemp -d -t info-XXXXXX)
for deb in /var/cache/apt/archives/*.deb
do
    # Move to working directory
    cd "$DIR"
    # Create DEBIAN directory
    mkdir -p DEBIAN
    # Extract control files
    dpkg-deb -e "$deb"
    # Extract file list, fixing up the leading ./ and turning / into /.
    dpkg-deb -c "$deb" | awk '{print $NF}' | cut -c2- | sed -e 's/^\/$/\/./' > DEBIAN/list
    # Figure out binary package name
    DEB=$(basename "$deb" | cut -d_ -f1)
    # Copy each control file into place
    cd DEBIAN
    for file in *
    do
        cp -a "$file" /var/lib/dpkg/info/"$DEB"."$file"
    done
    # Clean up
    cd ..
    rm -rf DEBIAN
done
rmdir "$DIR"
```

原理是重新下载所有安装过的软件包，然后从中提取文件列表信息复制到info文件夹里。（所以请在网速较好的时候使用）

```shell
sudo ./dpkg-warning-fix.sh
```



参考：

[dpkg: warning: files list file for package `*****’-爱开源 (aikaiyuan.com)](https://www.aikaiyuan.com/9147.html)



---

经验不足，按网上的教程操作，把 `/var/lib/dpkg/info/` 的文件删除了，之后安装软件就会频繁出现如下警告：

```shell
dpkg: warning: files list file for package 'python-apt-common' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'zerofree' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'aufs-tools' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libnpth0:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'fdisk' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libhtml-tagset-perl' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'iputils-ping' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'ubuntu-advantage-tools' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libedit2:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'python3-cryptography' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libpam-runtime' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'vim-tiny' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libncurses5:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libtool' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libcom-err2:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libgomp1:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'dmeventd' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libpgm-5.2-0:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libatomic1:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'libpython3-stdlib:amd64' missing; assuming package has no files currently installed
dpkg: warning: files list file for package 'liblxc1' missing; assuming package has no files currently installed

......
```



解决办法：

新建 `dpkg-warning.txt` 文件,并把上述提示全部拷贝到 该文件中：

```
vi dpkg-warning.txt
```

新建 `dpkg-warning-fix.sh` 文件，编辑内容：

```shell
#!/bin/bash

for package in $(cat dpkg-warning.txt | grep "dpkg: warning: files list file for package " | grep -Po "'[^']*'" | sed "s/'//g")；
do
  apt install --reinstall "$package";
done
```

```shell
chmod 777 dpkg-warning-fix.sh
```

```shell
./dpkg-warning-fix.sh
```



参考：

https://blog.csdn.net/taosera/article/details/79420257~~~




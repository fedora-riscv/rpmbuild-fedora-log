# rpmbuild-fedora-log

## srpm-get

https://github.com/fedora-riscv/srpm-get

为了方便 rpm 自举阶段编译出最小的 Fedora 系统，我使用 Python 编写出一个小工具自动从 openkoji 拉对应的 srpm 方便编译

list.txt 给出所需哪些包，就会自动下载到 srpm 目录下，没有找到的包也会自动记录到 failed.txt 当中

## 不用刻意调参就成功的编译

依靠上面写的工具，又写了一个脚本批量编译那些不用特别调参就编译通过的包

```
#!/bin/bash -ex

SRPM_DIR="./srpm"
LOG_DIR="./logs"

rm -rf $LOG_DIR && mkdir -p $LOG_DIR

for srpm in $SRPM_DIR/*.src.rpm; do
    name=$(basename "$srpm" .src.rpm)
    rpmbuild --nodeps --rebuild "$srpm" > "$LOG_DIR/$name.log" 2>&1 || true
done
```

## fedora 的 srpm 编包注意

由于 Fedora 的 srpm 针对 RedHat 平台有一些私货，比如 file 和 autoconf 等一众 srpm 编译失败的原因是

%{gpgverify} 这个宏不存在，而正常情况下这个宏由 redhat-rpm-config 这个包定义

如果没有这个宏的情况下许多包会出现

```
/usr/var/tmp/rpm-tmp.wh3r3c: line 29: fg: no job control
```

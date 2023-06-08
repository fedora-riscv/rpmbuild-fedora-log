# rpmbuild-fedora-log

## srpm-get

https://github.com/U2FsdGVkX1/srpm-get

为了方便 rpm 自举阶段编译出最小的 Fedora 系统，我使用 Python 编写出一个小工具自动从 openkoji 拉对应的 srpm 方便编译

list.txt 给出所需哪些包，就会自动下载到 srpm 目录下，没有找到的包也会自动记录到 failed.txt 当中

## 不用刻意调参就成功的编译

依靠上面写的工具，又写了一个脚本批量编译那些不用特别调参就编译通过的包

```
#!/bin/bash -ex

SRPM_DIR="./srpm"
LOG_DIR="./logs"

rm -rf $LOG_DIR && mkdir -p $LOG_DIR
rm -rf $SRPM_DIR && mkdir -p $SRPM_DIR

for srpm in $SRPM_DIR/*.src.rpm; do
    name=$(basename "$srpm" .src.rpm)
    rpmbuild --nodeps --rebuild "$srpm" > "$LOG_DIR/$name.log" 2>&1 || true
done
```

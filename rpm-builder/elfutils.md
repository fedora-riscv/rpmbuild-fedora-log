# elfutils

在完成 [gnutls](gnutls.md) 安装之后 elfutils 的依赖就没那么麻烦了，唯独 doxygen 需要再进行一下调整

```yaml
- name: doxygen # for libmicrohttpd
  install: [doxygen]
  define:
    fedora:
    _module_build: 1
- name: libmicrohttpd # for elfutils
  install: [libmicrohttpd, libmicrohttpd-devel]
- name: elfutils
  install: [elfutils, elfutils-devel, elfutils-libs, elfutils-libelf, elfutils-libelf-devel]
```

因为 doxygen 会检测是不是在 Fedora 平台，如果是的话就会开启 clang 支持，这个现在还远远达不到目标，于是暂时屏蔽掉 fedora 宏

而 _module_build 默认是开启的，于是又需要一堆（真 满满一页）的 tex 依赖，于是也开启模块编译，避免刚上来就整一大堆麻烦
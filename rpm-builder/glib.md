# glib

接着 [gnutls](gnutls.md) 的编译，因为已经完成 gnutls 和 elfutils 的安装，glib 就相对没那么麻烦了

```yaml
- name: cpio # for systemtap
  install: [cpio]
- name: nss # for systemtap
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2209654
  without: [tests]
  install: [nss, nss-devel]
  patch:
    - sed -i 's/\/configure/\/configure --build=%{_build}/' %SPEC # It doesn't use the configure macro
- name: systemtap # for glib2
  install: [systemtap]
  define:
    fedora:
    with_python3: 1
    with_python2_probes: 0
    with_python3_probes: 1

- name: glib2
  install: [glib2, glib2-devel]
```

需要注意，必须要安装 nss 这个依赖，否则 systemtap 会少生成很多文件

而 nss 的 spec 文件也是一言难尽，没有使用标准的 %configure rpm 宏编写，而是直接

```yaml
../nspr/configure \
       --prefix=%{_prefix} \
       --libdir=%{_libdir} \
       --includedir=%{_includedir}/nspr4 \
       --with-dist-prefix=$PWD/../dist \
```

所以在交叉编译环境下（比如 rpmbuild —target）就会失效，没办法这里只能用 patch 修复了

而 systemtap 是我见过最好的 spec，里面有许多参数可以调整依赖开关，默认情况下也是判断如果在 Fedora 平台下，就会开启一堆依赖，所以这里先暂时屏蔽掉，然后手动开启部分依赖
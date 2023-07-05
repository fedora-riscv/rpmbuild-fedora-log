# glib

接着 [gnutls](gnutls.md) 的编译，因为已经完成 gnutls 和 elfutils 的安装，glib 就相对没那么麻烦了

```yaml
- name: nss # for systemtap
  without: [tests]
  install: [nss, nss-devel, nss-util, nss-utils-devel, nss-softokn, nss-softokn-devel,
            nss-softokn-freebl, nss-softokn-freebl-devel, nspr, nspr-devel]
  patch:
    - sed -i 's/\/configure/\/configure --build=%{_build}/' %SPEC # it doesn't use rpm's configure macro
    - sed -i '/%{__os_install_post} \\/a\export LD_LIBRARY_PATH=$RPM_BUILD_ROOT/%{_libdir} \\' %SPEC # nss depends on itself
- name: cpio # for systemtap
  install: [cpio]
- name: systemtap # for glib2
  install: [systemtap, systemtap-sdt-devel]
  define:
    fedora:
    with_openssl: 1
    with_debuginfod: 1
    with_python3: 1
    with_python2_probes: 0
    with_python3_probes: 1
- name: marshalparser # for glib2
  nobuild: true
  install: [marshalparser]

- name: glib2
  install: [glib2, glib2-devel]
  patch:
    - sed -i 's/gtk_doc=true/gtk_doc=false/' %SPEC # we don't have gtk-doc
    - sed -i 's/sysprof=enabled/sysprof=disabled/' %SPEC # we don't have sysprof

    - sed -i '/\/gtk-doc\/$/d' %SPEC
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

最后安装 glib2 因为默认开启了很多依赖，暂时都没有所以关闭

另外有个坑一定要注意，因为 glib2 会生成 gtester，而 gtester 的 RPATH 被设置为 $ORIGIN，正常情况下这个是个特殊的 RPATH，会被 ld 解析为“程序所在目录寻找动态链接库”，然而如果在 LFS 中没有挂载 proc 的话就会出错
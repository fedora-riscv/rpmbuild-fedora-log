# rpm-builder

之前写了一个 srpm-get 自动默认参数编译，但一些包使用默认参数编译会有问题，或者是说前后依赖不正确

所以为了可复现，以及能够解决前后依赖&默认参数编译的问题，所以将之前的脚本升级为一个更强大的新项目——builder

目前这个程序运行于 Python，并且自动读取一个 YAML 配置文件，然后从 openkoji 下载 srpm 并编译，并且配置文件可以自定义（同样是为了适用于不同的平台，不仅限于 RISC-V 32 位）

[https://github.com/fedora-riscv/rpm-builder](https://github.com/fedora-riscv/rpm-builder)

# YAML 配置介绍

选取几个比较有代表的配置语法类似如此

```yaml
- name: redhat-rpm-config
  nobuild: true
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2161211
  install: [redhat-rpm-config]
  post:
    - ln -sf redhat /usr/lib/rpm/$(rpm --eval '%_host_vendor')
    - sed -i 's/^%_enable_debug_packages/#%_enable_debug_packages/' /usr/lib/rpm/redhat/macros
    - sed -i 's/^%__brp_check_rpaths/#%__brp_check_rpaths/' /usr/lib/rpm/redhat/macros
    - echo gcc > $(gcc --print-file-name=)/rpmver
- name: annobin
  without: [tests, annocheck, clangplugin, llvmplugin]
  define:
    _unpackaged_files_terminate_build: 0
- name: gnupg2
  with: [bootstrap]
  install: [gnupg2]
  patch:
    - sed -r -i '/dirmngr(-client)?$/d' %SPEC
- name: libtool
  undefine: [_annotated_build]
  install: [libtool, libtool-ltdl, libtool-ltdl-devel]

```

其中每一项都是描述每个包应该如何调用 rpm 编译，比如

name → 包名叫什么

nobuild → 是否不需要编译（比如对于 noarch 的包），如果是 true 的话就会自动从 koji 下载已经编译好的 rpm

url → 自定义 koji 的地址，可以用于固定某个版本，如果没有给出的话就会自动从 koji 上搜索

with → 会在 rpmbuild 时添加 —with xxx 的参数

without → 同上会添加 —without xxx

undefine → 同上会添加 —undefine xxx

define → 同上会自动添加 —define “xxx xxx”，如果 define 后面为空值，也会自动替换成 rpm 的 %nil 变量

install → 在没有指定 nobuild 时，会在编译完成时自动安装对应的包，在指定了 nobuild 之后会自动从 koji 下载对应的 noarch 包

pre → 编译前需要执行一些指令

patch → 编译前需要对 spec 文件进行一些修改，使用 %SPEC 自动替换为对应的 spec 文件，比如上面调用 sed 命令删除了 dirmngr 相关的依赖（因为此文件依赖 GnuTLS，然而这个又依赖一大堆，所以斩断一些不必要的东西）

post → 编译并且安装到系统之后（也就是 install 数组）需要执行一系列的命令

# 其他

利用上面的 YAML，程序会自动读取并且按照先后顺序来编译，或者也可以使用

```yaml
python3 main.py xxx
```

单独编译某个包

为了编译 Fedora RV32 所需的包，本文剩余的内容都将采用上面语法描述未来会踩的坑

[开始之前](rpm-builder/%E5%BC%80%E5%A7%8B%E4%B9%8B%E5%89%8D)

[nano](rpm-builder/nano)

[Fedora 自带的宏](rpm-builder/Fedora%20%E8%87%AA%E5%B8%A6%E7%9A%84%E5%AE%8F)

[autotool](rpm-builder/autotool)

[annobin](rpm-builder/annobin)

[gnupg2](rpm-builder/gnupg2)

[cmake](rpm-builder/cmake)

[ruby](rpm-builder/ruby)

[libselinux](rpm-builder/libselinux)

[texinfo](rpm-builder/texinfo)

[gnutls](rpm-builder/gnutls)

[elfutils](rpm-builder/elfutils)

[docbook](rpm-builder/docbook)

[glib](rpm-builder/glib)

[chkconfig](rpm-builder/chkconfig)

[openldap](rpm-builder/openldap)

[e2fsprogs](rpm-builder/e2fsprogs)

[pam](rpm-builder/pam)
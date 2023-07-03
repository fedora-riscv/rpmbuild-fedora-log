# Fedora 自带的宏

默认情况下 Fedora 藏了许多的 RPM 宏私货位于 /usr/lib/rpm/macro.d

而且 [src.fedoraproject.org](http://src.fedoraproject.org/) 上许多的包都会调用这些宏，于是在非 Fedora 系统上为了自举 Fedora 就需要预先安装许多宏，目前我已经收集到的有

```yaml
- name: redhat-rpm-config # we need to compile annobin
  nobuild: true
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2161211
  install: [redhat-rpm-config]
  post:
    - ln -sf redhat /usr/lib/rpm/$(rpm --eval '%_host_vendor')
    - sed -i 's/^%_enable_debug_packages/#%_enable_debug_packages/' /usr/lib/rpm/redhat/macros
    - sed -i 's/^%__brp_check_rpaths/#%__brp_check_rpaths/' /usr/lib/rpm/redhat/macros
    - echo gcc > $(gcc --print-file-name=)/rpmver
- name: multilib-rpm-config
  nobuild: true
  install: [multilib-rpm-config]
- name: fedora-release
  nobuild: true
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2207533
  install: [fedora-release-common]
- name: systemd
  nobuild: true
  install: [systemd-rpm-macros]
- name: rust-packaging
  nobuild: true
  install: [rust-srpm-macros]
- name: python-rpm-macros
  nobuild: true
  install: [python-rpm-macros, python3-rpm-macros, python-srpm-macros]
- name: python-rpmautospec
  nobuild: true
  install: [rpmautospec-rpm-macros]
- name: perl
  nobuild: true
  install: [perl-macros]
- name: perl-srpm-macros
  nobuild: true
  install: [perl-srpm-macros]
- name: perl-Fedora-VSP # for perl on fedora
  nobuild: true
  install: [perl-Fedora-VSP]
```

基本上都是 noarch 的包，所以使用 nobuild 描述

其中 redhat-rpm-config 这个宏被使用十分广泛

但是安装之后会放到 /usr/lib/rpm/redhat 目录，这在其他 vendor 上是不会读取的，所以做了一个软链接确保可以读取到

而且会默认开启 _enable_debug_packages，这个又会依赖 elfutils 的功能，所以需要暂时关闭

__brp_check_rpaths 则是依赖 check-rpaths，这个也是需要编译 rpm 时需要相关依赖，所以直接关闭

部分 Fedora 包会读取 $(gcc --print-file-name=)/rpmver 文件，这个文件是 Fedora GCC 在编译时塞进去的私货，用于标识 gc 版本，所以临时放一个占位的

fedora-release 会提供 %fedora 这个宏，作为之后很多包依赖的判断依据
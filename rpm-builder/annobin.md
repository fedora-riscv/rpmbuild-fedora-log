# annobin

在安装完 redhat-rpm-config 这个宏之后，因为该宏频繁调用 annobin 的 gcc 插件

如果没有安装 annobin 的话，有些包可能会因此导致失败，所以一切开始必须先安装 annobin（注意先安装 autotool）

```yaml
- name: libtool # for gmp
  undefine: [_annotated_build]
  install: [libtool, libtool-ltdl, libtool-ltdl-devel]
- name: gmp # for annobin
  without: [fips]
  undefine: [_annotated_build]
  install: [gmp, gmp-devel]
- name: annobin
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2209223
  without: [tests, annocheck, clangplugin, llvmplugin]
  install: [annobin-plugin-gcc]
  define:
    _unpackaged_files_terminate_build:
```

正如一开始所提到，为了避免宏调用 annobin 的 gcc 插件，在没有安装 annobin 以前需要先 undefine 掉 _annotated_build 这个变量
而 annobin 这个包当前版本似乎有一些 bug，当 without annocheck 的时候会留下一个 annocheck.1.gz 的文件，所以需要 _unpackaged_files_terminate_build 赋值为 0 避免 rpm 抱怨

PS：当前问题已经提交 [PR](https://src.fedoraproject.org/rpms/annobin/pull-request/33) 给 annobin 了

PSS：另外 gmp 这个包本身也对 RISC-V 32 位支持有问题，同样已经提交 [PR](https://src.fedoraproject.org/rpms/gmp/pull-request/6)，现阶段可以在安装完 gmp 之后补丁（参考 PR 修改）
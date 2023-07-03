# e2fsprogs

为了之后编译 krb5（pam 的前置依赖），所以安装前置依赖 e2fsprogs（而且这个包本身也很重要）

```yaml
- name: fuse # for e2fsprogs
  install: [fuse, fuse-devel, fuse-libs]
- name: e2fsprogs
  install: [libcom_err, libcom_err-devel, libss, libss-devel]
  define:
    _unpackaged_files_terminate_build:
  patch:
    - sed -r -i '/%files -n e2scrub/,/^$/d' %SPEC # we don't have systemd
```

e2fsprogs 需要 [texinfo](texinfo.md)，并且 Fedora 上的 e2fsprogs 还会自动赠送 systemd 的相关文件，于是又会依赖 systemd 了，为了避免麻烦使用 sed 将整个 e2scrub 模块掀掉（但是也会因此导致一部分的文件未被 rpm 追踪，所以需要定义宏）
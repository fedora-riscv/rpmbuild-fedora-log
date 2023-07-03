# chkconfig

由于后续的组件会依赖 update-alternatives 指令，所以提前编译了 chkconfig

```yaml
- name: libpng # for slang
  install: [libpng, libpng-devel]
- name: slang # for newt
  without: [oniguruma]
  install: [slang, slang-devel]
- name: newt # for chkconfig
  install: [newt, newt-devel]
- name: chkconfig
  install: [alternatives]
  patch:
    - sed -r -i '/systemd-sysv-install$/d' %SPEC # we don't have systemd
  define:
    _unpackaged_files_terminate_build:
```

需要注意要剔除 systemd 的文件，并且忽略 rpm 未追踪的文件

而且 newt 这个包的前置依赖十分杂乱，可以参考 docbook 的编译
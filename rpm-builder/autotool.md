# autotool

这个暂时没什么坑，因为都是 nobuild 的包，所以可以直接安装

```yaml
- name: autoconf
  nobuild: true
  install: [autoconf]
- name: automake
  nobuild: true
  install: [automake]
```
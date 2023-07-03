# docbook

因为后面的 glib、chkconfig 和 pam 都依赖 docbook 生成文档，所以为了这两个顺利编译，需要先准备 docbook 相关的包

```yaml
- name: sgml-common # MUST! otherwise there will be many problems
  nobuild: true
  install: [sgml-common, xml-common]

- name: opensp # for openjade
  install: [opensp, opensp-devel]
  define:
    _module_build: 1
- name: openjade # for docbook-utils
  install: [openjade]
- name: which # for docbook-utils
  install: [which]
- name: lynx # for docbook-utils
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2195676
  install: [lynx]
- name: docbook-style-dsssl # for docbook-utils
  nobuild: true
  install: [docbook-style-dsssl]
- name: docbook-dtds # for docbook-utils
  nobuild: true
  install: [docbook-dtds]
- name: docbook-utils # for newt
  nobuild: true
  install: [docbook-utils]

- name: docbook-style-xsl # for xmlto
  nobuild: true
  install: [docbook-xsl]
- name: libxslt # for xmlto
  install: [libxslt, libxslt-devel]
- name: xmlto # for nss
  install: [xmlto]
```

这里有个坑，如果没有事先安装 sgml-common 的话，之后的包可以正常编译，但是安装会出错，而且会牵扯出一堆的问题，所以是绝对不能省略的

其中 docbook-utils 是为了之后编译 chkconfig 的前置依赖 newt 所准备的

而 xmlto 是为了编译 glib 的前置依赖 nss 所准备的
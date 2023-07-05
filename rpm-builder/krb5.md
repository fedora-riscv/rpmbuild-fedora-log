# krb5

这个大概算是最麻烦的一环了，之前编译的很多组件都是为了 krb5，而且该包本身也依赖一堆的 Python 脚本，十分折磨人，好在都是 nobuild 的包

```yaml
- name: libev # for libverto
  install: [libev, libev-devel]
- name: libverto # for krb5
  install: [libverto, libverto-devel]

- name: python-sphinx # for krb5
  nobuild: true
  install: [python3-sphinx]
- name: python-docutils # for krb5
  nobuild: true
  install: [python3-docutils]
- name: babel # for krb5
  nobuild: true
  install: [python3-babel]
- name: pytz # for krb5
  nobuild: true
  install: [python3-pytz]
- name: python-jinja2 # for krb5
  nobuild: true
  install: [python3-jinja2-]
- name: python-packaging # for krb5
  nobuild: true
  install: [python3-packaging]
- name: snowball # for krb5
  nobuild: true
  install: [python3-snowballstemmer]
- name: python-imagesize # for krb5
  nobuild: true
  install: [python3-imagesize]
- name: python-sphinxcontrib-serializinghtml # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-serializinghtml]
- name: python-sphinxcontrib-applehelp # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-applehelp]
- name: python-sphinxcontrib-devhelp # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-devhelp]
- name: python-sphinxcontrib-htmlhelp # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-htmlhelp]
- name: python-sphinxcontrib-qthelp # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-qthelp]
- name: python-sphinx-theme-alabaster # for krb5
  nobuild: true
  install: [python3-sphinx-theme-alabaster]
- name: python-sphinxcontrib-log-cabinet # for krb5
  nobuild: true
  install: [python3-sphinxcontrib-log-cabinet]
- name: tcl # for krb5
  install: [tcl, tcl-devel]
- name: lmdb # for krb5
  install: [lmdb, lmdb-devel, lmdb-libs]

- name: krb5
  install: [krb5-libs, krb5-devel]
  patch:
    - sed -i 's/--with-pam/--without-pam/' %SPEC # we don't have pam
```

因为此时还没有安装 pam，并且 pam 和 krb5 会和 openldap 以及 cyrus-sasl 形成循环依赖包，为了斩断依赖可以参考之前的 openldap 编译
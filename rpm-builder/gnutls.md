# gnutls

gnutls、elfutils 和 glib 都是后面依赖的大头，而正好这三个也都和 libmicrohttpd 有相互依赖，于是必须要打一些 patch 斩断依赖

其中 gnutls 最麻烦的是 p11-kit 这个依赖，因为我们现在既没有 systemd 也没有 gtk-doc（这东西也是一堆事，于是考虑之后决定暂时不装）

```yaml
- name: meson # for p11-kit
  nobuild: true
  install: [meson]
- name: bash-completion # for p11-kit
  nobuild: true
  install: [bash-completion]
- name: ninja-build # for p11-kit
  with: [bootstrap]
  install: [ninja-build]
- name: libtasn1 # for p11-kit
  install: [libtasn1, libtasn1-devel, libtasn1-tools]
  patch:
    - sed -i '/^autoreconf/d' %SPEC # we don't have gtk-doc
- name: p11-kit # for gnutls
  install: [p11-kit, p11-kit-devel]
  define:
    __meson_auto_features: auto # we don't have systemd
  patch:
    - sed -i 's/gtk_doc=true/gtk_doc=false/' %SPEC # we don't have gtk-doc
    - sed -i 's/man=true/man=false/' %SPEC # we don't have docbook
    - sed -i '/p11-kit-remote/,/bash-completion/{//!d;}' %SPEC
    - sed -i '/p11-kit-server$/,/^$/{//!d;}' %SPEC
    - sed -i '/\/gtk-doc\/$/d' %SPEC

- name: nettle # for gnutls
  without: [fips]
  install: [nettle, nettle-devel]
- name: libunistring # for gnutls
  install: [libunistring, libunistring-devel]

- name: gnutls
  without: [bootstrap, dane, fips, tpm2, gost, tests, srp, mingw]
  install: [gnutls, gnutls-devel]
```

在这 libtasn1 和 p11-kit 都会依赖 gtk-doc，而这个又会依赖 glib，于是和上文的三大件形成循环依赖

因为 gtk-doc 只是用来生成文档的，所以没什么大的影响，为了斩断依赖必须尽可能该关就关

p11-kit 需要的补丁最多，因为关掉生成文档之后，原先 %files 阶段记录的文件都有变化，于是需要一点调整
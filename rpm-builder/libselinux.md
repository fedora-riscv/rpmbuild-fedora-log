# libselinux

这个包暂时没什么技巧，但是前置依赖很多

```yaml
- name: bison # for flex
  install: [bison]
- name: flex # for libsepol
  install: [flex, libfl, libfl-devel]
- name: pcre2 # for libsepol
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2122857
  install: [pcre2, pcre2-devel]
- name: libsepol # for libselinux
  install: [libsepol, libsepol-devel, libsepol-static]

- name: dos2unix # for swig
  install: [dos2unix]
- name: swig # for libselinux
  without: [testsuite]
  install: [swig]

- name: libselinux
  install: [libselinux, libselinux-devel]
```
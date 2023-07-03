# pam

为了安装 pam，需要先搞定 krb5，这个是最坑的依赖，会和 openldap 以及 cyrus-sasl 形成三个循环依赖包，为了斩断依赖可以参考之前的 openldap 编译

```yaml
- name: tcl # for krb5
  install: [tcl, tcl-devel]
- name: lmdb # for krb5
  install: [lmdb, lmdb-devel, lmdb-libs]
- name: libverto # for krb5
  install: [libverto, libverto-devel]
- name: krb5 # for elinks
  install: [krb5, krb5-devel]
- name: elinks # for pam
  install: [elinks]

- name: libxml2 # for docbook5
  install: [libxml2, libxml2-devel]
- name: docbook5-schemas # for pam
  nobuild: true
  install: [docbook5-schemas]
- name: docbook5-style-xsl # for pam
  nobuild: true
  install: [docbook5-style-xsl]

- name: pam # bug in 1.5.2
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2206209
  install: [pam, pam-devel]
```
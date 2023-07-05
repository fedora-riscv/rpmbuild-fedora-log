# pam

在之前完成了 krb5 的铺垫之后，pam 最终得以安装

```yaml
- name: libxml2 # for docbook5
  install: [libxml2, libxml2-devel]
- name: docbook5-schemas # for pam
  nobuild: true
  install: [docbook5-schemas]
- name: docbook5-style-xsl # for pam
  nobuild: true
  install: [docbook5-style-xsl]

- name: elinks # for pam
  install: [elinks]

- name: pam # bug in 1.5.2
  url: https://koji.fedoraproject.org/koji/buildinfo?buildID=2206209
  install: [pam, pam-devel]
```

由于未知原因，pam 1.5.3 这个版本似乎是有问题的，换成最新版解决
# gnupg2

安装 nano 时提到，Fedora 许多包都调用 %gpgverify 宏，所以为了以后不频繁屏蔽掉这个宏，就继续安装 gnupg2

```yaml
# gpg
- name: libassuan # for libksba
  install: [libassuan, libassuan-devel]
- name: libksba # for npth
  with: [bootstrap]
  install: [libksba, libksba-devel]
- name: npth # for gnupg2
  install: [npth, npth-devel]
  define:
    gpgverify: "#"
- name: gnupg2
  with: [bootstrap]
  install: [gnupg2]
  patch:
    - sed -r -i '/dirmngr(-client)?$/d' %SPEC # we don't have gnutls
```

gnupg2 需要依赖 libgpg-error、libgcrypt、libassuan、libksba、npth，其中前两个已经由 [bootstrap](https://github.com/fedora-riscv/bootstrap) 脚本自动安装（同时也是 rpm 自己的前置依赖）

所以后面三个就需要通过 YAML 描述安装了，而且 npth 这个包也会调用 gpgverify 宏，而此时 gnupg2 又尚未安装完成，于是形成循环依赖，只能暂时屏蔽掉
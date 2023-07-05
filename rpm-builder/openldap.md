# openldap

为了之后编译 krb5 ，这个作为前置依赖因为牵扯的补丁太多，所以也单独拔出来

而且有个很坑的地方是——openldap 依赖 cyrus-sasl，而 cyrus-sasl 依赖 krb5，krb5 又依赖 openldap，所以形成循环依赖，必须要斩断依赖

```yaml
- name: groff # for openldap
  install: [groff, groff-base]
  define:
    with_x: 0
    _unpackaged_files_terminate_build:
  patch:
    - sed -r -i '/mom-pdf\.pdf$/d' %SPEC # we don't have ghostscript
    - sed -r -i '/\/pdf\/$/d' %SPEC
  post:
    - ln -sf soelim.groff /usr/bin/soelim
- name: cyrus-sasl # openldap
  install: [cyrus-sasl, cyrus-sasl-devel, cyrus-sasl-lib]
  patch:
    - sed -r -i 's/bootstrap_cyrus_sasl 0$/bootstrap_cyrus_sasl 1/' %SPEC # enable bootstrap
    - sed -i '/Find Kerberos/,/^CFLAGS=/{//!d;}' %SPEC # remove krb5 and mysql postgresql dependency
    - sed -i 's/--with-mysql=yes --with-pgsql=yes/--without-mysql --without-pgsql/' %SPEC
    - sed -i 's/--without-sqlite/--with-sqlite3=\/usr/' %SPEC

    - sed -i '/cyrusbdb2current$/d' %SPEC
    - sed -i '/\*sasldb\*.so\*/d' %SPEC
    - sed -i '/saslpasswd2/d' %SPEC
    - sed -i '/sasldblistusers2/d' %SPEC
    - sed -i '/\*gssapi\*.so\*/d' %SPEC
    - sed -i '/libgs2.so\*/d' %SPEC
- name: libevent # for openldap
  install: [libevent, libevent-devel]
- name: cracklib # for openldap
  install: [cracklib, cracklib-devel]

- name: openldap
  install: [openldap, openldap-devel]
```

目前看下来只有把 cyrus-sasl 这边的依赖斩断才能继续编译，所以对 cyrus-sasl 进行了亿点点修改

并且 openldap 本身依赖了 libevent、groff 和 cracklib

groff 依赖 ghostscript，因为这个安装更加复杂所以通过 patch 去除

而且 groff 的安装也十分魔幻，spec 脚本里将原本的 soelim 程序重命名为 soelim.groff，然后调用 ghostscript 重链接了一个 soelim……现阶段为了避免麻烦就直接软链接搞定
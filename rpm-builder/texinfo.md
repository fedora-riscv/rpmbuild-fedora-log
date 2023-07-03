# texinfo

由于之后要编译的许多包都会依赖 texinfo（比如 gnutls 和 elfutils），于是 texinfo 和前面一样作为前置依赖一块安装

```yaml
- name: perl-generators # for texinfo
  nobuild: true
  install: [perl-generators]
- name: perl-Text-Unidecode # for texinfo
  nobuild: true
  install: [perl-Text-Unidecode]
- name: perl-Unicode-EastAsianWidth # for texinfo
  nobuild: true
  install: [perl-Unicode-EastAsianWidth]
- name: perl-libintl-perl # for texinfo
  install: [perl-libintl-perl]
- name: texinfo
  install: [texinfo]
```

虽然基本上都是 nobuild 并且和 Perl 有关，但是这就要求编译前安装 Perl 并且配置正确

我在这里就踩了一个坑，安装 Perl 的时候因为参数没有调整为“和 koji”一样的环境，于是就导致一堆问题（lib 找不到啊，man 没有啊， vendor 找不到啊），所以这里也贴一下 Perl 的配置参数

```bash
LIB_DIR=$( [ $(getconf LONG_BIT) = "64" ] && echo "/usr/lib64" || echo "/usr/lib" )
make clean || true
./Configure -des -Dprefix=/usr \
                 -Dvendorprefix=/usr \
                 -Dprivlib="/usr/share/perl5" \
                 -Darchlib="$LIB_DIR" \
                 -Dvendorlib="/usr/share/perl5/vendor_perl" \
                 -Dvendorarch="$LIB_DIR/perl5/vendor_perl" \
                 -Dman1dir=/usr/share/man/man1 \
                 -Dman3dir=/usr/share/man/man3
```
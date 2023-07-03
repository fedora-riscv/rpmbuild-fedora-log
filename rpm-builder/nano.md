# nano

为了调试方便，vim 又太多依赖，所以安装 nano 是比较合适的选择，安装 nano 前需要先安装 ncurses

```yaml
- name: ncurses
  install: [ncurses, ncurses-devel, ncurses-libs, ncurses-base]
  define:
    gpgverify: "#"
- name: nano
  install: [nano]
  define:
    gpgverify: "#"
```

这两个 spec 都是会调用 %gpgverify 这个宏（参考下面），如果此时系统内还没有 gnupg2 的话，就要用 define 将该宏定义为注释，以达到屏蔽掉原始定义，否则会出错

```bash
%prep
%{gpgverify} --keyring='%{SOURCE2}' --signature='%{SOURCE1}' --data='%{SOURCE0}'
%autosetup -S git
```
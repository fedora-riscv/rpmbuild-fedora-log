# cmake

在之后安装的包里，有些会依赖 cmake 和 %cmake 宏，所以提前安装 cmake

```yaml
- name: vim # for cmake
  nobuild: true
  install: [vim-filesystem]
- name: cmake
  with: [bootstrap]
  without: [emacs, git_test, gui, sphinx, test, X11_test]
  install: [cmake, cmake-data, cmake-rpm-macros]
  define:
    _unpackaged_files_terminate_build:
```

Fedora 里的 cmake 会顺便编译 vim 的模块，所以需要提前安装 vim-filesystem

而且由于关闭了 emacs 的支持，cmake 会产生一个文件未被 rpm 追踪，所以需要定义 _unpackaged_files_terminate_build 为 0 避免 rpm 抱怨

另外务必安装cmake-data，否则会提示 Could not find CMAKE_ROOT
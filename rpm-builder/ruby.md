# ruby

在之后安装的包里，有些会依赖 rubygems 和其相关模块，所以再一次安装 ruby

```yaml
- name: libyaml # for ruby
  install: [libyaml, libyaml-devel]
- name: readline # for ruby
  install: [readline, readline-devel]
- name: ruby # will error in qemu user mode
  without: [rubypick, systemtap, hardening_test, yjit]
  install: [ruby, ruby-devel, rubygems]
```

这里有一个问题在于，可能因为 qemu user mode 的 IO 问题，如果在 user mode 下会导致 ruby 许多模块编译失败，进而导致 ruby 自身也编译失败

目前还没找到具体原因是什么，所以先转用 qemu-system-riscv32 继续编译
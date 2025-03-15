+++
date = '2025-03-13T14:29:05Z'
draft = false
title = 'EBPF 介绍'
tags = ['markdown', 'Linux', '性能', '内核', '内核观测']
categories = ['markdown', 'Linux']
+++

## EBPF

BPF 最初代表伯克利包过滤器 (Berkeley Packet Filter)，但是现在 eBPF(extended BPF) 可以做的不仅仅是包过滤，这个缩写不再有意义了。eBPF 现在被认为是一个独立的术语，不代表任何东西。在 Linux 源代码中，术语 BPF 持续存在，在工具和文档中，术语 BPF 和 eBPF 通常可以互换使用。最初的 BPF 有时被称为 cBPF(classic BPF)，用以区别于 eBPF。

## BTF

在 eBPF（扩展的伯克利包过滤器）中，BTF 是 BPF Type Format 的缩写。它是一种元数据格式，用于描述 eBPF 程序和使用到的数据结构类型。BTF 的主要目的是提供类型信息，以便在调试、验证和内核与用户空间之间的交互中更好地理解 eBPF 程序。

### BTF 的主要作用：
1. 类型信息：
    - BTF 包含了 eBPF 程序中使用的数据结构的类型信息，例如结构体、联合体、枚举、函数原型等。
    - 这些信息可以帮助内核验证 eBPF 程序的正确性，同时也能用于调试和可视化。
2. 调试支持：
    - BTF 使得调试工具（如 bpftool）能够显示 eBPF 程序中的变量和数据结构的具体类型信息。
    - 这对于理解复杂的 eBPF 程序非常有帮助。
3. 内核与用户空间交互：
    - BTF 允许用户空间工具（如 bpftool 或 libbpf）了解内核中定义的数据结构，从而更好地与 eBPF 程序交互。
4. CO-RE（Compile Once, Run Everywhere）：
    - BTF 是 eBPF CO-RE 技术的核心组成部分。CO-RE 允许 eBPF 程序在不同内核版本上运行，而无需重新编译。
    - BTF 提供了内核数据结构的布局信息，使得 eBPF 程序能够动态适应不同内核版本。

### BTF 的工作原理：
- BTF 信息通常嵌入在 eBPF 程序或内核的 ELF 文件中。
- 当 eBPF 程序加载到内核时，BTF 信息会被解析并用于验证和调试。
- 内核本身也会导出 BTF 信息（通常位于 /sys/kernel/btf/vmlinux），用户空间工具可以利用这些信息来理解内核数据结构。

### 如何生成 BTF：
- 在编译 eBPF 程序时，可以通过 Clang 的 -g 选项生成 BTF 信息。
- 例如：
    ```bash
    clang -O2 -g -target bpf -c program.c -o program.o
    ```
    这里的 -g 选项会生成 BTF 信息并嵌入到目标文件中。

### 总结：
BTF 是 eBPF 生态系统中一个重要的组成部分，它提供了类型信息和调试支持，使得 eBPF 程序更易于开发、调试和维护。同时，BTF 也是实现 eBPF CO-RE 的关键技术之一。

## 参考链接

1. [eBPF - Introduction, Tutorials & Community Resources](https://ebpf.io/zh-hans/)
2. [bpftool](https://github.com/libbpf/bpftool)


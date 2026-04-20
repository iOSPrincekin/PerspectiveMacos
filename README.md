# PerspectiveMacos

![Platform](https://img.shields.io/badge/platform-macOS-black)
![Focus](https://img.shields.io/badge/focus-XNU%20%2F%20boot.efi-blue)
![Status](https://img.shields.io/badge/status-in%20progress-orange)

> 一个面向源码级学习 macOS 的实验项目，目标是尽可能走通从启动链路到内核替换的关键路径。

## 项目目标

很多开发者做了多年 iOS 或 macOS 开发，但对系统究竟是如何编译、如何启动、如何运行，仍缺少从源码和运行时视角的完整理解，现在找工作这么卷，工作多年还在停留在表层开发，无法深入的理解底层代码对开发者来说是很不公平的。相比单纯阅读源码，能够亲手重新编译、替换关键组件并启动验证，往往更能建立深入认知。

由于 Apple 系统不是完全开源，个人开发者若想真正做到源码级修改、构建、运行和调试，门槛极高。也正因为如此，重新编译并修改 macOS 内核这件事，才更具吸引力。

这个仓库用于记录面向源码级学习 macOS 的实践成果，也希望借此找到志同道合的同学，一起探索这个精妙的系统。

## 进展总览

| 模块 | 目标说明 | 基于项目 | 当前状态 |
| --- | --- | --- | --- |
| `QEMU` 启动 | 运行 `macOS 11.4 BaseSystem.img`，进入启动界面并进入恢复模式 | [OSX-KVM](https://github.com/thenickdude/OSX-KVM) | `已完成` |
| `boot.efi` 替换 | 编写并替换镜像中的 `System/Library/CoreServices/boot.efi`，成功进入启动界面与恢复模式 | [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) | `已完成` |
| `boot.efi` 关闭内核ASLR | 关闭内核的加载地址的ASLR，使每次内核加载地址固定，方便调试 | [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) | `已完成` |
| `XNU Kernel` 替换 | 基于 `xnu-7195.121.3` 编译对应 `kernel`，重新映射到 `System/Library/KernelCollections/BootKernelExtensions.kc` | [PureDarwin](https://github.com/PureDarwin/PureDarwin) | `进行中` |

## 当前成果

### 1. QEMU 启动 macOS BaseSystem

- 已可在 `QEMU` 中运行 `macOS 11.4 BaseSystem.img`
- 已成功进入 macOS 启动界面
- 已可进入恢复模式

### 2. 自定义 `boot.efi`

- 已编写自己的 `boot.efi`
- 已替换 `BaseSystem.img` 中的 `System/Library/CoreServices/boot.efi`
- 替换后仍可正常进入启动界面和恢复模式

### 3. `boot.efi` 关闭内核ASLR

- 基于已编写自己的 `boot.efi`，关闭 内核的加载地址的ASLR
- 已经成功关闭，每次内核加载地址固定，使用LLDB 调试成功

### 4. 自定义 XNU 内核

- 已基于 `xnu-7195.121.3` 源码编译生成对应 `kernel`
- 正在将编译得到的内核重新映射到 `BootKernelExtensions.kc`
- 目标是完成系统内核代码替换并继续验证启动链路

## 这个项目在做什么

| 方向 | 说明 |
| --- | --- |
| `启动链路` | 关注从引导加载到系统启动界面的关键流程 |
| `组件替换` | 尝试替换 `boot.efi`、`kernel` 等核心组件 |
| `源码级验证` | 通过修改、编译、启动、打印和调试来理解系统行为 |
| `工程化复现` | 将探索过程沉淀为可复现的实验路径 |

## 欢迎交流

如果你也对以下方向感兴趣，欢迎一起交流：

- macOS 启动流程
- `boot.efi` 重编译与引导阶段分析
- XNU 内核重编译与替换
- 基于虚拟化的系统级调试与验证

# PerspectiveMacos

# 目的

大家做iOS 或 macos 开发都很多年了，但是macos或 iOS 系统是如何编译如何运行的，大部分同学应该都还不清楚，笔者认为学习一个操作系统比较好的方式就是可以重新编译它并进行调试，枯燥的阅读源代码远没有添加自己的一些调试代码进行打印验证这个亲身参与来的深刻和透彻，介于水果的不开源政策，个人开发者想从源代码级别去重新编译达到可以添加自己的代码、并成功运行进行调试，难于登天，但是能重新编译修改macos 内核对于笔者有无尽的吸引力，遂公布自己的面向源码级别学习macos的成果，找到志同道合的人，共同进步，欣赏这个美妙的系统。

# 成果

1.qemu 可以运行 macos11.4 BaseSystem.img，并进入macos 启动界面进入恢复模式, 基于 https://github.com/thenickdude/OSX-KVM 项目，已经成功；

2.编写 自己 boot.efi 替换 macos11.4 BaseSystem.img 中的 System/Library/CoreServices/boot.efi 后，并进入macos 启动界面进入恢复模式，基于 https://github.com/acidanthera/OpenCorePkg ，已经成功；

3.使用 xnu-7195.121.3 源代码编译生成 对应的 kernel 文件，将编译得到的 kernel 代码重新映射到 System/Library/KernelCollections/BootKernelExtensions.kc 文件中，代替之前的内核代码，基于 https://github.com/PureDarwin/PureDarwin ,进行中

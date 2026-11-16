---
title: Build Your Own macOS Kernel — Results
title_zh: 编译自己的 macOS 内核——成果篇
slug: build-your-own-macos-kernel-results
date: 2026-11-11 22:34:01
categories:
tags:
description:
---

## Background

macOS is an excellent operating system, yet many iOS and macOS developers only encounter operating-system internals in documentation. Working directly with the macOS kernel source is difficult and demanding. After a long investigation, I completed that journey and built a macOS system with my own startup program and kernel.

## Results

![macOS 11.4 successfully booted with a custom boot.efi and XNU kernel](custom-macos-kernel-boot-success.png)

The screenshot shows macOS running with my compiled versions of `/System/Library/CoreServices/boot.efi` and `/System/Library/KernelCollections/BootKernelExtensions.kc`, two protected components that cannot normally be replaced in a standard system.

### Project Status

The table below separates completed results from the work that remains. To avoid ambiguity, installing Apple's original macOS on real hardware is listed separately from running the custom kernel on real hardware.

| Status | Item | Notes |
| :---: | --- | --- |
| ✅ | Compile a custom `boot.efi` for macOS 11.4 | The system startup program has been compiled, modified, and debugged. |
| ✅ | Compile a custom XNU kernel for macOS 11.4 | The kernel has been built and debugged from the XNU source corresponding to macOS 11.4. |
| ✅ | Generate a custom kernel cache | The custom XNU kernel and related system components have been packaged into the cache required to boot macOS. |
| ✅ | Fix custom-kernel boot failures | Several low-level compatibility and data-validation issues that stopped the system from booting have been resolved. |
| ✅ | Build and configure the OpenCore environment | The boot environment can load the custom startup program and kernel in a virtual machine. |
| ✅ | Boot macOS 11.4 with the custom startup program and kernel | The system successfully reaches the macOS desktop in a virtual machine. |
| ✅ | Create a macOS 11.4 installer containing the custom kernel | The custom startup program, kernel, and kernel cache have been integrated into an installation image. |
| ✅ | Complete installation from the custom image | The complete path from booting the installer and installing to disk through first-boot setup has succeeded. |
| ✅ | Complete first-boot setup and enter the desktop | A user account was created successfully, and system applications including Finder and Safari run normally. |
| ✅ | Verify the installed custom system files | The startup program and kernel cache used by the installed system match the custom build outputs. |
| ✅ | Enter the macOS recovery environment | The macOS recovery environment boots successfully in a virtual machine. |
| ✅ | Install Apple's original macOS 11.4 on a physical Mac | The original system was installed on a Late 2013 13-inch MacBook Pro and reached the desktop. This does not mean the custom kernel has run on real hardware. |
| ❌ | Install and run the custom kernel on a physical Mac laptop | The custom startup program and kernel have only been verified in a virtual machine; physical-Mac installation and boot remain unverified. |
| ❌ | Install and run the custom macOS system on a Windows PC | Adaptation and installation for PC processors, graphics, motherboards, storage, and other hardware have not yet been completed. |
| ❌ | Complete physical-hardware driver support | Graphics, audio, networking, USB, power management, sleep, and wake must still be verified on target hardware. |
| ❌ | Complete compatibility testing across multiple computers | Full installation and stability testing has not yet been performed across multiple Mac and Windows PC models. |

> The central result is complete: the custom startup program, XNU kernel, and kernel cache have passed the full compile, install, boot, and desktop workflow in a virtual machine. The next milestone is adapting and validating the system on physical hardware.












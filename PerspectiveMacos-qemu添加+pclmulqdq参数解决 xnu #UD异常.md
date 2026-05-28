# 1.重新编译xnu 内核并使用 LLDB 调试崩溃了

堆栈

```
Debugger: Unexpected kernel trap number: 0x6, RIP: 0xffffff801398ed0e, CR2: 0xffffffa029b7b000
CPU 0 panic trap number 0x6, rip 0xffffff801398ed0e
cr0 0x0000000080010033 cr2 0xffffffa029b7b000 cr3 0x0000000015293000 cr4 0x00000000000406e0
Debugger called: <panic>
panic(cpu 0 caller 0xffffff8010515fe2): Kernel trap at 0xffffff801398ed0e, type 6=invalid opcode, registers:
CR0: 0x0000000080010033, CR2: 0xffffffa029b7b000, CR3: 0x0000000015293000, CR4: 0x00000000000406e0
RAX: 0x96982201178bfbfd, RBX: 0xffffffa029b45670, RCX: 0xb5b4bcb6ad240063, RDX: 0x0000000000000014
RSP: 0xffffffa029b455f0, RBP: 0xffffffa029b45640, RSI: 0xffffffa029b45670, RDI: 0xffffffa029b456f0
R8:  0xffffffa029b45620, R9:  0xffffffa029b455d0, R10: 0xc6003427b2231dbf, R11: 0xdc5d90f5f7c43869
R12: 0xffffff80139d4990, R13: 0x0000000000000010, R14: 0x0000000000000000, R15: 0x000000000000000c
RFL: 0x0000000000010086, RIP: 0xffffff801398ed0e, CS:  0x0000000000000008, SS:  0x0000000000000000
Fault CR2: 0xffffffa029b7b000, Error code: 0x0000000000000000, Fault CPU: 0x0 VMM, PL: 0, VF: 0

Backtrace (CPU 0), Frame : Return Address
0xffffffa029b44cc0 : 0xffffff80102996a5 mach_kernel : _debugger_collect_diagnostics + 0x275
0xffffffa029b44d20 : 0xffffff8010298d06 mach_kernel : _handle_debugger_trap + 0x1f6
0xffffffa029b44d60 : 0xffffff801052da8f mach_kernel : _kdp_i386_trap + 0x22f
0xffffffa029b44db0 : 0xffffff801051572a mach_kernel : _kernel_trap + 0x94a
0xffffffa029b44ed0 : 0xffffff8010535aaf mach_kernel : trap_from_kernel + 0x26
0xffffffa029b44ef0 : 0xffffff80102985d8 mach_kernel : _DebuggerTrapWithState + 0x98
0xffffffa029b45030 : 0xffffff801029918c mach_kernel : _panic_trap_to_debugger + 0x28c
0xffffffa029b45090 : 0xffffff8010e79bfb mach_kernel : _panic + 0xbb
0xffffffa029b45180 : 0xffffff8010515fe2 mach_kernel : _panic_trap + 0x562
0xffffffa029b453c0 : 0xffffff801051574d mach_kernel : _kernel_trap + 0x96d
0xffffffa029b454e0 : 0xffffff8010535aaf mach_kernel : trap_from_kernel + 0x26
0xffffffa029b45500 : 0xffffff801398ed0e com.apple.kec.corecrypto : _gcm_init + 0x8e
0xffffffa029b45640 : 0xffffff8013960a5d com.apple.kec.corecrypto : _ccmode_gcm_init + 0x7d
0xffffffa029b45660 : 0xffffff801395a2ed com.apple.kec.corecrypto : _ccgcm_one_shot + 0x6d
0xffffffa029b45980 : 0xffffff801394bf61 com.apple.kec.corecrypto : _fipspost_post_aes_gcm + 0xa1
0xffffffa029b45a10 : 0xffffff8013945126 com.apple.kec.corecrypto : _fipspost_post + 0xb59
0xffffffa029b45aa0 : 0xffffff801399ff96 com.apple.kec.corecrypto : _corecrypto_kext_start + 0x6f8
0xffffffa029b45b00 : 0xffffff8010cbf869 mach_kernel : __ZN6OSKext5startEb + 0x3f9
0xffffffa029b45ba0 : 0xffffff8010cbb9c3 mach_kernel : __ZN6OSKext4loadEhhP7OSArray + 0x873
0xffffffa029b45c40 : 0xffffff8010cba4ae mach_kernel : __ZN6OSKext22loadKextWithIdentifierEP8OSStringPP8OSObjectbbhhP7OSArray + 0x3ce
0xffffffa029b45d00 : 0xffffff8010cba0bb mach_kernel : __ZN6OSKext22loadKextWithIdentifierEPKcbbhhP7OSArray + 0xcb
0xffffffa029b45d50 : 0xffffff8010e4de55 mach_kernel : __ZN12KLDBootstrap28loadKernelExternalComponentsEv + 0x275
0xffffffa029b45db0 : 0xffffff8010e4d142 mach_kernel : __ZN12KLDBootstrap21readStartupExtensionsEv + 0x112
0xffffffa029b45df0 : 0xffffff8010e4cfb0 mach_kernel : __ZL32bootstrapRecordStartupExtensionsv + 0x10
0xffffffa029b45e00 : 0xffffff8010d34b22 mach_kernel : _InitIOKit + 0x1e2
0xffffffa029b45e30 : 0xffffff8010e4b087 mach_kernel : _PE_init_iokit + 0x2b7
0xffffffa029b45e70 : 0xffffff80102edfc1 mach_kernel : _kernel_bootstrap_thread + 0x191
0xffffffa029b45fa0 : 0xffffff8010507f0e mach_kernel : _call_continuation + 0x2e
      Kernel Extensions in backtrace:
         com.apple.kec.corecrypto(11.1)[F5F1255F-6552-3CF4-A9DB-D60EFDEB4A9A]@0xffffff8013942000->0xffffff80139d3fff

Process name corresponding to current thread: Unknown
Boot args: keepsyms=1 -v debug=0x108 zone_array_dump=1 gzalloc_alloc_debug=1 zalloc_gz_trace=1

Mac OS version:
Not yet set

Kernel version:
Darwin Kernel Version 20.5.0: Wed May 27 08:49:18 CST 2026; lee:xnu-build/DEBUG_X86_64
Kernel UUID: DA1A96B5-8BD3-36A2-9D30-BC3C159CA98F
KernelCache slide: 0x0000000010000000
KernelCache base:  0xffffff8010200000
Kernel slide:      0x0000000010010000
Kernel text base:  0xffffff8010210000
__HIB  text base: 0xffffff8010100000
System shutdown begun: NO
Panic diags file unavailable, panic occurred prior to initialization
Hibernation exit count: 0

System uptime in nanoseconds: 583251742434
Last Sleep:           absolute           base_tsc          base_nano
  Uptime  : 0x00000087d56f629e
  Sleep   : 0x0000000000000000 0x0000000000000000 0x0000000000000000
  Wake    : 0x0000000000000000 0x0000009b40f0dd05 0x0000000000000000
last started kext at 576293943011: @kec.corecrypto	11.1 (addr 0xffffff8013942000, size 598016)
loaded kexts:
@kec.corecrypto	11.1
@kec.Libm	1
Attempting to commit panic log to NVRAM

** In Memory Panic Stackshot Succeeded ** Bytes Traced 2106 (Uncompressed 4880) **
Attempting to commit panic log to NVRAM

Please go to https://panic.apple.com to report this panic
```
从崩溃堆栈结合源代码分析可以看出崩溃原因是  Debugger: Unexpected kernel trap number: 0x6   =》`trap number: 0x6` = 内核常量 `T_INVALID_OPCODE` = 硬件 #UD

src/Kernel/xnu/osfmk/i386/trap.h

```
#define T_INVALID_OPCODE        6               /* invalid op code */
```



# 2.查找触发 #UD 异常 原因

根据 崩溃 堆栈可知崩溃在 com.apple.kec.corecrypto : _gcm_init 方法

gcm_init (https://github.com/apple/corecrypto/blob/main/ccaes/src/intel/ghash-x86_64.s )

```
# Copyright (c) (2014,2015,2016,2018,2019) Apple Inc. All rights reserved.
#
# corecrypto is licensed under Apple Inc.’s Internal Use License Agreement (which
# is contained in the License.txt file distributed with corecrypto) and only to 
# people who accept that license. IMPORTANT:  Any license rights granted to you by 
# Apple Inc. (if any) are limited to internal use within your organization only on 
# devices and computers you own or control, for the sole purpose of verifying the 
# security characteristics and correct functioning of the Apple Software.  You may 
# not, directly or indirectly, redistribute the Apple Software or any portions thereof.
#include <corecrypto/cc_config.h>
#if  CCAES_INTEL_ASM && defined(__x86_64__)

	.text	
	.p2align	4


	.macro  karatsuba_reduce_to_128
    /* Karatsuba method produces t0 in %xmm3, t1 in %xmm1, t2 in %xmm0 */
    /* %xmm2 = H<<1 mod g(x) */
    movdqa      %xmm0,%xmm1
    pshufd      _IMM(78),%xmm0,%xmm3
    pshufd      _IMM(78),%xmm2,%xmm4
    pxor        %xmm0,%xmm3
    pxor        %xmm2,%xmm4
    pclmulqdq   _IMM(0x0), %xmm2, %xmm0
    pclmulqdq   _IMM(0x11), %xmm2, %xmm1
    pclmulqdq   _IMM(0x0), %xmm4, %xmm3

    /* reduce to 128-bit in %xmm0 */
    pxor       %xmm1, %xmm3
    pxor       %xmm0, %xmm3
    movdqa     %xmm3, %xmm4
    pslldq     _IMM(8), %xmm3
    psrldq     _IMM(8), %xmm4
    pxor       %xmm3, %xmm0
    pxor       %xmm1, %xmm4
    pshufd     _IMM(78), %xmm0, %xmm1
    pclmulqdq  _IMM(0x10), L0x1c2_polynomial(%rip), %xmm0
    pxor       %xmm1, %xmm0
    pshufd     _IMM(78), %xmm0, %xmm1
    pclmulqdq  _IMM(0x10), L0x1c2_polynomial(%rip), %xmm0
    pxor       %xmm1, %xmm4
    pxor       %xmm4, %xmm0
    .endm

	.macro  write_Htable arg0, arg1
    movdqu  %xmm0,\arg0
    pshufd  $78,%xmm0,%xmm3
    pxor    %xmm0,%xmm3
    movdqu  %xmm3,\arg1
    .endm

/*
        void gcm_init(u128 Htable[16], u128 *H);

        the following equation will be used in the computation of A*H

            reflected (A)*reflected (H<<1 mod g(x)) = reflected (A*H) mod g(x)

        this function pre-computes (H^i << 1) mod g(x) for i=1:8
        it also precomputes the corresponding constants that are used in the Karatsuba algorithm. 
*/
.globl	_gcm_init

_gcm_init:

#if CC_KERNEL
    push    %rbp
    mov     %rsp, %rbp
    sub     $5*16, %rsp
    movdqa  %xmm0, 0*16(%rsp)
    movdqa  %xmm1, 1*16(%rsp)
    movdqa  %xmm2, 2*16(%rsp)
    movdqa  %xmm3, 3*16(%rsp)
    movdqa  %xmm4, 4*16(%rsp)
#endif    
	movdqu	(%rsi),%xmm2                    // H = aes_encrypt(0);
	pshufb  L$bswap_mask(%rip), %xmm2       // reflected(H)

    // compute %xmm2 = reflected( H<<1 mod g(x) )
	pshufd	$255,%xmm2,%xmm4                
	movdqa	%xmm2,%xmm3
	psllq	$1,%xmm2
	psrlq	$63,%xmm3
	psrad	$31, %xmm4                       
	pslldq	$8,%xmm3
	por	    %xmm3,%xmm2
	pand	L0x1c2_polynomial(%rip),%xmm4
	pxor	%xmm4,%xmm2                     // reflected(H<<1 mod g(x))

	movdqa	        %xmm2,%xmm0
    write_Htable    0(%rdi), 128(%rdi)
    karatsuba_reduce_to_128
    write_Htable    16(%rdi), 144(%rdi)
    karatsuba_reduce_to_128
    write_Htable    32(%rdi), 160(%rdi)
    karatsuba_reduce_to_128
    write_Htable    48(%rdi), 176(%rdi)
    karatsuba_reduce_to_128
    write_Htable    64(%rdi), 192(%rdi)
    karatsuba_reduce_to_128
    write_Htable    80(%rdi), 208(%rdi)
    karatsuba_reduce_to_128
    write_Htable    96(%rdi), 224(%rdi)
    karatsuba_reduce_to_128
    write_Htable    112(%rdi), 240(%rdi)
#if CC_KERNEL
    movdqa  0*16(%rsp), %xmm0
    movdqa  1*16(%rsp), %xmm1
    movdqa  2*16(%rsp), %xmm2
    movdqa  3*16(%rsp), %xmm3
    movdqa  4*16(%rsp), %xmm4
    mov     %rbp, %rsp
    pop     %rbp
#endif    
	ret

    .globl	_gcm_gmult
    .p2align	4
_gcm_gmult:
#if CC_KERNEL
    push    %rbp
    mov     %rsp, %rbp
    sub     $5*16, %rsp
    movdqa  %xmm0, 0*16(%rsp)
    movdqa  %xmm1, 1*16(%rsp)
    movdqa  %xmm2, 2*16(%rsp)
    movdqa  %xmm3, 3*16(%rsp)
    movdqa  %xmm4, 4*16(%rsp)
#endif    
	movdqu	(%rdi),%xmm0
	movdqu	(%rsi),%xmm2
	pshufb  L$bswap_mask(%rip), %xmm0

    karatsuba_reduce_to_128

	pshufb  L$bswap_mask(%rip), %xmm0
	movdqu	%xmm0,(%rdx)
#if CC_KERNEL
    movdqa  0*16(%rsp), %xmm0
    movdqa  1*16(%rsp), %xmm1
    movdqa  2*16(%rsp), %xmm2
    movdqa  3*16(%rsp), %xmm3
    movdqa  4*16(%rsp), %xmm4
    mov     %rbp, %rsp
    pop     %rbp
#endif    
	ret

```

在 gcm_init 方法中 进行LLDB 逐步调试

```
(lldb)  
Process 1 stopped
* thread #1, stop reason = instruction step into
    frame #0: 0xffffff801398ed0a
->  0xffffff801398ed0a: pxor   %xmm2, %xmm4
    0xffffff801398ed0e: pclmulqdq $0x0, %xmm2, %xmm0
    0xffffff801398ed14: pclmulqdq $0x11, %xmm2, %xmm1
    0xffffff801398ed1a: pclmulqdq $0x0, %xmm4, %xmm3
Target 1: (boot.efi) stopped.
(lldb)  
Process 1 stopped
* thread #1, stop reason = instruction step into
    frame #0: 0xffffff801398ed0e
->  0xffffff801398ed0e: pclmulqdq $0x0, %xmm2, %xmm0
    0xffffff801398ed14: pclmulqdq $0x11, %xmm2, %xmm1
    0xffffff801398ed1a: pclmulqdq $0x0, %xmm4, %xmm3
    0xffffff801398ed20: pxor   %xmm1, %xmm3
Target 1: (boot.efi) stopped.
(lldb)  
Process 1 stopped
* thread #1, stop reason = instruction step into
    frame #0: 0xfffff6b0c00a9690
->  0xfffff6b0c00a9690: pushq  $0x0
    0xfffff6b0c00a9692: pushq  %rax
    0xfffff6b0c00a9693: popq   %rax
    0xfffff6b0c00a9694: pushq  $0x1
Target 1: (boot.efi) stopped.
(lldb) n
Process 1 stopped
* thread #1, stop reason = instruction step over
    frame #0: 0xfffff6b0c00a9692
->  0xfffff6b0c00a9692: pushq  %rax
    0xfffff6b0c00a9693: popq   %rax
    0xfffff6b0c00a9694: pushq  $0x1
    0xfffff6b0c00a9696: pushq  $0x6
Target 1: (boot.efi) stopped.
(lldb)  


```

发现执行 ->  0xffffff801398ed0e: pclmulqdq $0x0, %xmm2, %xmm0 时触发了 #UD 异常

由于是在 qemu 调试，所以检查 qemu 参数

当前参数

```
qemu-system-x86_64 -s -S -m 4096 -cpu Penryn,kvm=on,vendor=GenuineIntel,+invtsc,vmware-cpuid-freq=on,+ssse3,+sse4.2,+popcnt,+avx,+aes,+xsave,+xsaveopt,check -machine q35 -usb -device usb-kbd -device usb-tablet -smp 4,cores=2,sockets=1 -device usb-ehci,id=ehci -device nec-usb-xhci,id=xhci -global nec-usb-xhci.msi=off -device isa-applesmc,osk='ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc' -drive if=pflash,format=raw,readonly=on,file=././OVMF_CODE.fd -smbios type=2 -device ich9-intel-hda -device hda-duplex -device ich9-ahci,id=sata -drive file=./OpenCore/OpenCore-master.iso,if=none,id=OpenCoreBoot,format=raw, -device ide-hd,bus=sata.2,drive=OpenCoreBoot -device ide-hd,bus=sata.3,drive=InstallMedia -drive id=InstallMedia,if=none,file=./BaseSystem.img,format=raw -drive id=MacHDD,if=none,file=./mac_hdd_ng.img,format=qcow2 -device ide-hd,bus=sata.4,drive=MacHDD -netdev user,id=net0,hostfwd=tcp::2222-:22 -serial stdio -device vmware-svga -debugcon file:debug.log -global isa-debugcon.iobase=0x402 
```

发现 -cpu 缺少 +pclmulqdq 是导致本次 #UD 崩溃的原因



# 3.修复

为 qemu 启动 -cpu 参数添加 +pclmulqdq  ，修改后为

```
qemu-system-x86_64 -s -S -m 4096 -cpu Penryn,kvm=on,vendor=GenuineIntel,+invtsc,vmware-cpuid-freq=on,+ssse3,+sse4.2,+popcnt,+avx,+aes,+pclmulqdq,+xsave,+xsaveopt,check -machine q35 -usb -device usb-kbd -device usb-tablet -smp 4,cores=2,sockets=1 -device usb-ehci,id=ehci -device nec-usb-xhci,id=xhci -global nec-usb-xhci.msi=off -device isa-applesmc,osk='ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc' -drive if=pflash,format=raw,readonly=on,file=././OVMF_CODE.fd -smbios type=2 -device ich9-intel-hda -device hda-duplex -device ich9-ahci,id=sata -drive file=./OpenCore/OpenCore-master.iso,if=none,id=OpenCoreBoot,format=raw, -device ide-hd,bus=sata.2,drive=OpenCoreBoot -device ide-hd,bus=sata.3,drive=InstallMedia -drive id=InstallMedia,if=none,file=./BaseSystem.img,format=raw -drive id=MacHDD,if=none,file=./mac_hdd_ng.img,format=qcow2 -device ide-hd,bus=sata.4,drive=MacHDD -netdev user,id=net0,hostfwd=tcp::2222-:22 -serial stdio -device vmware-svga -debugcon file:debug.log -global isa-debugcon.iobase=0x402 
```

调整后，启动qemu 使用LLDB调试，在相同断点不再触发崩溃，解决了这个问题；

# 1.重新编译xnu 内核并使用 LLDB 调试崩溃了

堆栈

```

(lldb) c
Process 1 resuming
Process 1 stopped
* thread #1, stop reason = breakpoint 7.1
    frame #0: 0xffffff8010eda4c0 kernel.debug`panic(str="Kernel trap at 0x%016llx, type %d=%s, registers:\nCR0: 0x%016llx, CR2: 0x%016llx, CR3: 0x%016llx, CR4: 0x%016llx\nRAX: 0x%016llx, RBX: 0x%016llx, RCX: 0x%016llx, RDX: 0x%016llx\nRSP: 0x%016llx, RBP: 0x%016llx, RSI: 0x%016llx, RDI: 0x%016llx\nR8:  0x%016llx, R9:  0x%016llx, R10: 0x%016llx, R11: 0x%016llx\nR12: 0x%016llx, R13: 0x%016llx, R14: 0x%016llx, R15: 0x%016llx\nRFL: 0x%016llx, RIP: 0x%016llx, CS:  0x%016llx, SS:  0x%016llx\nFault CR2: 0x%016llx, Error code: 0x%016llx, Fault CPU: 0x%x%s%s%s%s, PL: %d, VF: %d\n") at debug.c:800:10
   797     void
   798     panic(const char *str, ...)
   799     {
-> 800         va_list panic_str_args;
   801     
   802         va_start(panic_str_args, str);
   803         panic_trap_to_debugger(str, &panic_str_args, 0, NULL, 0, NULL, (unsigned long)(char *)__builtin_return_address(0));
Target 1: (boot.efi) stopped.
(lldb) bt
* thread #1, stop reason = breakpoint 7.1
  * frame #0: 0xffffff8010eda4c0 kernel.debug`panic(str="Kernel trap at 0x%016llx, type %d=%s, registers:\nCR0: 0x%016llx, CR2: 0x%016llx, CR3: 0x%016llx, CR4: 0x%016llx\nRAX: 0x%016llx, RBX: 0x%016llx, RCX: 0x%016llx, RDX: 0x%016llx\nRSP: 0x%016llx, RBP: 0x%016llx, RSI: 0x%016llx, RDI: 0x%016llx\nR8:  0x%016llx, R9:  0x%016llx, R10: 0x%016llx, R11: 0x%016llx\nR12: 0x%016llx, R13: 0x%016llx, R14: 0x%016llx, R15: 0x%016llx\nRFL: 0x%016llx, RIP: 0x%016llx, CS:  0x%016llx, SS:  0x%016llx\nFault CR2: 0x%016llx, Error code: 0x%016llx, Fault CPU: 0x%x%s%s%s%s, PL: %d, VF: %d\n") at debug.c:800:10
    frame #1: 0xffffff8010579062 kernel.debug`panic_trap(regs=0xffffff80101a6400, pl=1, fault_result=0) at trap.c:896:2
    frame #2: 0xffffff80105787cd kernel.debug`kernel_trap(state=0xffffff80101a63f0, lo_spp=0xffffff80101a63d0) at trap.c:834:2
    frame #3: 0xffffff8010598b2f kernel.debug`trap_from_kernel + 38
    frame #4: 0xffffff8010599f55 kernel.debug`counter_inc(counter=0x0000000000000398) at counter.c:77:4
    frame #5: 0xffffff801042fdb2 kernel.debug`vm_fault_internal(map=0xffffff8011ed5100, vaddr=18446743693081014272, caller_prot=3, change_wiring=0, wire_tag=0, interruptible=0, caller_pmap=0x0000000000000000, caller_pmap_addr=0, physpage_p=0x0000000000000000) at vm_fault.c:4008:2
    frame #6: 0xffffff801042f317 kernel.debug`_vm_fault$XNU_INTERNAL(map=0xffffff8011ed5100, vaddr=18446743693081014272, fault_type=3, change_wiring=0, wire_tag=0, interruptible=0, caller_pmap=0x0000000000000000, caller_pmap_addr=0) at vm_fault.c:3725:9
    frame #7: 0xffffff80105786aa kernel.debug`kernel_trap(state=0xffffff80101a6d70, lo_spp=0xffffff80101a6d50) at trap.c:761:27
    frame #8: 0xffffff8010598b2f kernel.debug`trap_from_kernel + 38
    frame #9: 0xffffff8010547307 kernel.debug`memcpy + 7
    frame #10: 0xffffff8010317309 kernel.debug`ledger_entry_add(template=0xffffff934ccadfc0, key="wired_mem", group="physmem", units="bytes") at ledger.c:291:3
    frame #11: 0xffffff8010357cb0 kernel.debug`init_task_ledgers at task.c:1050:27
    frame #12: 0xffffff80102f92b5 kernel.debug`coalitions_init at coalition.c:2066:2
    frame #13: 0xffffff8010351a88 kernel.debug`kernel_bootstrap at startup.c:477:2
    frame #14: 0xffffff8010581ede kernel.debug`machine_startup at model_dep.c:332:2
    frame #15: 0xffffff80105584b4 kernel.debug`i386_init at i386_init.c:1118:2
    frame #16: 0xffffff801056afa8 kernel.debug`x86_init_wrapper + 8
(lldb)


(lldb)  
Process 1 stopped
* thread #1, stop reason = step over
    frame #0: 0xffffff801031729f kernel.debug`ledger_entry_add(template=0xffffff934cd87800, key="wired_mem", group="physmem", units="bytes") at ledger.c:286:17
   283 				template_unlock(template);
   284 				return -1;
   285 			}
-> 286 			new_entries = kalloc(new_sz);
   287 			if (new_entries == NULL) {
   288 				template_unlock(template);
   289 				return -1;
Target 1: (boot.efi) stopped.
(lldb) p/x new_sz
(int) 0x00000380
(lldb) n
Process 1 stopped
* thread #1, stop reason = step over
    frame #0: 0xffffff80103172d0 kernel.debug`ledger_entry_add(template=0xffffff934cd87800, key="wired_mem", group="physmem", units="bytes") at ledger.c:287:19
   284 				return -1;
   285 			}
   286 			new_entries = kalloc(new_sz);
-> 287 			if (new_entries == NULL) {
   288 				template_unlock(template);
   289 				return -1;
   290 			}
Target 1: (boot.efi) stopped.
(lldb) p/x new_entries
(entry_template *) 0xffffffa019b24000


```
从崩溃堆栈结合源代码分析可以看出崩溃原因是 kalloc 返回的 0xffffffa019b24000 在进行memcpy 触发 vm_fault_internal 

# 2.查找触发 vm_fault_internal 原因

考虑是 new_entries = kalloc(new_sz); 返回的 new_entries 虚拟地址没有映射物理地址导致的

ledger_entry_add方法 (src/Kernel/xnu/osfmk/kern/ledger.c)

```

/*
 * Add a new entry to the list of entries in a ledger template. There is
 * currently no mechanism to remove an entry.  Implementing such a mechanism
 * would require us to maintain per-entry reference counts, which we would
 * prefer to avoid if possible.
 */
/*
 * 中文概要：往 ledger 模板表里追加一条计量项定义（key/分组/单位），返回该条在 lt_entries[]
 * 中的索引，供运行时 ledger_t 与同序号的余额数组对应。-1 表示参数非法或 kalloc 失败。
 * （当前不提供删除条目，以免要维护每条目的引用计数。）
 */
int
ledger_entry_add(ledger_template_t template, const char *key,
    const char *group, const char *units)
{
	/* 成功时设为 template->lt_cnt++ 之前的计数，作为返回给调用方的新条目索引。 */
	int idx;
	/* 指向 lt_entries[idx]（即表中「下一条要写」的那一个 entry_template 槽）。 */
	struct entry_template *et;

	/* key 不能为空；长度必须 < LEDGER_NAME_MAX（留尾随 NUL）；已关联 zalloc zone 实例的模板禁止再改条目表。 */
	if ((key == NULL) || (strlen(key) >= LEDGER_NAME_MAX) || (template->lt_zone != NULL)) {
		return -1;
	}

	/* 保护 lt_cnt / lt_entries / lt_table_size 与扩容逻辑不被并发破坏。 */
	template_lock(template);

	/* If the table is full, attempt to double its size */
	/* 已用条目数等于当前容量 → 无法再尾插，需把条目表整块翻倍重分配。 */
	if (template->lt_cnt == template->lt_table_size) {
		/* new_entries：新分配的更大数组；old_entries：待 kfree 的旧指针。s：升 IPL 时用。 */
		struct entry_template *new_entries, *old_entries;
		/* 扩容前后的「条目个数」及对应字节长度；new_sz 为翻倍后字节数。 */
		int old_cnt, old_sz, new_sz = 0;
		spl_t s;

		/* 当前表大小（条目数）；下面 old_sz / new_sz 均按 struct entry_template 计字节。 */
		old_cnt = template->lt_table_size;
		old_sz = old_cnt * (int)(sizeof(struct entry_template));
		/* double old_sz allocation, but check for overflow */
		/* old_sz * 2 溢出则拒绝扩容避免错误大小 kalloc。 */
		if (os_mul_overflow(old_sz, 2, &new_sz)) {
			template_unlock(template);
			return -1;
		}
		/* 按翻倍后字节数分配新条目表（内容尚未初始化后半段）。 */
		new_entries = kalloc(new_sz);
		if (new_entries == NULL) {
			template_unlock(template);
			return -1;
		}
		/* 把旧表中已有 entry_template 原样拷贝到新表前半（保持已有 key/索引不变）。 */
		memcpy(new_entries, template->lt_entries, old_sz);
		/* 新表后半段清零：对应新增槽位默认为空结构，避免读到垃圾。 */
		memset(((char *)new_entries) + old_sz, 0, old_sz);
		/* assume: if the sz didn't overflow, neither will the count */
		/* 容量（可容纳条目数）翻倍；条目数 lt_cnt 暂不变——随后仍插在 lt_cnt。 */
		template->lt_table_size = old_cnt * 2;

		/* 暂存旧数组指针以便 kfree（在换掉 template->lt_entries 之后）。 */
		old_entries = template->lt_entries;

		/* 升到调度级 IPL 并把 lt_inuse CAS 置 1：与读模板处配合，换掉 lt_entries 指针时避免出现撕裂读。 */
		TEMPLATE_INUSE(s, template);
		template->lt_entries = new_entries;
		TEMPLATE_IDLE(s, template);

		kfree(old_entries, old_sz);
	}

	/* 下一空闲槽等于当前条目个数；拷贝完后再 lt_cnt++。 */
	et = &template->lt_entries[template->lt_cnt];
	/* key / group / units 三组显示名拷贝进模板条目（均被截断在 LEDGER_NAME_MAX 以内）。 */
	strlcpy(et->et_key, key, LEDGER_NAME_MAX);
	strlcpy(et->et_group, group, LEDGER_NAME_MAX);
	strlcpy(et->et_units, units, LEDGER_NAME_MAX);
	/* 新条目默认可记账（未 inactive）。 */
	et->et_flags = LF_ENTRY_ACTIVE;
	/* 暂未挂回调（例如告警时可由运行时再设）。 */
	et->et_callback = NULL;

	/* 对外返回的条目索引即为追加前计数；此后模板内条目数加一。 */
	idx = template->lt_cnt++;
	template_unlock(template);

	return idx;
}


```


kalloc (src/Kernel/xnu/osfmk/kern/kalloc.h) -> kheap_alloc(src/Kernel/xnu/osfmk/kern/kalloc.h) ->  zalloc_ext (src/Kernel/xnu/osfmk/kern/zalloc.c) -> zalloc_gz (src/Kernel/xnu/osfmk/kern/zalloc.c) -> zalloc_return (src/Kernel/xnu/osfmk/kern/zalloc.c) -> zone_element_addr (src/Kernel/xnu/osfmk/kern/zalloc.c)

kalloc (src/Kernel/xnu/osfmk/kern/kalloc.h)

```

#define kalloc(size) \
	kheap_alloc(KHEAP_DEFAULT, size, Z_WAITOK)


```

kheap_alloc(src/Kernel/xnu/osfmk/kern/kalloc.h)

```

#define kheap_alloc(kalloc_heap, size, flags)                           \
	({ VM_ALLOC_SITE_STATIC(0, 0);                                  \
	kalloc_ext(kalloc_heap, size, flags, &site).addr; })



```

zalloc_ext (src/Kernel/xnu/osfmk/kern/zalloc.c)

```

/*!
 * @function zalloc_ext
 *
 * @brief
 * The core implementation of @c zalloc(), @c zalloc_flags(), @c zalloc_percpu().
 */
/*
 * zalloc 族统一入口：给定 zone、统计视图 zstats、flags，返回指向一块 zone 元素的指针。
 * zone_t：分区描述；zone_stats：该 view/kheap 的 per-cpu 统计；zalloc_flags：Z_WAITOK/Z_NOWAIT/Z_NOFAIL 等。
 * 正文：断言「可阻塞/上下文」与 Z_NOFAIL 用法，必要时走 gzalloc，再在「pcpu magazine」与「zalloc_item」间二选一。
 */
void *
zalloc_ext(zone_t zone, zone_stats_t zstats, zalloc_flags_t flags)
{
	/*
	 * KASan uses zalloc() for fakestack, which can be called anywhere.
	 * However, we make sure these calls can never block.
	 */
	assert(zone->kasan_fakestacks ||
	    ml_get_interrupts_enabled() ||
	    ml_is_quiescing() ||
	    debug_mode_active() ||
	    startup_phase < STARTUP_SUB_EARLY_BOOT);

	/*
	 * Make sure Z_NOFAIL was not obviously misused
	 */
	if (zone->z_replenishes) {
		assert((flags & (Z_NOWAIT | Z_NOPAGEWAIT)) == 0);
	} else if (flags & Z_NOFAIL) {
		assert(!zone->exhaustible &&
		    (flags & (Z_NOWAIT | Z_NOPAGEWAIT)) == 0);
	}

#if CONFIG_GZALLOC
	if (__improbable(zone->gzalloc_tracked)) {
		return zalloc_gz(zone, zstats, flags);
	}
#endif /* CONFIG_GZALLOC */

	if (zone->z_pcpu_cache) {
		return zalloc_cached(zone, zstats, flags);
	}

	return zalloc_item(zone, zstats, flags);
}


```

zalloc_gz (src/Kernel/xnu/osfmk/kern/zalloc.c)

```

/*!
 * @function zalloc_gz
 *
 * @brief
 * Performs allocations for zones using gzalloc.
 *
 * @discussion
 * This function is noinline so that it doesn't affect the codegen
 * of the fastpath.
 */
__attribute__((noinline))
static void *
zalloc_gz(zone_t zone, zone_stats_t zstats, zalloc_flags_t flags)
{
	vm_offset_t addr = gzalloc_alloc(zone, zstats, flags);
	return zalloc_return(zone, zone_element_encode(addr, 0, ZPM_AUTO),
	           flags, zone_elem_size(zone), NULL);
}

```


zalloc_return (src/Kernel/xnu/osfmk/kern/zalloc.c)

```


/*!
 * @function zalloc_return
 *
 * @brief
 * Performs the tail-end of the work required on allocations before the caller
 * uses them.
 *
 * @discussion
 * This function is called without any zone lock held,
 * and preemption back to the state it had when @c zalloc_ext() was called.
 *
 * @param zone          The zone we're allocating from.
 * @param ze            The encoded element we just allocated.
 * @param flags         The flags passed to @c zalloc_ext() (for Z_ZERO).
 * @param elem_size     The element size for this zone.
 * @param freemag       An optional magazine that needs to be freed.
 */
__attribute__((noinline))
static void *
/* freemag：若为从 magazine/depot 换新弹夹时换下的一叠空杂志，在此处释放：item_fast 通常为 NULL。 */
zalloc_return(zone_t zone, zone_element_t ze, zalloc_flags_t flags,
    vm_offset_t elem_size, zone_magazine_t freemag)
{
	/* 把编码后的元素 ze 转成「当前 elem_size」下的线性内核虚拟地址（供清零/tag 使用）。 */
	vm_offset_t addr = zone_element_addr(ze, elem_size);

#if KASAN_ZALLOC
	/* KASAN：shadow 标记整段对象为 ASAN_VALID，后续 load/store 才合法。 */
	if (zone->z_percpu) {
		/* per-cpu zone：每个 CPU 有独立偏移副本，对每个副本各标 VALID。 */
		zpercpu_foreach_cpu(i) {
			kasan_poison_range(addr + ptoa(i), elem_size,
			    ASAN_VALID);
		}
	} else {
		/* 普通 zone：单块连续 VA 标记即可。 */
		kasan_poison_range(addr, elem_size, ASAN_VALID);
	}
#endif
#if ZALLOC_ENABLE_POISONING
	/* 可选运行时毒化/校验（与 CANARY 一类），检出元数据与用户大小不一致等问题。 */
	zalloc_validate_element(zone, addr, elem_size, zone_element_prot(ze));
#endif /* ZALLOC_ENABLE_POISONING */
#if ZONE_ENABLE_LOGGING || CONFIG_ZLEAKS
	/* 日志或泄漏检测：按 zone/大小概率采样，记下分配点栈。 */
	if (__improbable(zalloc_should_log_or_trace_leaks(zone, elem_size))) {
		zalloc_log_or_trace_leaks(zone, addr, __builtin_frame_address(0));
	}
#endif /* ZONE_ENABLE_LOGGING || CONFIG_ZLEAKS */
#if KASAN_ZALLOC
	/* Zone 配置了左右 redzone：缩小「用户可见」长度并在两侧留白给 KASAN。 */
	if (zone->z_kasan_redzone) {
		addr = kasan_alloc(addr, elem_size,
		    elem_size - 2 * zone->z_kasan_redzone,
		    zone->z_kasan_redzone);
		elem_size -= 2 * zone->z_kasan_redzone;
	}
	/*
	 * Initialize buffer with unique pattern only if memory
	 * wasn't expected to be zeroed.
	 */
	/* 空闲回收未保证零且未要求 Z_ZERO 时写入 KASAN 泄漏追踪图案（便于认出未初始化使用）。 */
	if (!zone->z_free_zeroes && !(flags & Z_ZERO)) {
		kasan_leak_init(addr, elem_size);
	}
#endif /* KASAN_ZALLOC */
	/* 调用方要带 Z_ZERO 而该 zone free 时不自动清零 → 在此处整块 bzero。 */
	if ((flags & Z_ZERO) && !zone->z_free_zeroes) {
		bzero((void *)addr, elem_size);
	}

#if VM_MAX_TAG_ZONES
	/* 打开 tag 统计的 zone：把 vm_tag 写入元素旁 slot，更新按 tag 的计数。 */
	if (__improbable(zone->tags)) {
		/* flags 里的 tag；未指定则用通用 kalloc 类。 */
		vm_tag_t tag = zalloc_flags_get_tag(flags);
		if (tag == VM_KERN_MEMORY_NONE) {
			tag = VM_KERN_MEMORY_KALLOC;
		}
		// set the tag with b0 clear so the block remains inuse
		/* 存入 (*2) 且保持 bit0 为 0：与「释放时 bit0 置位」一类约定兼容，表示仍为 in-use。 */
		*ztSlot(zone, addr) = (vm_tag_t)(tag << 1);
		vm_tag_update_zone_size(tag, zone->tag_zone_index,
		    (long)elem_size);
	}
#endif /* VM_MAX_TAG_ZONES */

	/* Mach 泄漏探测器与 DTrace 探针（大小与指针）。 */
	TRACE_MACHLEAKS(ZALLOC_CODE, ZALLOC_CODE_2, elem_size, addr);
	DTRACE_VM2(zalloc, zone_t, zone, void*, addr);
	/* 若在 cached 路径上替换了 magazine，这里归还换下的空 magazine。 */
	if (freemag) {
		zone_magazine_free(freemag);
	}
	/* 契约：无 zone 锁、抢占状态与进入 zalloc_ext 时一致；返回用户缓冲区指针。 */
	return (void *)addr;
}

```

zone_element_encode (src/Kernel/xnu/osfmk/kern/zalloc.c)

```

static zone_element_t
zone_element_encode(vm_offset_t base, vm_offset_t eidx, zprot_mode_t zpm)
{
	return (zone_element_t){ .ze_value = base | (eidx << 2) | zpm };
}

```

zone_element_addr (src/Kernel/xnu/osfmk/kern/zalloc.c)

```

static vm_offset_t
zone_element_addr(zone_element_t ze, vm_offset_t esize)
{
	return zone_element_base(ze) + esize * zone_element_idx(ze);
}

```

## 崩溃原因

zalloc_gz 里写的是：

```

zone_element_encode(raw_addr, 0, ZPM_AUTO)  

```

ze = zone_element_encode(raw_addr, 0, ZPM_AUTO) ≡ raw_addr (ZPM_AUTO == 0)


idx = (ze_value & PAGE_MASK) >> 2; 

vm_offset_t addr = zone_element_addr(ze, elem_size); =  trunc_page(ze) + esize * idx; = ze + esize * idx != ze


返回的虚拟地址 addr 比 已经映射物理地址的 raw_addr 高了  esize * idx ，导致返回的虚拟地址 addr 没有 映射物理地址


# 3.apple 原生 BootKernelExtensions.kc 存在这个问题吗？


zalloc_ext (src/Kernel/xnu/osfmk/kern/zalloc.c)

```

/*!
 * @function zalloc_ext
 *
 * @brief
 * The core implementation of @c zalloc(), @c zalloc_flags(), @c zalloc_percpu().
 */
/*
 * zalloc 族统一入口：给定 zone、统计视图 zstats、flags，返回指向一块 zone 元素的指针。
 * zone_t：分区描述；zone_stats：该 view/kheap 的 per-cpu 统计；zalloc_flags：Z_WAITOK/Z_NOWAIT/Z_NOFAIL 等。
 * 正文：断言「可阻塞/上下文」与 Z_NOFAIL 用法，必要时走 gzalloc，再在「pcpu magazine」与「zalloc_item」间二选一。
 */
void *
zalloc_ext(zone_t zone, zone_stats_t zstats, zalloc_flags_t flags)
{
  ...

#if CONFIG_GZALLOC
	if (__improbable(zone->gzalloc_tracked)) {
		return zalloc_gz(zone, zstats, flags);
	}
#endif /* CONFIG_GZALLOC */

   ...
}


```

## 1.lldb调试 apple 原生 BootKernelExtensions.kc 观察 有没有进入 return zalloc_gz(zone, zstats, flags);


apple 原生 BootKernelExtensions.kc 对应汇编代码

```

; === zalloc_ext 入口 ===
FFFFFF80002EC1B0  push    rbp
FFFFFF80002EC1B1  mov     rbp, rsp
; zalloc.c:6246  if (zone->gzalloc_tracked)  ← 你要验证的分支
FFFFFF80002EC1B4  test    byte ptr [rdi+3Eh], 2    ; gzalloc_tracked (bit1)
FFFFFF80002EC1B8  jnz     short loc_FFFFFF80002EC216 ; taken → gzalloc 路径

```

 进行lldb调试，发现 apple 原生 BootKernelExtensions.kc 并没有调用 return zalloc_gz(zone, zstats, flags);

 ## 2.apple 原生 BootKernelExtensions.kc 为什么没有调用 return zalloc_gz(zone, zstats, flags); ？


gzalloc_configure (src/Kernel/xnu/osfmk/kern/gzalloc.c)
```

__startup_func
static void
gzalloc_configure(void)
{
#if !KASAN_ZALLOC
	char temp_buf[16];

	if (PE_parse_boot_argn("-gzalloc_mode", temp_buf, sizeof(temp_buf))) {
		gzalloc_mode = TRUE;
		gzalloc_min = GZALLOC_MIN_DEFAULT;
		gzalloc_max = ~0U;
	}

	if (PE_parse_boot_argn("gzalloc_min", &gzalloc_min, sizeof(gzalloc_min))) {
		gzalloc_mode = TRUE;
		gzalloc_max = ~0U;
	}

	if (PE_parse_boot_argn("gzalloc_max", &gzalloc_max, sizeof(gzalloc_max))) {
		gzalloc_mode = TRUE;
		if (gzalloc_min == ~0U) {
			gzalloc_min = 0;
		}
	}

	if (PE_parse_boot_argn("gzalloc_size", &gzalloc_size, sizeof(gzalloc_size))) {
		gzalloc_min = gzalloc_max = gzalloc_size;
		gzalloc_mode = TRUE;
	}

	(void)PE_parse_boot_argn("gzalloc_fc_size", &gzfc_size, sizeof(gzfc_size));

	if (PE_parse_boot_argn("-gzalloc_wp", temp_buf, sizeof(temp_buf))) {
		gzalloc_prot = VM_PROT_READ;
	}

	if (PE_parse_boot_argn("-gzalloc_uf_mode", temp_buf, sizeof(temp_buf))) {
		gzalloc_uf_mode = TRUE;
		gzalloc_guard = KMA_GUARD_FIRST;
	}

	if (PE_parse_boot_argn("-gzalloc_no_dfree_check", temp_buf, sizeof(temp_buf))) {
		gzalloc_dfree_check = FALSE;
	}

	(void) PE_parse_boot_argn("gzalloc_zscale", &gzalloc_zonemap_scale, sizeof(gzalloc_zonemap_scale));

	if (PE_parse_boot_argn("-gzalloc_noconsistency", temp_buf, sizeof(temp_buf))) {
		gzalloc_consistency_checks = FALSE;
	}

	if (PE_parse_boot_argn("gzname", gznamedzone, sizeof(gznamedzone))) {
		gzalloc_mode = TRUE;
	}
#if DEBUG
	if (gzalloc_mode == FALSE) {
		gzalloc_min = 1024;
		gzalloc_max = 1024;
		strlcpy(gznamedzone, "pmap", sizeof(gznamedzone));
		gzalloc_prot = VM_PROT_READ;
		gzalloc_mode = TRUE;
	}
#endif
	if (PE_parse_boot_argn("-nogzalloc_mode", temp_buf, sizeof(temp_buf))) {
		gzalloc_mode = FALSE;
	}

	if (gzalloc_mode) {
		gzalloc_reserve_size = GZALLOC_RESERVE_SIZE_DEFAULT;
		gzalloc_reserve = (vm_offset_t) pmap_steal_memory(gzalloc_reserve_size);
	}
#endif
}

```

差异点是 

```

#if DEBUG
	if (gzalloc_mode == FALSE) {
		gzalloc_min = 1024;
		gzalloc_max = 1024;
		strlcpy(gznamedzone, "pmap", sizeof(gznamedzone));
		gzalloc_prot = VM_PROT_READ;
		gzalloc_mode = TRUE;
	}
#endif


```

RELEASE（Apple 零售/RELEASE KC）：没有上面 #if DEBUG 块 → 除非 boot-arg 显式开启，否则 gzalloc_mode 一直为 FALSE → gzalloc_zone_init() 直接 return，所有 zone 的 gzalloc_tracked 保持 0 → zalloc_ext 永远不会进 zalloc_gz。


所有 让 gzalloc_mode = 0 ，即可和 apple 原生 BootKernelExtensions.kc kalloc 调用路径一致


## 2.自己编译的内核如何关闭 gzalloc_mode ？


在编译的引导文件 OpenCore-master.iso config.plist boot-arg 新增 -nogzalloc_mode即可，boot-arg="keepsyms=1 -v debug=0x108 zone_array_dump=1 gzalloc_alloc_debug=1 -nogzalloc_mode"

## 3.自己编译的内核关闭 gzalloc_mode 后效果

可以看到虽然还是有崩溃，但是已经 不是之前崩溃了，而是后面新的崩溃，已经解决 kalloc 返回的 虚拟地址没有映射物理地址的问题


```

(lldb) c
Process 1 resuming
Process 1 stopped
* thread #1, stop reason = breakpoint 7.1 8.1
    frame #0: 0xffffff8010a9d7f4 BootKernelExtensions_o.kc`panic(str="Kernel trap at 0x%016llx, type %d=%s, registers:\nCR0: 0x%016llx, CR2: 0x%016llx, CR3: 0x%016llx, CR4: 0x%016llx\nRAX: 0x%016llx, RBX: 0x%016llx, RCX: 0x%016llx, RDX: 0x%016llx\nRSP: 0x%016llx, RBP: 0x%016llx, RSI: 0x%016llx, RDI: 0x%016llx\nR8:  0x%016llx, R9:  0x%016llx, R10: 0x%016llx, R11: 0x%016llx\nR12: 0x%016llx, R13: 0x%016llx, R14: 0x%016llx, R15: 0x%016llx\nRFL: 0x%016llx, RIP: 0x%016llx, CS:  0x%016llx, SS:  0x%016llx\nFault CR2: 0x%016llx, Error code: 0x%016llx, Fault CPU: 0x%x%s%s%s%s, PL: %d, VF: %d\n") at debug.c:802:2 [opt]
BootKernelExtensions_o.kc`panic:
->  0xffffff8010a9d7f4 <+46>: movq   %rcx, (%rsi)
    0xffffff8010a9d7f7 <+49>: leaq   0x10(%rbp), %rcx
    0xffffff8010a9d7fb <+53>: movq   %rcx, 0x8(%rsi)
    0xffffff8010a9d7ff <+57>: movq   %rax, 0x10(%rsi)
Target 1: (boot.efi) stopped.
(lldb) bt
* thread #1, stop reason = breakpoint 7.1 8.1
  * frame #0: 0xffffff8010a9d7f4 BootKernelExtensions_o.kc`panic(str="Kernel trap at 0x%016llx, type %d=%s, registers:\nCR0: 0x%016llx, CR2: 0x%016llx, CR3: 0x%016llx, CR4: 0x%016llx\nRAX: 0x%016llx, RBX: 0x%016llx, RCX: 0x%016llx, RDX: 0x%016llx\nRSP: 0x%016llx, RBP: 0x%016llx, RSI: 0x%016llx, RDI: 0x%016llx\nR8:  0x%016llx, R9:  0x%016llx, R10: 0x%016llx, R11: 0x%016llx\nR12: 0x%016llx, R13: 0x%016llx, R14: 0x%016llx, R15: 0x%016llx\nRFL: 0x%016llx, RIP: 0x%016llx, CS:  0x%016llx, SS:  0x%016llx\nFault CR2: 0x%016llx, Error code: 0x%016llx, Fault CPU: 0x%x%s%s%s%s, PL: %d, VF: %d\n") at debug.c:802:2 [opt]
    frame #1: 0xffffff80103c58f6 BootKernelExtensions_o.kc`panic_trap(regs=0xffffffa028ac3660, pl=<unavailable>, fault_result=<unavailable>) at trap.c:841:2 [opt]
    frame #2: 0xffffff80103c55dd BootKernelExtensions_o.kc`kernel_trap(state=<unavailable>, lo_spp=<unavailable>) at trap.c:780:2 [opt]
    frame #3: 0xffffff8010232a2f BootKernelExtensions_o.kc`trap_from_kernel + 38
    frame #4: 0xffffff8013590d0e
    frame #5: 0xffffff8013562a5d
    frame #6: 0xffffff801355c2ed
    frame #7: 0xffffff801354df61
    frame #8: 0xffffff8013547126
    frame #9: 0xffffff80135a1f96
    frame #10: 0xffffff801095cccc BootKernelExtensions_o.kc`OSKext::start(this=<unavailable>, startDependenciesFlag=<unavailable>) at OSKext.cpp:7462:12 [opt]
    frame #11: 0xffffff801095a22c BootKernelExtensions_o.kc`OSKext::load(this=0xffffff934f0743a0, startOpt='\0', startMatchingOpt='\x02', personalityNames=0x0000000000000000) at OSKext.cpp:5845:13 [opt]
    frame #12: 0xffffff801096f12d BootKernelExtensions_o.kc`OSKext::loadKextWithIdentifier(kextIdentifier=0xffffff86828b5620, kextRef=0x0000000000000000, allowDeferFlag=false, delayAutounloadFlag=false, startOpt='\0', startMatchingOpt='\x02', personalityNames=0x0000000000000000) at OSKext.cpp:5232:20 [opt]
    frame #13: 0xffffff801096ef43 BootKernelExtensions_o.kc`OSKext::loadKextWithIdentifier(kextIdentifierCString="com.apple.kec.corecrypto", allowDeferFlag=false, delayAutounloadFlag=false, startOpt='\0', startMatchingOpt='\x02', personalityNames=0x0000000000000000) at OSKext.cpp:5100:11 [opt]
    frame #14: 0xffffff8010c1135e
    frame #15: 0xffffff8010c10ba0
    frame #16: 0xffffff80109b17e1 BootKernelExtensions_o.kc`InitIOKit(dtTop=<unavailable>) at IOStartIOKit.cpp:196:3 [opt]
    frame #17: 0xffffff8010a74864 BootKernelExtensions_o.kc`PE_init_iokit at pe_init.c:191:2 [opt]
    frame #18: 0xffffff80102bc46f BootKernelExtensions_o.kc`kernel_bootstrap_thread at startup.c:661:2 [opt]
    frame #19: 0xffffff801023213e BootKernelExtensions_o.kc`call_continuation + 46
(lldb) c
Process 1 resuming
(lldb) 


```

# 4.总结

本次 kalloc 返回虚拟地址没有映射 物理地址的问题，又是DEBUG 和 RELEASE 编译代码不同代码导致的，通过boot-arg调整 自己编译的 DEBUG xnu 内核 调整和 apple 原生 BootKernelExtensions.kc 一样的 代码逻辑后，解决了这个问题



# 5. 遗留问题


我自己编译的 xnu 内核 即使在DEBUG模式下，逻辑应该是自洽的，我没有修改任何内核代码，不应该会出现 

```
返回的虚拟地址 addr 比 已经映射物理地址的 raw_addr 高了  esize * idx ，导致返回的虚拟地址 addr 没有 映射物理地址
```
这种情况，需要进一步排查


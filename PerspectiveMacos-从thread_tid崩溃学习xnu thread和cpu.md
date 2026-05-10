# 1.重新编译xnu 内核并使用 LLDB 调试崩溃了

堆栈

```

(lldb) bt
* thread #1, stop reason = breakpoint 8.1
  * frame #0: 0xffffff8010eda4d0 kernel.debug`panic(str="Kernel trap at 0x%016llx, type %d=%s, registers:\nCR0: 0x%016llx, CR2: 0x%016llx, CR3: 0x%016llx, CR4: 0x%016llx\nRAX: 0x%016llx, RBX: 0x%016llx, RCX: 0x%016llx, RDX: 0x%016llx\nRSP: 0x%016llx, RBP: 0x%016llx, RSI: 0x%016llx, RDI: 0x%016llx\nR8:  0x%016llx, R9:  0x%016llx, R10: 0x%016llx, R11: 0x%016llx\nR12: 0x%016llx, R13: 0x%016llx, R14: 0x%016llx, R15: 0x%016llx\nRFL: 0x%016llx, RIP: 0x%016llx, CS:  0x%016llx, SS:  0x%016llx\nFault CR2: 0x%016llx, Error code: 0x%016llx, Fault CPU: 0x%x%s%s%s%s, PL: %d, VF: %d\n") at debug.c:800:10
    frame #1: 0xffffff8010579062 kernel.debug`panic_trap(regs=0xffffff80101a5a50, pl=1, fault_result=0) at trap.c:841:2
    frame #2: 0xffffff80105787cd kernel.debug`kernel_trap(state=0xffffff80101a5a40, lo_spp=0xffffff80101a5a20) at trap.c:780:2
    frame #3: 0xffffff8010598b2f kernel.debug`trap_from_kernel + 38
    frame #4: 0xffffff8010599f55 kernel.debug`counter_inc(counter=0x0000000000000398) at counter.c:77:4
    frame #5: 0xffffff801042fdb2 kernel.debug`vm_fault_internal(map=0x0000000000000000, vaddr=288178176, caller_prot=1, change_wiring=0, wire_tag=0, interruptible=0, caller_pmap=0x0000000000000000, caller_pmap_addr=0, physpage_p=0x0000000000000000) at vm_fault.c:4008:2
    frame #6: 0xffffff801042f317 kernel.debug`_vm_fault$XNU_INTERNAL(map=0x0000000000000000, vaddr=288179504, fault_type=1, change_wiring=0, wire_tag=0, interruptible=0, caller_pmap=0x0000000000000000, caller_pmap_addr=0) at vm_fault.c:3725:9
    frame #7: 0xffffff80105786aa kernel.debug`kernel_trap(state=0xffffff80101a63c0, lo_spp=0xffffff80101a63a0) at trap.c:714:27
    frame #8: 0xffffff8010598b2f kernel.debug`trap_from_kernel + 38
    frame #9: 0xffffff80103732ab kernel.debug`thread_tid(thread=0x00000000112d3b50) at thread.c:2741:40
    frame #10: 0xffffff8010ed8a98 kernel.debug`__firehose_buffer_tracepoint_reserve + 1544
    frame #11: 0xffffff8010d567cd kernel.debug`_firehose_trace(stream=firehose_stream_persist, ftid=firehose_tracepoint_id_u @ 0xffffff80101a68b0, stamp=218524522623, pubdata=0xffffff80101a6ad8, publen=18, use_streaming=true) at log.c:523:7
    frame #12: 0xffffff8010d5a3af kernel.debug`_os_log_actual(type=OS_LOG_TYPE_DEFAULT, format="oslog_init completed, %u chunks, %u io pages\n", dso=0xffffff8010270000, addr=0xffffff8010ee22c5, logdata="\xc5\"\xc7", logdata_sz=18, flags=_firehose_tracepoint_flags_pc_style_main_exe, driverKit=false) at log.c:382:2
    frame #13: 0xffffff8010d5a074 kernel.debug`_os_log_to_log_internal(type=OS_LOG_TYPE_DEFAULT, fmt="oslog_init completed, %u chunks, %u io pages\n", args=0xffffff80101a6ed0, addr=0xffffff8010ee22c5, dso=0xffffff8010270000, driverKit=false) at log.c:439:3
    frame #14: 0xffffff8010d56150 kernel.debug`_os_log_with_args_internal(oslog=0xffffff80112c3218, type=OS_LOG_TYPE_DEFAULT, format="oslog_init completed, %u chunks, %u io pages\n", args=0xffffff80101a6ed0, addr=0xffffff8010ee22c5, dso=0x0000000000000000, driverKit=false, addcr=false) at log.c:249:3
    frame #15: 0xffffff8010d56362 kernel.debug`os_log_with_args(oslog=0xffffff80112c3218, type=OS_LOG_TYPE_DEFAULT, format="oslog_init completed, %u chunks, %u io pages\n", args=0xffffff80101a6ed0, addr=0xffffff8010ee22c5) at log.c:229:2
    frame #16: 0xffffff801032d526 kernel.debug`vprintf_internal(fmt="oslog_init completed, %u chunks, %u io pages\n", ap_in=0xffffff80101a6ed0, caller=0xffffff8010ee22c5) at printf.c:925:3
    frame #17: 0xffffff801032d30d kernel.debug`printf(fmt="oslog_init completed, %u chunks, %u io pages\n") at printf.c:938:8
    frame #18: 0xffffff8010ee22c5 kernel.debug`oslog_init at subr_log.c:926:2
    frame #19: 0xffffff8010edb9a3 kernel.debug`kernel_startup_initialize_upto(upto=STARTUP_SUB_OSLOG) at startup.c:368:3
    frame #20: 0xffffff801035194a kernel.debug`kernel_bootstrap at startup.c:407:2
    frame #21: 0xffffff8010581ede kernel.debug`machine_startup at model_dep.c:332:2
    frame #22: 0xffffff80105584b4 kernel.debug`i386_init at i386_init.c:1118:2
    frame #23: 0xffffff801056afa8 kernel.debug`x86_init_wrapper + 8
(lldb) 




```

从崩溃堆栈可以看到崩溃原因是     frame #9: 0xffffff80103732ab kernel.debug`thread_tid(thread=0x00000000112d3b50) at thread.c:2741:40
访问的 0x00000000112d3b50 地址不是xnu内核的高地址 (> 0xffffff8000000000),地址无效，导致触发缺页异常中断进入 trap_from_kernel 方法

**thread_tid 方法**

xnu/osfmk/kern/thread.c


```
uint64_t
thread_tid(
	thread_t        thread)
{
	return thread != THREAD_NULL? thread->thread_id: 0;
}

```

**firehose_buffer_tracepoint_reserve 方法**

PureDarwin/src/Libraries/libSystem/libdispatch/src/firehose/src/firehose_inline_internal.h

```



/*!
 * @function firehose_buffer_tracepoint_reserve
 *
 * @abstract
 * Reserves space in the firehose buffer for the tracepoint with specified
 * characteristics.
 *
 * @discussion
 * This returns a slot, with the length of the tracepoint already set, so
 * that in case of a crash, we maximize our chance to be able to skip the
 * tracepoint in case of a partial write.
 *
 * Once the tracepoint has been written, firehose_buffer_tracepoint_flush()
 * must be called.
 *
 * @param fb
 * The buffer to allocate from.
 *
 * @param stream
 * The buffer stream to use.
 *
 * @param pubsize
 * The size of the public data for this tracepoint, cannot be 0, doesn't
 * take the size of the tracepoint header into account.
 *
 * @param privsize
 * The size of the private data for this tracepoint, can be 0.
 *
 * @param privptr
 * The pointer to the private buffer, can be NULL
 *
 * @param reliable
 * Whether we should wait for logd or drop the tracepoint in the event that no
 * chunk is available.
 *
 * @result
 * The pointer to the tracepoint.
 */
OS_ALWAYS_INLINE
static inline firehose_tracepoint_t
firehose_buffer_tracepoint_reserve(firehose_buffer_t fb, uint64_t stamp,
		firehose_stream_t stream, uint16_t pubsize,
		uint16_t privsize, uint8_t **privptr, bool reliable)
{
	firehose_buffer_stream_t fbs = &fb->fb_header.fbh_stream[stream];
	firehose_stream_state_u old_state, new_state;
	firehose_chunk_t fc;
	bool waited = false;
	bool success;
	long result;
	firehose_chunk_ref_t ref;

	// cannot use os_atomic_rmw_loop, _page_try_reserve does a store
	old_state.fss_atomic_state =
			os_atomic_load(&fbs->fbs_state.fss_atomic_state, relaxed);
	for (;;) {
		new_state = old_state;

		ref = old_state.fss_current;
		if (likely(ref && ref != FIREHOSE_STREAM_STATE_PRISTINE)) {
			fc = firehose_buffer_ref_to_chunk(fb, ref);
			result = firehose_chunk_tracepoint_try_reserve(fc, stamp, stream,
					0, pubsize, privsize, privptr);
			if (likely(result > 0)) {
				uint64_t thread;
#if KERNEL
				thread = thread_tid(current_thread());
#else
				thread = _pthread_threadid_self_np_direct();
#endif
				return firehose_chunk_tracepoint_begin(fc,
						stamp, pubsize, thread, result);
			}
			if (likely(result < 0)) {
				firehose_buffer_ring_enqueue(fb, old_state.fss_current);
			}
			new_state.fss_current = 0;
		}

		if (!reliable && ((waited && old_state.fss_timestamped)
#ifndef KERNEL
				|| old_state.fss_waiting_for_logd
#endif
			)) {
			new_state.fss_loss =
					MIN(old_state.fss_loss + 1, FIREHOSE_LOSS_COUNT_MAX);

			success = os_atomic_cmpxchgv(&fbs->fbs_state.fss_atomic_state,
					old_state.fss_atomic_state, new_state.fss_atomic_state,
					&old_state.fss_atomic_state, relaxed);
			if (success) {
#ifndef KERNEL
				_dispatch_trace_firehose_reserver_gave_up(stream, ref, waited,
						old_state.fss_atomic_state, new_state.fss_atomic_state);
#endif
				return NULL;
			} else {
				continue;
			}
		}

		if (unlikely(old_state.fss_allocator)) {
#if KERNEL
			_dispatch_firehose_gate_wait(&fbs->fbs_state.fss_gate,
					DLOCK_LOCK_DATA_CONTENTION);
			waited = true;

			old_state.fss_atomic_state =
					os_atomic_load(&fbs->fbs_state.fss_atomic_state, relaxed);
#else
			if (likely(reliable)) {
				new_state.fss_allocator |= FIREHOSE_GATE_RELIABLE_WAITERS_BIT;
			} else {
				new_state.fss_allocator |= FIREHOSE_GATE_UNRELIABLE_WAITERS_BIT;
			}

			bool already_equal = (new_state.fss_atomic_state ==
					old_state.fss_atomic_state);
			success = already_equal || os_atomic_cmpxchgv(
					&fbs->fbs_state.fss_atomic_state, old_state.fss_atomic_state,
					new_state.fss_atomic_state, &old_state.fss_atomic_state,
					relaxed);
			if (success) {
				_dispatch_trace_firehose_reserver_wait(stream, ref, waited,
						old_state.fss_atomic_state, new_state.fss_atomic_state,
						reliable);
				_dispatch_firehose_gate_wait(&fbs->fbs_state.fss_gate,
						new_state.fss_allocator,
						DLOCK_LOCK_DATA_CONTENTION);
				waited = true;

				old_state.fss_atomic_state = os_atomic_load(
						&fbs->fbs_state.fss_atomic_state, relaxed);
			}
#endif
			continue;
		}

		// if the thread doing the allocation is of low priority we may starve
		// threads of higher priority, so disable pre-emption before becoming
		// the allocator (it is re-enabled in
		// firehose_buffer_stream_chunk_install())
		__firehose_critical_region_enter();
#if KERNEL
		new_state.fss_allocator = 1;
#else
		new_state.fss_allocator = _dispatch_lock_value_for_self();
#endif
		success = os_atomic_cmpxchgv(&fbs->fbs_state.fss_atomic_state,
				old_state.fss_atomic_state, new_state.fss_atomic_state,
				&old_state.fss_atomic_state, relaxed);
		if (likely(success)) {
			break;
		}
		__firehose_critical_region_leave();
	}

	struct firehose_tracepoint_query_s ask = {
		.stamp = stamp,
		.pubsize = pubsize,
		.privsize = privsize,
		.stream = stream,
		.for_io = (firehose_stream_uses_io_bank & (1UL << stream)) != 0,
#ifndef KERNEL
		.quarantined = fb->fb_header.fbh_quarantined,
#endif
		.reliable = reliable,
	};

#ifndef KERNEL
	_dispatch_trace_firehose_allocator(((uint64_t *)&ask)[0],
			((uint64_t *)&ask)[1], old_state.fss_atomic_state,
			new_state.fss_atomic_state);
#endif

	return firehose_buffer_tracepoint_reserve_slow(fb, &ask, privptr);
}


```

可以看到是通过 		thread = thread_tid(current_thread());   
current_thread 方法获取当前线程


# 2.current_thread 方法的设值和读取

## 读取

xnu/osfmk/i386/machine_routines.c

```

#undef current_thread
extern thread_t current_thread(void) __attribute__((const));
thread_t
current_thread(void)
{
	return current_thread_fast();
}

```


xnu/osfmk/i386/cpu_data.h

```

#define current_thread_fast()           get_active_thread()

```

xnu/osfmk/i386/cpu_data.h

```
static inline __attribute__((const)) thread_t
get_active_thread(void)
{
	return CPU_DATA()->cpu_active_thread;
}

```

## 设值

xnu/osfmk/i386/cpu_data.h

```

/* 每颗逻辑 CPU 一块；%gs 基址指此结构以单指令热路径访问；remote CPU 用 cpu_data_ptr[] */
typedef struct cpu_data {
	/* PAL 层在本 CPU 上的私有状态（页目录等）；宏 cpu_pd 为 cpu_pal_data 别名 */
	struct pal_cpu_data     cpu_pal_data;           /* PAL-specific data */
#define                         cpu_pd cpu_pal_data     /* convenience alias */
	/* 自指：得到本 cpu_data_t*，current_cpu_datap() 等用它 */
	struct cpu_data         *cpu_this;              /* pointer to myself */
	/* 本 CPU 的 PCPU 区在 VA 空间中的基址 */
	vm_offset_t             cpu_pcpu_base;
	/* 当前运行线程；current_thread() 读此字段（scheduler 在切换时更新） */
	thread_t                cpu_active_thread;
	/* 下一调度目标或切换中的线程指针（依上下文） */
	thread_t                cpu_nthread;
	/* 逻辑 CPU 编号 0..N-1，数组索引与策略用 */
	int                     cpu_number;             /* Logical CPU */
	/* 中断嵌套/状态保存指针（路径相关） */
	void                    *cpu_int_state;         /* interrupt state */
	/* 当前内核栈底（活动栈） */
	vm_offset_t             cpu_active_stack;       /* kernel stack base */
	/* 内核栈另一端边界，用于栈切换与边界检查 */
	vm_offset_t             cpu_kernel_stack;       /* kernel stack top */
	/* 中断/IST 专用栈顶边界 */
	vm_offset_t             cpu_int_stack_top;
	/* 其它核投递的 IPI/事件位（volatile：多写者） */
	volatile int            cpu_signals;            /* IPI events */
	/* 上一批信号快照，调试对比用 */
	volatile int            cpu_prior_signals;      /* Last set of events,
	                                                 * debugging
	                                                 */
	/* 待处理 AST：返回用户/内核边界时触发抢占、信号等 */
	ast_t                   cpu_pending_ast;
	/*
	 * Note if rearranging fields:
	 * We want cpu_preemption_level on a different
	 * cache line than cpu_active_thread
	 * for optimizing mtx_spin phase.
	 */
	/* 中断嵌套层级：与可在何处阻塞相关 */
	int                     cpu_interrupt_level;
	/* 抢占禁用计数：>0 时不可强占；宜与 cpu_active_thread 分 cache line */
	volatile int            cpu_preemption_level;
	/* CPU 是否在运行（相对 idle/下线） */
	volatile int            cpu_running;
#if !MONOTONIC
	/* 非单调路径：固定功能 PMU 是否开启 */
	boolean_t               cpu_fixed_pmcs_enabled;
#endif /* !MONOTONIC */
	/* 每 CPU 的 RTC 软件定时器（deadline 等） */
	rtclock_timer_t         rtclock_timer;
	/* 当前活动页表根 CR3；与 pmap 切换一致；64B 对齐减轻伪共享 */
	volatile addr64_t       cpu_active_cr3 __attribute((aligned(64)));
	union {
		/* TLB 失效请求：整型或拆成 local/global 两半（shootdown 协议） */
		volatile uint32_t cpu_tlb_invalid;
		struct {
			volatile uint16_t cpu_tlb_invalid_local;
			volatile uint16_t cpu_tlb_invalid_global;
		};
	};
	/* 与 IDT/陷阱入口相关的描述符/缓存（实现相关） */
	uint64_t                cpu_ip_desc[2];
	/* 当前 task 的地址空间映射类别标记 */
	volatile task_map_t     cpu_task_map;
	/* 当前用户任务的 CR3（若有独立用户页表） */
	volatile addr64_t       cpu_task_cr3;
	/* 纯内核映射 CR3（与任务页表分工） */
	addr64_t                cpu_kernel_cr3;
	/* 用户 CR3 变体/快路径副本（实现相关） */
	volatile addr64_t       cpu_ucr3;
	/* 影子任务页表根（调试/虚拟化等） */
	volatile addr64_t       cpu_shadowtask_cr3;
	/* 快速全零页是否已映射 */
	boolean_t               cpu_pagezero_mapped;
	/* 双映射 uber 窗口：GDT/TSS 等在内核 VA 的别名视图 */
	cpu_uber_t              cpu_uber;
/* Double-mapped per-CPU exception stack address */
	/* 双映射区内本 CPU 异常栈地址（如 IST 栈顶相关） */
	uintptr_t               cd_estack;
	/* FPU/SSE/AVX 等扩展状态惰性切换标志（XSAVE） */
	int                     cpu_xstate;
	/* 当前任务是否使用 LDT */
	int                     cpu_curtask_has_ldt;
	/* 当前线程是否需要段检查（兼容路径） */
	int                     cpu_curthread_do_segchk;
/* Address of shadowed, partially mirrored CPU data structures located
 * in the double mapped PML4
 */
	/* 双映射 PML4 中影子 cpu 结构地址：trap 快路径可读镜像 */
	void                    *cd_shadow;
	union {
		/* TLB 失效“次数”型计数（与 shootdown 统计配合） */
		volatile uint32_t cpu_tlb_invalid_count;
		struct {
			volatile uint16_t cpu_tlb_invalid_local_count;
			volatile uint16_t cpu_tlb_invalid_global_count;
		};
	};

	/* 记录其它 CPU 的 local TLB 代际，shootdown 时可避免无效刷新 */
	uint16_t                cpu_tlb_gen_counts_local[MAX_CPUS];
	/* 对应 global 代际（跨空间失效） */
	uint16_t                cpu_tlb_gen_counts_global[MAX_CPUS];

	/* Mach processor 对象：调度、电源、负载 */
	struct processor        *cpu_processor;
	/* 本 CPU LDT 在 GDT 中的描述符指针 */
	struct real_descriptor  *cpu_ldtp;
	/* 聚合 GDT/TSS 表指针 */
	struct cpu_desc_table   *cpu_desc_tablep;
	/* GDTR/IDTR 影子等（cpu_desc_load 使用） */
	cpu_desc_index_t        cpu_desc_index;
	/* LDT 槽位/索引 */
	int                     cpu_ldt;

#define HWINTCNT_SIZE 256
	/* 按中断向量计数硬件中断 */
	uint32_t                cpu_hwIntCnt[HWINTCNT_SIZE];    /* Interrupt counts */
	/* VM-exit 等情况下的中断相关计数 */
	uint64_t                cpu_hwIntpexits[HWINTCNT_SIZE];
	/* DR7 镜像：调试寄存器跨切换保存 */
	uint64_t                cpu_dr7; /* debug control register */
	/* 中断进入/退出时间戳，用于延迟 */
	uint64_t                cpu_int_event_time;     /* intr entry/exit time */
	/* PAL 纳秒时间换算结构 */
	pal_rtc_nanotime_t      *cpu_nanotime;          /* Nanotime info */
#if KPC
	/* double-buffered performance counter data */
	/* KPC 双缓冲采样缓冲 */
	uint64_t                *cpu_kpc_buf[2];
	/* PMC shadow and reload value buffers */
	/* PMC 影子与重装表 */
	uint64_t                *cpu_kpc_shadow;
	uint64_t                *cpu_kpc_reload;
#endif
#if MONOTONIC
	/* Mach 单调时钟每 CPU 状态 */
	struct mt_cpu cpu_monotonic;
#endif /* MONOTONIC */
	/* 是否启用 PCID（功能位 + pmap） */
	uint32_t                cpu_pmap_pcid_enabled;
	/* 当前活动 PCID 标签 */
	pcid_t                  cpu_active_pcid;
	/* 上次使用的 PCID，重用策略 */
	pcid_t                  cpu_last_pcid;
	/* 内核地址空间常用 PCID，与用户任务隔离 */
	pcid_t                  cpu_kernel_pcid;
	/* pmap 侧任务 PCID 一致性/引用计数表指针 */
	volatile pcid_ref_t     *cpu_pmap_pcid_coherentp;
	/* 内核 pmap 的对应指针 */
	volatile pcid_ref_t     *cpu_pmap_pcid_coherentp_kernel;
	/* PCID 分配与 refcount（见 pcid_cdata_t） */
	pcid_cdata_t            *cpu_pcid_data;
#ifdef  PCID_STATS
	/* 统计：PCID 需刷 TLB 次数 */
	uint64_t                cpu_pmap_pcid_flushes;
	/* 统计：保留 PCID 未刷次数 */
	uint64_t                cpu_pmap_pcid_preserves;
#endif
	/* 实际运行周期计数（频率缩放） */
	uint64_t                cpu_aperf;
	/* 固定频率周期计数 */
	uint64_t                cpu_mperf;
	/* C3/C6/C7 驻留采样 */
	uint64_t                cpu_c3res;
	uint64_t                cpu_c6res;
	uint64_t                cpu_c7res;
	/* 中断中时间累计 */
	uint64_t                cpu_itime_total;
	/* 运行时间累计 */
	uint64_t                cpu_rtime_total;
	uint64_t                cpu_ixtime;
	/* 从 idle 唤醒计数 */
	uint64_t                cpu_idle_exits;
	/*
	 * Note that the cacheline-copy mechanism uses the cpu_rtimes field in the shadow CPU
	 * structures to temporarily stash the code cacheline that includes the instruction
	 * pointer at the time of the fault (this field is otherwise unused in the shadow
	 * CPU structures).
	 */
	/* 运行时间直方图；影子 cpu 结构里可临时挪作 fault 现场 cacheline（见英文注） */
	uint64_t                cpu_rtimes[CPU_RTIME_BINS];
	/* 中断时间直方图 */
	uint64_t                cpu_itimes[CPU_ITIME_BINS];
#if !MONOTONIC
	/* 非 MONOTONIC：采样区间指令/用户计数等 */
	uint64_t                cpu_cur_insns;
	uint64_t                cpu_cur_ucc;
	uint64_t                cpu_cur_urc;
#endif /* !MONOTONIC */
	/* 通用 PMC 四路 */
	uint64_t                cpu_gpmcs[4];
	/* 观测到的最大中断延迟 */
	uint64_t                cpu_max_observed_int_latency;
	/* 最大延迟对应向量 */
	int                     cpu_max_observed_int_latency_vector;
	/* NMI 握手：是否已应答 */
	volatile boolean_t      cpu_NMI_acknowledged;
	/* 调试器停核时刻 */
	uint64_t                debugger_entry_time;
	/* 调试 IPI 时戳 */
	uint64_t                debugger_ipi_time;
	/* A separate nested interrupt stack flag, to account
	 * for non-nested interrupts arriving while on the interrupt stack
	 * Currently only occurs when AICPM enables interrupts on the
	 * interrupt stack during processor offlining.
	 */
	/* 已在中断栈上时又开中断的嵌套深度 */
	uint32_t                cpu_nested_istack;
	/* 上述异常嵌套发生次数 */
	uint32_t                cpu_nested_istack_events;
	/* 致命陷阱保存帧指针（不返回） */
	x86_saved_state64_t     *cpu_fatal_trap_state;
	/* 二次致命/嵌套 panic 状态 */
	x86_saved_state64_t     *cpu_post_fatal_trap_state;
#if CONFIG_VMX
	/* VT-x 每 CPU（VMCS 等） */
	vmx_cpu_t               cpu_vmx;                /* wonderful world of virtualization */
#endif
#if CONFIG_MCA
	/* 机器校验异常冻结状态 */
	struct mca_state        *cpu_mca_state;         /* State at MC fault */
#endif
	/* sysctl cpu_type 等用的 CPU 家族/型号桶 */
	int                     cpu_type;
	int                     cpu_subtype;
	/* SMT 线程类型标记 */
	int                     cpu_threadtype;
	/* 中断可屏蔽状态快捷位 */
	boolean_t               cpu_iflag;
	/* AP 引导完成握手标志 */
	boolean_t               cpu_boot_complete;
	/* 休眠镜像路径标记 */
	int                     cpu_hibernate;
#define MAX_PREEMPTION_RECORDS (8)
#if     DEVELOPMENT || DEBUG
	/* 抢占记录环当前索引 */
	int                     cpu_plri;
	/* 抢占不均衡调试：调用栈环 */
	plrecord_t              plrecords[MAX_PREEMPTION_RECORDS];
#endif
	/* 控制台/早期输出缓冲 */
	void                    *cpu_console_buf;
	/* LAPIC、电源等 x86 逻辑 CPU 封装 */
	struct x86_lcpu         lcpu;
	/* 物理 APIC ID / 拓扑中的物理索引 */
	int                     cpu_phys_number;        /* Physical CPU */
	/* PE 赋予的稳定 cpu_id */
	cpu_id_t                cpu_id;                 /* Platform Expert */
#if DEBUG
	/* 调试：CR3 路径追踪 */
	uint64_t                cpu_entry_cr3;
	uint64_t                cpu_exit_cr3;
	uint64_t                cpu_pcid_last_cr3;
#endif
	/* stop-the-world 类屏障：本 CPU 是否在集合点 */
	boolean_t               cpu_rendezvous_in_progress;
#if CST_DEMOTION_DEBUG
	/* Count of thread wakeups issued by this processor */
	/* 本 CPU 发出的线程唤醒总数（C-state 调试） */
	uint64_t                cpu_wakeups_issued_total;
#endif
#if DEBUG || DEVELOPMENT
	/* TSC 与其它核对齐的 delta */
	uint64_t                tsc_sync_delta;
#endif
} cpu_data_t;


```

xnu/osfmk/i386/mp_desc.c

```

/*
 * 在“当前 CPU”上加载 per-CPU 描述符状态：GS/KERNEL_GS 基址指向 cpu_data，
 * 刷新 GDT/IDT/LDT/TSS，使异常/中断入口与本 CPU 栈/IST 绑定。唤醒路径也需重跑。
 */
void
cpu_desc_load(cpu_data_t *cdp)
{
	/* 本 CPU 的 cpu_desc_index：存 GDTR/IDTR 影子与双映射窗口指针，供 lgdt/lidt 使用 */
	cpu_desc_index_t        *cdi = &cdp->cpu_desc_index;

	/* POST 诊断：进入 cpu_desc_load（启动/唤醒时串口或端口可观测进度） */
	postcode(CPU_DESC_LOAD_ENTRY);

	/* Stuff the kernel per-cpu data area address into the MSRs */
	/* POST：即将写入 IA32_GS_BASE */
	postcode(CPU_DESC_LOAD_GS_BASE);
	/* 当前线程/每 CPU 数据通过 %gs: 访问：基址设为该 CPU 的 cpu_data（cdp） */
	wrmsr64(MSR_IA32_GS_BASE, (uintptr_t) cdp);
	/* POST：即将写入 KERNEL_GS_BASE（与 swapgs 配对，用户/内核切换 GS 时另一扇窗口） */
	postcode(CPU_DESC_LOAD_KERNEL_GS_BASE);
	/* 此处同样指向 cdp，便于内核路径上 GS 一致；用户态返回经 swapgs 切换到 KERNEL_GS_BASE */
	wrmsr64(MSR_IA32_KERNEL_GS_BASE, (uintptr_t) cdp);

	/*
	 * Ensure the TSS segment's busy bit is clear. This is required
	 * for the case of reloading descriptors at wake to avoid
	 * their complete re-initialization.
	 */
	/* 重载 TSS（ltr）前须清 busy：Intel 规定对已 busy 的 TSS 再次 ltr 非法；唤醒时避免全量重 init */
	gdt_desc_p(KERNEL_TSS)->access &= ~ACC_TSS_BUSY;

	/* Load the GDT, LDT, IDT and TSS */
	/* GDTR limit：GDTSZ 个 8 字节描述符，总字节数减 1 写入 limit（x86 段表惯例） */
	cdi->cdi_gdtb.size = sizeof(struct real_descriptor) * GDTSZ - 1;
	/* 用户/映射视角下的 GDT 影子（cdi_gdtu）与底层（cdi_gdtb）limit 对齐 */
	cdi->cdi_gdtu.size = cdi->cdi_gdtb.size;
	/* IDT limit：4K 基数 + cpu 号（本工程布局），控制本 CPU 可见向量表范围 */
	cdi->cdi_idtb.size = 0x1000 + cdp->cpu_number;
	cdi->cdi_idtu.size = cdi->cdi_idtb.size;

	/* POST + lgdt：装载本 CPU GDT，此后段选择子按该表解析 */
	postcode(CPU_DESC_LOAD_GDT);
	lgdt((uintptr_t *) &cdi->cdi_gdtu);
	/* POST + lidt：装载本 CPU IDT，异常/中断经 idt 项跳入内核桩 */
	postcode(CPU_DESC_LOAD_IDT);
	lidt((uintptr_t *) &cdi->cdi_idtu);
	/* POST + lldt：装载内核 LDT（槽位 KERNEL_LDT，供部分兼容/隔离路径） */
	postcode(CPU_DESC_LOAD_LDT);
	lldt(KERNEL_LDT);
	/* POST + ltr：用 KERNEL_TSS 激活任务状态段（特权栈、IST、I/O 位图等由此生效） */
	postcode(CPU_DESC_LOAD_TSS);
	set_tr(KERNEL_TSS);

	/* POST：cpu_desc_load 完成 */
	postcode(CPU_DESC_LOAD_EXIT);
}

```

xnu/osfmk/i386/proc_reg.h

```

/*
 * 段基址 MSR（64 位模式）：数字是 Intel/AMD 体系结构规定的 MSR 索引，不是“任选 GS”。
 *   0xC0000100 → IA32_FS_BASE：wrmsr 到此索引改的是 FS 的隐藏基址。
 *   0xC0000101 → IA32_GS_BASE：改 GS 的隐藏基址。
 *   0xC0000102 → IA32_KERNEL_GS_BASE：仅与 GS 配对，供 SWAPGS 与用户态/内核态切换 GS 基址
 * （无对称的 “KERNEL_FS_BASE” / SWAPFS）。因此内核把 per-CPU（如 cpu_data）绑在 GS 上时
 * 使用 0x101/0x102；若用 FS 则应写 0xC0000100。
 */

#define MSR_IA32_GS_BASE                        0xC0000101



/*
 * wrmsr(msr, lo, hi) —— 展开为单条 x86 `wrmsr`，写 64 位 MSR（586+）。
 *
 * 第 1 行 `#define wrmsr(msr, lo, hi) \`
 *   - 宏名 wrmsr；参数 msr 为 MSR 索引（32 位），lo/hi 分别为待写入值的低/高 32 位。
 *   - 行末反斜杠将定义延续到下一行，预处理器把两行拼成一条替换文本。
 *
 * 第 2 行 `__asm__ volatile("wrmsr" : : "c"(msr), "a"(lo), "d"(hi))`
 *   - Intel 约定：执行 `wrmsr` 前须使 ECX=MSR 号，EAX=低 32 位，EDX=高 32 位（64 位模式下仍为这
 *     三寄存器的低 32 位部分承载操作数）。
 *   - GCC 内联汇编：无输出操作数；输入约束 "c"/"a"/"d" 分别把 C 表达式绑定到 ECX/EAX/EDX。
 *   - volatile：该写通常有副作用且不可被优化删掉，也限制编译器对此点的乱序假设（与 rdmsr 注释中
 *     “直接改参数以利优化”的接口风格不同，这里是固定寄存器约定）。
 */
#define wrmsr(msr, lo, hi) \
	__asm__ volatile("wrmsr" : : "c" (msr), "a" (lo), "d" (hi))


    static inline void
wrmsr64(uint32_t msr, uint64_t val)
{
	wrmsr(msr, (val & 0xFFFFFFFFUL), ((val >> 32) & 0xFFFFFFFFUL));
}

```

通过 wrmsr64(MSR_IA32_GS_BASE, (uintptr_t) cdp); 将 当前线程/每 CPU 数据 设置到 MSR_IA32_GS_BASE ，即 gs:0 的地址



# 3.从汇编代码发现问题

@src/Kernel/libfirehose_kernel/src/firehose_inline_internal.h:281-282 对应的汇编代码是：

````
com.apple.kernel:__text:FFFFFF8000ED8A41 mov [rbp+var_158], 0
com.apple.kernel:__text:FFFFFF8000ED8A4C call _current_thread
com.apple.kernel:__text:FFFFFF8000ED8A51 mov edi, eax ; thread
com.apple.kernel:__text:FFFFFF8000ED8A53 call _thread_tid
com.apple.kernel:__text:FFFFFF8000ED8A58 mov [rbp+var_158], rax
````

@src/Kernel/xnu/libkern/os/log.c:546-547 对应汇编代码是：

```
com.apple.kernel:__text:FFFFFF8000D56C5B mov [rbp+var_164], ax
com.apple.kernel:__text:FFFFFF8000D56C62 call _current_thread
com.apple.kernel:__text:FFFFFF8000D56C67 mov rdi, rax ; thread
com.apple.kernel:__text:FFFFFF8000D56C6A call _thread_tid
```

前者是 mov edi, eax 使用 32位的eax 存储 _current_thread 返回值，而后者是 mov rdi, rax ; thread 使用 rax 存储返回值，后者使用 rax 存储 _current_thread 返回值是正确的，前者使用 32为 eax 存储 _current_thread 返回值会使数据丢失从而导致崩溃





# 4.为什么会这样？

current_thread 方法 thread_t 在不同代码调用存在 内核态 64位 和 用户态 32位 2种情况，而32位会在内核运行时导致数据丢失而崩溃

```
thread_t
current_thread(void)
{
	return current_thread_fast();
}

```



@src/Kernel/xnu/libkern/os/log.c   中 64位 thread_t 的引入链条

```
log.c
  -> #include <kern/cpu_data.h>
      -> #include <mach/mach_types.h>
          -> typedef struct thread *thread_t   // 64位
      -> machine/cpu_data.h
          -> current_thread() macro returns thread_t

  -> #include <kern/thread.h>
      -> #include <mach/mach_types.h>
      -> extern uint64_t thread_tid(thread_t thread)
```



@src/Kernel/xnu/libkern/os/log.c 中 32位 thread_t 的引入链条

```

firehose_buffer.c
  -> <kern/thread.h>
      -> <mach/mach_types.h>
          -> System.framework PrivateHeaders
              -> typedef mach_port_t thread_t     // 32位

```



# 5.如何修复？

1.`libfirehose_kernel` 是内核代码，但它的 CMake include 搜索顺序曾经让用户态 `System.framework` Mach 头先被命中，导致 `<mach/mach_types.h>` 走成了用户态 ABI：`thread_t = mach_port_t`。改这两行就是把内核 Mach 头放到搜索顺序前面。



src/Kernel/libfirehose_kernel/CMakeLists.txt

```
target_link_libraries(libfirehose_kernel PRIVATE AvailabilityHeaders xnu_private_headers)
target_link_libraries(libfirehose_kernel PRIVATE xnu_kernel_headers xnu_kernel_private_headers)
```



2.为什么会命中错误头？

`xnu/CMakeLists.txt` 定义了几组 header interface target：

```
add_library(xnu_private_headers INTERFACE)
target_include_directories(xnu_private_headers INTERFACE
    ${CMAKE_CURRENT_BINARY_DIR}/xnu_header_install/usr/local/include
    ${CMAKE_CURRENT_BINARY_DIR}/xnu_header_install/System/Library/Frameworks/System.framework/Versions/B/PrivateHeaders
)

add_library(xnu_kernel_headers INTERFACE)
target_include_directories(xnu_kernel_headers INTERFACE
    ${CMAKE_CURRENT_BINARY_DIR}/xnu_header_install/System/Library/Frameworks/Kernel.framework/Versions/A/Headers
)

add_library(xnu_kernel_private_headers INTERFACE)
target_include_directories(xnu_kernel_private_headers INTERFACE
    ${CMAKE_CURRENT_BINARY_DIR}/xnu_header_install/System/Library/Frameworks/Kernel.framework/Versions/A/PrivateHeaders
)
```

也就是说：

- `xnu_private_headers` 包含的是 System.framework PrivateHeaders，偏用户态/System 接口。
- `xnu_kernel_headers` / `xnu_kernel_private_headers` 包含的是 Kernel.framework Headers/PrivateHeaders，才是内核编译应优先使用的头。

之前 `libfirehose_kernel` 的顺序是先 `xnu_private_headers`，再 `xnu_kernel_headers`。C/C++ 预处理器解析：

```
#include <mach/mach_types.h>
```

时按 `-I` 顺序从前往后找，先找到 `System.framework/.../PrivateHeaders/mach/mach_types.h`，于是整份 TU 就用了用户态 Mach 类型。并且 include guard 已经定义后，后面即使再出现 Kernel.framework 的同名头，也不会重新定义回来。



3.为什么改这两行能解决崩溃?

修改后顺序变成：

src/Kernel/libfirehose_kernel/CMakeLists.txt

```
target_link_libraries(libfirehose_kernel PRIVATE AvailabilityHeaders xnu_kernel_headers xnu_kernel_private_headers)
target_link_libraries(libfirehose_kernel PRIVATE xnu_private_headers)
```

Make 会把这些 interface target 的 include directories 传递到 `libfirehose_kernel` 的编译命令里。顺序调整后，编译 `firehose_buffer.c` 时搜索路径大致变成：

1. `Kernel.framework/Versions/A/Headers`
2. `Kernel.framework/Versions/A/PrivateHeaders`
3. `System.framework/Versions/B/PrivateHeaders`

于是 `<mach/mach_types.h>` 首先命中 Kernel.framework 里的内核头，`thread_t` 得到：

```
typedef struct thread *thread_t;
```

而不是：

```
typedef mach_port_t thread_t;
```

这会修正所有依赖 `thread_t`、`task_t`、`host_t` 等 Mach kernel object 类型的声明、内联函数、结构体布局和调用约定。之前如果按 32 位 `mach_port_t` 编译，内核里真实传入/存储的是 64 位指针，轻则编译出的访问偏移/类型检查错误，重则运行时把指针截断或按端口名解释，最终崩溃。

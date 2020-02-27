---
layout: post
title: 内存虚拟化之linux内存管理分析
categories: linux
tags: [os, linux, kernel, mm virtualize, mm manage] 
---

# 内存虚拟化之linux内存管理分析

## gdt

````asm
.fill GDT_ENTRY_BOOT_CS,8,0 //第0项处理器禁用
.quad 0x00cf9a000000ffff	/* base: 0x00000000；limit: 4G；type: 代码段，执行/可读；DPL:0*/
.quad 0x00cf92000000ffff	/* base: 0x00000000；limit: 4G；type: 数据段，可读/写；DPL: 0 */
````

````c
static void setup_gdt(void)
{
	/* There are machines which are known to not boot with the GDT
	   being 8-byte unaligned.  Intel recommends 16 byte alignment. */
	static const u64 boot_gdt[] __attribute__((aligned(16))) = {
		/* CS: code, read/execute, 4 GB, base 0 */
		[GDT_ENTRY_BOOT_CS] = GDT_ENTRY(0xc09b, 0, 0xfffff),
		/* DS: data, read/write, 4 GB, base 0 */
		[GDT_ENTRY_BOOT_DS] = GDT_ENTRY(0xc093, 0, 0xfffff),
		/* TSS: 32-bit tss, 104 bytes, base 4096 */
		/* We only have a TSS here to keep Intel VT happy;
		   we don't actually use it for anything. */
		[GDT_ENTRY_BOOT_TSS] = GDT_ENTRY(0x0089, 4096, 103),
	};
	/* Xen HVM incorrectly stores a pointer to the gdt_ptr, instead
	   of the gdt_ptr contents.  Thus, make it static so it will
	   stay in memory, at least long enough that we switch to the
	   proper kernel GDT. */
	static struct gdt_ptr gdt;

	gdt.len = sizeof(boot_gdt)-1;
	gdt.ptr = (u32)&boot_gdt + (ds() << 4);

	asm volatile("lgdtl %0" : : "m" (gdt));
}
````

**平坦内存映射**

## 分页

````c
config PAGE_OFFSET
        hex
        default 0xB0000000 if VMSPLIT_3G_OPT
        default 0x80000000 if VMSPLIT_2G
        default 0x78000000 if VMSPLIT_2G_OPT
        default 0x40000000 if VMSPLIT_1G
        default 0xC0000000
        depends on X86_32
````

内核空间对应的线性地址空间为：0xC0000000~0xFFFFFFFF
设置页目录表的第768到第1023项，平坦映射物理地址0~1G


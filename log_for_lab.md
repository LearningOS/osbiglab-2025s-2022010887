# week 6 进展报告 0327

黄雨婕 2022010887

---

这一周撰写了starry tutorial book 里运行时模块的文档，然后主要对着大一暑假小学期rust课堂的课件学习语法特性。

自己尝试写了一些简单的代码练手后，克隆了rcore仓库，对照着 Tutorial book 了解rcore框架的结构和其中一些syscall的实现。

将rcore框架和大实验 syscall_imp 下面的代码进行简单对比，发现虽然目前大实验框架已经能够通过basic测例，但实际上已经实现的syscall非常少，很多比较基本的syscall依然需要自己实现。

下周计划：超过64分

# week 7 进展报告 0403

黄雨婕 2022010887

---

- 撰写了群里分派的文档任务， 包括Unixbench 测例的题目描述、编译方法、样例输出、评分依据。[oskernel-testsuits-cooperation/doc/Unixbench.md](https://github.com/oscomp/oskernel-testsuits-cooperation/blob/master/doc/Unixbench.md)
- 按照上次陈老师的建议看了 [https://github.com/LearningOS](https://github.com/LearningOS) 里的 [2025春夏季开源操作系统训练营](https://github.com/LearningOS/rust-based-os-comp2025/blob/main/2025-spring-summary.md) ，确实非常有用
- 写了一部分 syscall

目前进度：汇总后通过了过半的 libctest ,分数300+

下周计划：进一步实现更多的系统调用，争取通过更多的libctest测例。

# week 9 进展报告 0417

黄雨婕 2022010887

---

本周进展：通过了全部busybox测例，进一步优化代码结构，然后开始规划进程线程相关的实现，基于windows、linux、星绽（Asterinas）进行了相关的调研。

Asterinas 的进程和线程管理实现与 Linux 接近，主要区别如下：

#### **1. 进程管理**

**进程创建** ：

* 使用 `clone` 指定资源共享（如内存、文件表等）。
* 提供了 `execve` 系统调用，用于加载 ELF 文件并启动用户进程。
* 通过 `ProcessBuilder` 构建进程，支持设置资源限制、信号处理、环境变量等。

**进程状态** ：

* 使用 `ProcessStatus` 表示进程状态（如运行中、僵尸状态）。
* 提供 `wait` 系列函数，支持父进程等待子进程退出。

**进程组与会话** ：

* 支持进程组（`ProcessGroup`）的管理。
* 提供了 `to_new_group` 方法，用于将进程移动到新的组或会话。

**资源限制** ：

* 使用 `ResourceLimits` 管理进程的资源限制（如文件描述符数量、内存大小等）。

**信号处理** ：

* 实现了信号发送和处理机制，支持向单个进程、进程组发送信号。
* 提供了 `send_parent_death_signal`，处理父子进程间的信号交互。

**对比** ：

* 和 linux一样，使用 `clone` 和 `execve` 系统调用创建进程。支持进程组和会话管理，符合 POSIX 标准。提供了 `wait` 系列函数，支持父进程等待子进程退出。
* 相比 Linux ， Asterinas 的实现更简化，例如 `wait` 仅支持部分标志（如 `WNOHANG`）。
* 信号处理逻辑可能不如 Linux 完整，例如实时信号队列的支持可能有限。
* 资源限制的实现可能不如 Linux 细致，例如 `RLIMIT_AS` 等限制可能未完全支持。
* 提供了类似 Windows 的父子进程关系管理。不过 Windows 使用 `CreateProcess` 创建进程，支持更复杂的进程属性设置。

#### **2. 线程管理**

* **线程创建** ：
* 使用 `create_new_user_task` 创建用户线程，支持设置线程本地存储（TLS）和用户上下文。
* 提供了 `ThreadOptions`，用于配置线程的调度策略和 CPU 亲和性。
* **线程状态** ：
* 使用 `ThreadStatus` 表示线程状态（如运行中、已退出）。
* 提供了线程暂停和恢复的机制。
* **工作队列** ：
* 实现了 `WorkerPool` 和 `WorkQueue`，用于管理内核线程的异步任务。
* 提供了简单的调度器 `SimpleScheduler`，动态调整工作线程数量。

**对比** ：

* 三者都提供了线程本地存储（TLS）和调度策略。
* 和linux一样，使用 `clone` 创建线程，支持共享资源（如内存、文件表）。
* Asterinas 的线程调度策略较为简单，工作队列的实现更轻量，调度器功能有限，和 Linux 的 CFS（完全公平调度器）等复杂调度算法不同。Windows 的线程调度比 Asterinas 更复杂，支持优先级提升等机制。

#### **3. 用户空间交互**

* **用户空间内存访问** ：
* 提供了 `CurrentUserSpace`，支持从用户空间读取或写入数据。
* 实现了 `read_cstring` 等方法，简化用户空间数据操作。
* **VDSO 支持** ：
* 实现了虚拟动态共享对象（VDSO），允许用户空间直接调用某些内核功能（如获取时间），减少上下文切换。

**对比** ：

* 基本和 linux 完全一致
* Windows 使用 `NTDLL` 提供用户态与内核态的接口，而非 VDSO。同时Windows 的内存管理更复杂，支持内存映射文件等功能。

Asterinas 的进程和线程管理实现借鉴了 Linux 的设计，并进行了简化。与 Linux 和 Windows 相比，其实现更轻量化，功能上有所取舍。

**下周计划**：（周六考试结束之后）参考不同的OS，完善我们的进程线程相关设计并开始实现。

# week 10 进展报告 0424

黄雨婕  2022010887

---

代码重构：继续将部分 syscall 的实现分成接口和实现两部分。

debug：发现并修复了关于 poll 对于 timeout 判定的一个逻辑问题。

```rust
pub fn sys_poll(fds: &mut [PollFd], timeout: i32) -> i32 {
    debug!("sys_poll <= fds: {:?}, timeout: {}", fds, timeout);
    syscall_body!(sys_poll, {
        let timeout = if timeout < 0 {
            0
        } else {
            timeout as u64 * NANOS_PER_MICROS
        };
        sys_poll_impl(fds, timeout, timeout < 0)
    })
}
```

线程进程相关：尝试参照了另一组的实现，看完代码之后觉得结构差异太大，强行合并还不如自己实现，至少不用对着别人写的代码debug。

下周计划：五一放假，下次开会应该是两周之后，希望到时候能至少实现 进程线程 相关的部分。

# week 12 进展报告 0508

黄雨婕  2022010887

**本周进展：**

- 跑了LTP、iozone，发现缺少系统调用，于是实现了sys_sync 、sys_fsync、sys_truncate、sys_ftruncate、sys_prlimit64、sys_setrlimit 、sys_getrlimit等。( 之前没写 get_process_data_by_id 方法，没办法修改 resource_limit 字段)

**待解决的问题：**

- LTP希望读写meminfo 文件，目前还没解决。

```
vi /proc/meminfo
```

```
MemTotal:        7993708 kB
MemFree:         6955792 kB
MemAvailable:    6884312 kB
Buffers:           11588 kB
Cached:            64856 kB
SwapCached:        76020 kB
Active:           117492 kB
Inactive:         432340 kB
Active(anon):      74064 kB
Inactive(anon):   402012 kB
Active(file):      43428 kB
Inactive(file):    30328 kB
...
```

- 编写 sys_renameat 的时候发现底层库报错 `rc=2`。

**下周计划：LTP、iozone**

# week 13 进展报告 0515

黄雨婕  2022010887

**本周进展：**

- 对于 processData 里的 resourcelimit 相关数据结构、接口、syscall 进行了重写，对不同limit的初值进行了合理的初始化。
- 修改了 dup 的逻辑（需要先判定 当前文件描述符数量 是否小于 当前的最大限制，是再复制文件描述符）,并将其从 .arceos 重构到另一个模块下。
- 对上周所述 renameat 底层库报错的问题进行了调试，步入到底层库的C代码后发现得到的文件路径开头不是/，报错原因是文件路径错误导致返回 no entry，该问题已修复。

**待解决的问题：内存泄漏等**

**下周计划：LTP 、iozone**



# week 14 进展报告 0522

黄雨婕  2022010887

**本周进展：**

通过了所有 iozone 测例，获得满分。

正在编写最终的总结报告文档。

**下周计划：**

完成最终文档。

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

下周计划：进一步实现更多的系统调用，争取通过更多的libctest测例

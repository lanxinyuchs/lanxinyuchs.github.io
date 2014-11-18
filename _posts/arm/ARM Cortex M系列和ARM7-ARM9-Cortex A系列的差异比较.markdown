### ARM Cortex M系列和ARM7/ARM9/Cortex A系列的差异比较

Tags: ARM

Cortex M系列和Cortex A系列都是基于ARM V7指令集架构的，但是前者没有什么历史包袱，所以有很多全新的设计，代表作是STM32，而后者更多的是保持了对ARM7/ARM9系列的向前兼容性。两者的差异体现在以下几个方面：

1. Cortex-M系列只有Previliged和User两种运行模式，而ARM7/ARM9/Cortex-A系列有FIQ, IRQ, Superviser, System, Abort, Undefined, User七种运行模式，当然前六种模式都可以视为Previliged模式（其中除system外的五种为异常模式）。FIQ和IRQ在M系列中被优先级可进一步细分的NVIC替代。Superviser中的“软件中断”指令SWI在M系列中被SVC指令替代。M系列中，不能直接从用户线程模式（使用进程堆栈指针PSP）进入特权线程模式（使用主堆栈指针MSP），而是需要先通过SVC触发一个（软）中断进入handler模式，再操作CONTROL控制寄存器进入特权线程模式，同样A系列中，因为User模式不能操作CPSR,所以 不能直接从User模式进入System模式，而是需要先通过SWI进入Superviser模式，Abort可以对应M系列的MemManage Fault，Undefined可以对应M系列的usage fault异常。另外M系列还有bus fault, hard fault等异常。正因为A系列有7种模式，每个模式有自己的专有寄存器（SP, LR, SPSR），所以总共有16 + 2*5 + 5 = 31个通用寄存器和6个状态寄存器，而M系列只有R0~R15 + MSP/PSP共17个通用寄存器。

2. ARM7/ARM9有16位Thumb和32位ARM两种指令状态，因此存在两种状态之间的切换，而Cortex-M系列使用的是16/32位Thumb-2紧凑型混合指令集，不存在指令切换的开销，Cortex-A系列则同时支持传统的ARM,Thumb指令集和新增的Thumb-2指令集

参考[Cortex M3特性解析](https://www.zybuluo.com/lanxinyuchs/note/39621)

补充：x86架构有ring0~ring3共4种模式，ring0为特权级，为操作系统所使用，ring3为普通用户级，为应用程序所使用。通过0x80指令触发异常可从应用层trap到内核层





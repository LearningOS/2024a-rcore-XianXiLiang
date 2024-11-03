## lab1

#### 0.

我实现的功能：记录每个程序的系统调用次数以及系统调用时刻的时间，调用状态固定为Running

修改文件有**syscall/mod.rs，task/mod.rs，syscall/process.rs，task/task.rs**

在task/task.rs中TaskManager下TaskControlBlock的定义中**新增了system_call_times和start_time**，用于记录维护内核控制块信息；

在task/mod.rs中的lazy_static! **补充声明tasks数组时的参数初始化**；

在syscall/mod.rs的syscall函数开始，更新系统调用的次数，**注意缩小inner借用的作用域**，否则在syscall释放以前会有两个借用；

在syscall/process.rs中读取当前运行的task，更新传入的TaskInfo信息；

虽然系统调用接口采用桶计数，但是内核采用相同的方法进行维护会相当**占用内存**，每个任务都要维护一个大数组；

可以用HashMap进行计数；

#### 1.

bad_address访问内核区域的地址，bad_instruction在U态调用S态指令sret，bad_registers在U态访问S态寄存器

```
[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x804003a4, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
```

RustSBI version 0.3.0-alpha.2；RISC-V SBI v1.0.0

#### 2.1.

a0代表 trap_handler 函数返回的值

情景一：U态触发中断异常

情景二：S态触发中断异常

#### 2.2.

特殊处理了sstatus、sepc、sscratch，sstatus的值决定了返回用户态时的特权级别和中断状态，sepc的值决定了返回用户态的指令地址，sscratch的值决定了返回用户态的栈指针位置

#### 2.3.

x2保存指向内核栈的栈指针；x4是线程指针，除非手动出于一些特殊用途使用它，否则一般也不会被用到

#### 2.4.

该指令之后，sp的值是指向用户态的栈指针；sscratch的值是指向内核态的栈指针

#### 2.5.

sret指令完成状态切换；因为sret会从sstatus，sepc恢复用户态的信息，这条指令就是干这个的

#### 2.6.

该指令之后，sp的值是指向内核态的栈指针；sscratch的值是指向用户态的栈指针

#### 2.7.

从L13指令切换栈指针开始，U态开始进入S态

## 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > *copilot*

2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > *无*

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。
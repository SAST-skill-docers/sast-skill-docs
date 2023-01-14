# 硬件设计

!!! note
	本文档以个人为视角撰写，如有偏颇之处请联系作者更正。
	

计算机系的《数字逻辑电路》课程的讲授，是与其他院系完全不同的：讲最基本的组合逻辑和时序逻辑电路的分析与设计。选课的学生，背网原之余，每每花半个晚上，看十几个数电 Slides，便得到一个 B+ 或以上的成绩 —— 这是常常发生的事，现在往往要拿到一个 P，—— ，倘若算卡诺图化简算的快些，便可以拿到一个 A-，或者甚至是 A，然后沾沾自喜了。但这些学生，多是不擅长考试，大抵没有如此幸运……

站在《计算机组成与设计》课程（官方名《**计**算机组成**原**理》）所需要的角度，我们来回看在大二春的半个学期的数电课程中，我们应该学会什么东西。大抵来说，可以概括为以下内容：

- 了解组合逻辑电路的基本组成部件与时序逻辑电路的基本组成部件（只需知道其功能，不需要探究其底层实现）
- 了解波形图的看法，可以根据波形图设计组合逻辑和时序逻辑电路
- 了解怎么使用硬件编程语言将实现组合逻辑电路、同步时序逻辑电路
- 针对数电考试，我们只需要再学会使用卡诺图进行最简与或表达式的化简，类似于小学时的“看谁算的又快又对”

除此之外，一旦课程讲到什么三极管的特性，讲到什么 CMOS 管的特点，如果你并不对这些感兴趣，那你大可以去思忖自己的午饭，这可能比搞懂三级管的四种工作模式对提升幸福感有帮助的多。我们接下来逐点对上述进行介绍。

- 组合逻辑电路与时序逻辑电路部件：上课会讲这些部件的设计方法，了解基本 idea 即可，没必要 get your hands dirty 然后去实际把电路图什么的抄下来，考试只会考怎么把这些部件组合起来实现功能
- 波形图：也是只有理解难度的项目，但是是时序逻辑的基本
- 硬件编程语言：推荐在《数字逻辑实验》课程中即使用 Verilog 或者 SystemVerilog  进行编写，<s>别再抄那破 VHDL 代码了</s>，这里推荐 [这篇文档](https://lab.cs.tsinghua.edu.cn/digital-design/doc/) 的“通过例子学硬件描述语言”部分



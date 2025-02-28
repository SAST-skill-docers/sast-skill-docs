# 如何成为一名神经网络训练师

Credit: 原作者 lifeihan

## 一、基本门槛

(1) **具有一定的数学基础：**一定要对**微积分、线性代数**具有较好的掌握，作为基础中的基础。大一上的同学可以提前学习微积分下册的多元函数微分、偏导相关内容，相较上册无难度提升，在其基础上简要阅读即可了解并掌握。此外，有余力者可以学习**概率论与数理统计**的相关内容。此部分不建议自行系统学习相关课本等内容，而是在 AI 学习遇到时再进行查询学习。

(2) **掌握 Python 语言：**此处可参照酒井暑培 3.0 系列课程～

- [酒井科协暑培 3.0 基础 + 人工智能](https://zhaochen20.notion.site/2ff7e897ee9847a4900206a52d895aa9)
- 建议不要死啃知识点，掌握最基础的知识后尝试跟着教程写一个小游戏或者诸如魔方求解一类的工具性程序，**遇到不懂的进行查询学习，而非在全面掌握知识点后再开始写程序**。然后再自行写一些小型程序进行巩固，这样才是最快掌握 Python 语言的方法。

(3) **对一些常用工具进行简要的学习掌握：**如 Vim、Git、Linux……此部分可参照大量网络教程或酒井暑培资料，此处不做过多赘述。

## 二、入门 PyTorch 的基础知识学习

(1) **机器学习相关基础知识：** 机器学习的部分知识只作为理解深度学习知识的基础，不必在一些相较之下更深入的知识上进行纠结，~~因为我学完了回头不用也忘了~~。同时如果以 PyTorch 入门，训练神经网络为目的，不必进行相应的代码训练，而掌握知识点即可。此处对应该掌握的机器学习基础知识做一提纲，可对照着查缺补漏：

- 数据预处理：数据清洗、数据变换
    - [总结：数据清洗的一些总结](https://blog.csdn.net/MrLevo520/article/details/77573757)
    - [机器学习数据预处理代码汇总](https://blog.csdn.net/weixin_35757704/article/details/89280715?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164553726516780264084676%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164553726516780264084676&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2)
    - [读完本文，让你快速掌握数据预处理｜机器学习](https://zhuanlan.zhihu.com/p/135237642)
- 特征工程：
    - [特征工程](https://blog.csdn.net/lc013/article/details/100033144?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.pc_relevant_paycolumn_v3&utm_relevant_index=6)
- 监督学习：
    - 分类算法：K-近邻算法，决策树，朴素贝叶斯，Logistic 回归，SVM，Softmax 回归
    - 回归算法：线性回归，局部加权线性回归
- 无监督学习：
    - K-means，层次聚类，DBSCAN
- 模型评估方法：准确率，召回率，P-R 曲线，ROC，AUC，MSE
    - [机器学习模型评估的方法总结（回归、分类模型的评估）](https://blog.csdn.net/sinat_16388393/article/details/91427631?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164639688216780264063596%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164639688216780264063596&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2)

**算法部分学习资料推荐：**

1. 视频推荐：吴恩达的机器学习课程（B 站搜索即可），可以高倍速过一遍，对相关内容有一定基础的理解，但是其不涉及几乎任何数学推导，而是傻瓜式的告诉你如何使用，这不是我们学习的目的，更建议在观看完前几节课有了一定了解后转 2。
2. 对着知识提纲名字逐个在 CSDN/知乎/StackOverflow 搜索进行学习，而非啃书或者看视频，效率更高。
3. 如果想对知识有更深入的理解，建议阅读李航的《统计学习方法》一书，相关知识点啃一遍后收获颇丰。（周志华的《机器学习》也称西瓜书也可考虑，笔者有但没阅读过故不做推荐，此书风评也不错～）

(2) **深度学习相关基础知识：**此部分知识建议首先以视频资料为起始进行了解学习，然后根据提纲进行拓展补充，查缺补漏～

- 视频资料推荐：李宏毅的机器学习课程（虽然叫机器学习，实则涵盖着大量的深度学习内容），老师风趣幽默，同时针对知识有着简明易懂的讲解，也不乏数学推导的自愿学习；针对一些延伸内容也有助教做了相关的学习讲解，非常推荐～
    - 课程搬运视频链接：[李宏毅 2021/2022 春机器学习课程](https://www.bilibili.com/video/BV1Wv411h7kN?spm_id_from=333.999.0.0)
    - 课程主页（作业链接，讲义下载使用）：[Machine Learning 2021 Spring](https://speech.ee.ntu.edu.tw/~hylee/ml/2021-spring.php)<br/> **注：**课程前 27 讲针对基础，务必看完；而后不一定要将后续所有课程学完，建议先行学习比较常见且基础的 CNN，Self-Attention，Transformer，RNN 部分，而余下的内容有余力可以全部学习，否则在以后需要时再观看学习更佳。

- 知识提纲：
    - **数据预处理 & 数据增强：** 
        - 图像剪裁：中心剪裁，随机剪裁，随机长宽比剪裁
        - 翻转和旋转：概率水平翻转，概率垂直旋转，随机旋转
        - 图像变换：Resize，标准化，填充，灰度化，线性变化，仿射变换，Tensor
    - **层次结构：**
        - 卷积层：标准卷积，空洞卷积，分组卷积，可变形卷积，可分离卷积
        - 激励层：sigmoid，tanh，ReLU，Leaky ReLU，ELU，SELU
        - 池化层：平均池化，最大池化，随机池化，全局平均池化
        - 上采样层：双线性插值，反卷积，反池化
    - **损失函数：**zero one loss，cross entropy loss，MSE loss，focal loss，logistic loss，center loss，wing loss，dice loss，hinge loss，arcface loss
    - **优化算法：**BCD，SGD，MBGD，Momentum，RMSProp，AdaGrad，Adam
    - **权重初始化：**零初始化，随机初始化，Xavier 初始化，He 初始化，预训练模型
    - **学习率调整：**LamdalLR，MultiplicativeLR，StepLR，MultiStepLR，ExponentialLR，CosineAnnealingLR，CyclicLR，OneCycleLR，Warmup

## 三、入门 PyTorch 工具

入门工具的过程也是锻炼码力的过程，此处推荐以下路线:

- 观看官方文档的简单示例并跟随写代码作为最基础的入门 ：
    - [PyTorch 中文官方教程 1.7](https://pytorch.apachecn.org/#/docs/1.7/README)
- 寻找 Kaggle 网站的几个最基础示例比赛（如 MNIST 库的识别），在其 Code 中寻找基础的解决方案进行摹写学习：
    - [Kaggle](https://www.kaggle.com/)
- 完成李宏毅课程作业，跟随公布的最佳方案进行一遍摹写，遇到不懂的函数/处理进行搜索引擎搜索补充学习，更好的做法是学会查询官方的函数库词典：[Torch](https://pytorch.org/docs/stable/torch.html)
- 尝试在 Kaggle 上自行寻找有开源 Code 的比赛自行撰写并提交看看结果，学习他人经验。

## 四、寻找适合自己的领域，进行深入学习与科研

深度学习不是雨露均沾的学科，建议找到自己感兴趣的方向，如 NLP、CV、计算优化等等大方向，及其下的细分领域进行学习，和自身的实验室/SRT 科研进行结合找到自己感兴趣的点，进行学习，基本的学习思路如下：

- 发现兴趣，找到方向，与学长老师交流此方向的前景，~~卷不卷~~，学习与入门方法；
- 对此领域的开山之作，一直到最近顶会的高影响力论文进行阅读学习，养成做笔记的好习惯，主要记录模型思路，优缺点；
- 了解，学习并逐渐掌握，熟练运用此方向的常用开源模型框架，能够更换自己的数据集，作出简要改造等，跑起来模型；
- 对模型参数，模型结构有自己深刻的认识，能够根据运算结果做出合理的调整并获取进一步优化的结果；
- 能够自行改变模型结构，利用自身理解对数据处理，模型计算等多方面做出优化，在领域翘楚模型的基础上创造性改造以获取更好的结果，甚至进行创造性的突破。

## 五、结语

~~快逃~~）祝大家能够坚持下来找到自己喜欢的方向并尽快入门这一年轻的学科～

## 可用链接

读者可以尝试阅读下列文档学习 PyTorch 框架：

- [动手学深度学习](https://zh-v2.d2l.ai/)

# 2025年新增内容
课程材料仓库：
https://github.com/sast-summer-training-2024/sast2024-DP-and-Pytorch.git

https://www.bilibili.com/video/BV1FSYueTEWw/?share_source=copy_web&vd_source=5f41358f46c6dc60e03c3ff6ca5a8520
## Pytorch的训练方法类似八股文，百分之99的模型都是这么训练的
- 准备数据集
- 定义模型
- 定义损失函数与优化器
- 在模型上进行训练
- 存储模型参数

https://www.bilibili.com/video/BV1FSYueTEWw/?share_source=copy_web&vd_source=5f41358f46c6dc60e03c3ff6ca5a8520
给出了一个具体的训练实例，建议对照着视频链接逐步操作。一些要点如下。

## 一些要点

### 模型定义
在PyTorch中定义神经网络模型需要继承`torch.nn.Module`类，并实现以下两个核心方法：
```python
class TestModel(torch.nn.Module):
    def __init__(self):
        super().__init__()  # 必须调用父类初始化
        self.layer = torch.nn.Linear(5, 2)  # 定义全连接层

    def forward(self, data):
        return self.layer(data)  # 定义前向传播逻辑
```
- `__init__`方法用于定义网络层结构
- `forward`方法实现具体的前向计算过程

### PyTorch梯度计算与模型优化
梯度计算示例：
```python
test_model = TestModel()
optimizer = torch.optim.SGD(test_model.parameters(), lr=1)
loss_fn = torch.nn.MSELoss()

# 前向计算
data = torch.rand((1, 5))
y = test_model(data)

# 反向传播
loss = loss_fn(torch.randn((1, 2)), y)
loss.backward()  # 自动计算梯度

# 参数更新
optimizer.step()  # 应用梯度更新
optimizer.zero_grad()  # 清空梯度缓存

# 无梯度计算模式
with torch.no_grad():
    loss = loss_fn(...)  # 不记录计算图
```
关键点：
- `loss.backward()`自动计算梯度并存储在Parameter.grad中
- 优化器通过`step()`方法更新参数
- `zero_grad()`防止梯度累加
- `torch.no_grad()`上下文管理器用于推理阶段

### PyTorch数据集处理
自定义数据集实现：
```python
class MyDataSet(Dataset):
    def __init__(self, file: str):
        self.data = []
        # 多线程数据预处理
        with open(file) as fin:
            inputlist = list(fin)
            tlist = [threading.Thread(target=deal_segment, 
                     args=(inputlist[1000*i:1000*(i+1)], self.data)) 
                    for i in range(math.ceil(len(inputlist)/1000))]
            for t in tqdm(tlist):  # 启动所有线程
                t.start()
            for t in tlist:  # 等待线程完成
                t.join()

    def __getitem__(self, index):
        return self.data[index]  # 返回单个样本
    
    def __len__(self):
        return len(self.data)
```
数据加载器使用：
```python
train_loader = DataLoader(
    dataset=train_set,
    batch_size=32,
    shuffle=True  # 训练集需要打乱顺序
)
```

### 训练实例
模型定义：
```python
class MyModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.emb = nn.Embedding(50000, 64)  # 词嵌入层
        self.layer1 = nn.Linear(256*64, 64*128)
        self.ac1 = nn.ReLU()  # 激活函数
        self.layer2 = nn.Linear(64*128, 16*16)
        self.out = nn.Linear(16*16, 2)  # 输出层

    def forward(self, data):
        hidden = self.emb(data).view(-1, 64*256)
        return self.out(self.ac2(...))
```

训练流程：
```python
# 初始化训练组件
model = MyModel().cuda()
loss_fn = nn.CrossEntropyLoss()
optimizer = SGD(model.parameters(), lr=1e-3)

# 训练循环
for epoch in range(EPOCHS):
    for batch, (X, y) in enumerate(train_loader):
        pred = model(X.cuda())
        loss = loss_fn(pred, y.cuda())
        
        loss.backward()
        optimizer.step()
        optimizer.zero_grad()
        
        # 记录训练指标
        if batch % 20 == 0:
            wandb.log({
                "loss": loss,
                "acc": accuracy...
            })
    
    # 保存模型
    torch.save(model.state_dict(), "model.pt")
```

### 模型推理
```python
model = MyModel()
model.load_state_dict(torch.load('model.pt'))
model.eval()  # 设置评估模式

with torch.no_grad():  # 禁用梯度计算
    ids = torch.tensor(tokenizer.encode(sentence).ids)
    out = torch.nn.functional.softmax(model(ids), dim=-1)
    print("预测概率:", out)
    print("预测类别:", torch.argmax(out).item())
```

### 作业要求
实现RNN文本分类任务：
1. 使用GRU/LSTM单元（可手写或使用内置类）
2. 支持可变长度输入（根据实际输入长度循环）
3. 分类准确率超过80%
4. 使用可视化工具绘制训练曲线

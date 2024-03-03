> 本文档用于提交清华大学生物信息学课程（2024春）作业，同时记录本学期生物信息学学习计划或课堂笔记
> 最后修改于2024/3/3

## 本学期生物信息学相关学习计划
- Linux bash脚本编写    form 3/3 to 3/30  参考书《鸟哥的Linux私房菜 基础学习篇》  复习bash语言
- 深度学习RNN初学       from 3/3 to 3/9    参考书《动手学深度学习 PyTorch版》     先配合GPT4看懂和在本地跑通书上的代码
- Git                  from 3/3 to 3/17   参考书                               在Git bash终端上学习基本操作
- 生物信息学课程        every week         参考书《Bioinformatics Tutorial》     每周五及周末完成作业


## SRT 
> 项目预计为多组学与生物信息学手段癌症预测，主要负责一些RNA degradation score方面的探索
> 项目学生报名阶段尚未启动
- week2  数据预处理  将TEP2015 TEP2017 TEP2018 三个数据集的bedgraph文件使用现成python脚本，在bash流水线下批量转化为ks矩阵，每个数据集分别使用longest   transcript, exon1, RP-mRNA三种ref bed文件转化为三个ks矩阵                                                                                1/9
- week2  数据预处理  将TEP2015和TEP2017的longest transcript数据在python pandas下去除空值过多的基因，然后配合相应label做成Pytorch Dataset

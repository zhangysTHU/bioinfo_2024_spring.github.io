> 本文档用于提交清华大学生物信息学课程（2024春）作业，同时记录本学期生物信息学学习计划或课堂笔记
> 最后修改于2024/3/3

## 本学期生物信息学相关学习计划
- Linux bash脚本编写    form 3/3 to 3/30  参考书《鸟哥的Linux私房菜 基础学习篇》  复习bash语言
- 深度学习RNN初学       from 3/3 to 3/9    参考书《动手学深度学习 PyTorch版》     先配合GPT4看懂和在本地跑通书上的代码  完成
- Git                  from 3/3 to 3/17   参考书                               在Git bash终端上学习基本操作         未完成
- 生物信息学课程        every week         参考书《Bioinformatics Tutorial》     每周五及周末完成作业


## SRT time table
> 项目预计为多组学与生物信息学手段癌症预测，主要负责一些RNA degradation score方面的探索
> 项目学生报名阶段尚未启动


### week2:
- 将TEP2015 TEP2017 TEP2022 三个数据集的bedgraph文件使用现成python脚本，在bash流水线下批量转化为ks矩阵，每个数据集分别使用longest   transcript, exon1, RP-mRNA三种ref bed文件转化为三个ks矩阵
- 将TEP2015和TEP2017的longest transcript数据在python pandas下去除空值过多的基因，然后配合相应label做成Pytorch Dataset
- 填充baopengfei留下的ML相关信息线上表格
- 重新评估exons和longest-transcript两个参考bed文件的正确性，给出新的转化脚本
> 实际完成情况：
> 限制于Cnode算力，只完成了2015和2017数据集的ks处理，获得了ks矩阵，处理过缺失值。为了保持机器学习超参数一致没有进行下一步的机器学习操作
> 从baiyilan获得了正确的long_RNA.gtf到longest_transcript.gtf的转化脚本。经过评估，原先的exons.bed不全，原先的longest_transcript.bed条目正确，但是由于只包含完整转录本起止位点，无法用于剔除内含子的ks转换
> 没有给出新的去除内含子的gtf转化脚本
> 尝试改进了multiprocess.py的并行情况，但是没有实际效率改善

### week3
- 不再尝试从ref中去除内含子，从改变multiprocess.py出发，在计算CDF时使用exons计算然后合并，以消除内含子区域
- 阅读和理解baiyilan先前留下的机器学习脚本
- 填充baopengfei留下的ML相关信息线上表格
- 初步根据mRIN值评估TEP2015和TEP2017数据集的batch effect
> 实际完成情况：
> 重写了multiprocess.py，使用gtf作为参考，使用transcriptid对exon进行分组，对同组exon进行长度合并
> 完成了new_exon,new_transcript在2015数据集上的重跑
> 写出了将bedgraph在RP-mRNA.bed参考下放缩为固定bins的python脚本，消除不同基因bedgraph长度不同的问题，作为1DCNN的输入
> 由luhanbo在R上作图分析了2015和2017年RP-mRNA参考下和exon1参考下的batch effect，Welch Two Sample Ttest均显著，在视觉上高斯核拟合曲线较为接近

### week4
- 在Cnode上跑完2017的transcript数据。分析去除内含子前后transcript参考下mRIN的相关性
- 分析已有的2015和2017数据集ksmat和mRIM数据，根据batch作图使用KS检验分析batch effect；尝试分析样本在四种ref下的TPM数据，用于进行batch effect对比
- 分析已有的2015和2017数据集上的NC样本和CANCER样本差异，使用mRIN值在四种ref下进行t-test，在RP-mRNA参考下进行2d PCA聚类分析
- 完善bedgraph_bins_plot脚本，仿照multiprocess的重写将内含子考虑在内
> 已有的2015和2017数据集mRIM数据KS检验分析batch effect在三种参考下均显著，提示来自不同的分布；transcript 2017还没有跑完

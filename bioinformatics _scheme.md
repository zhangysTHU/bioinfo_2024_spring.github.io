> 本文档用于记录2024年生物信息学学习计划或SRT计划，本文档同时挂载于github pages

## SRT milestones table and conduction（2024春）
> 项目预计为多组学与生物信息学手段癌症预测，主要负责一些RNA degradation score方面的探索
> 项目学生报名已完成
> 2024年3月底已完成TEP2015,2017数据集的genes，longest_transcript, exons, exon1, RPgene, RPmRNA; 完成了TEP2022的RPgene，RPmRNA
> 2024年3月底已完成已有数据集和特征下的cancer nc 的t检验
> 2024年4月初完成全部样本下和nc样本下的batch effect验证
- 2024年4月底完成所有基于RF,LR, KNN, SVM, XGBoost的组内验证和交叉验证
- 2024年4月底完成基于放缩后的bedgraph 100bins，1000bins，end or start nuclitide 


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
> 已有的2015和2017数据集mRIM数据KS检验分析batch effect在四种参考下均显著，提示来自不同的分布；
> 去除内含子后的transcript与原transcript的mRIN相关性很小
> 2015和2017两个数据集比较，多种参考下得到的mRIN的差异显著性全部相反

### week5
- 只使用每个数据集中的nc进行batch effect分析
- 对RP-mRNA也进行去除内含子分析，包括batch effect分析和nc-cancer之间的t检验
- 跑完2022的RPmRNA去除或不去出introns的分析
- 完成bedgraph_plot的python脚本，完成训练使用的pytorch脚本
> 对于RP基因，2015，2017，2022三个数据集之间batch effect的比较发现2015并没有出现显著的偏移，反而是2022偏移较大。没有单独使用nc分析
> 2022数据集RP无论是否去除内含子在nc-cancer之间都不显著；2015和2017数据集去除内含子的RP均显著，而且与不去除的趋势相反，而且2015和2017之间趋势也相反
> 2015，2017，2022的RP去除内含子与RPgene相关性很高
> 完成bedgraph_plot脚本

### week6
> 完成了bedgraph_100bins_plot.py的脚本迁移，从本地移动到cnode，加入了参数化调用；完成了调用bedgraph_100bins_plot.py的bash脚本

### week7
- 使用nc对2015，2017，2022进行batch effect分析，不再使用高斯核曲线画图，使用直方图或者箱线图
- 使用baiyilan的single机器学习脚本对已有的2015，2017的transcript，transcript_rmintrons, exon1, Rpgene, RpmRNA进行学习，2022只是用Rpgene, RpmRNA。进行两轮五折交叉验证，不进行cross验证
- 跑完2015，2017，2022的RPgene和RPmRNA bedgraph100bins结果，第一列为样本名，此后每一列是一个bin。使用label中的样本名进行挑选，进行pytorch学习
- 跑通baiyilan的机器学习脚本
> 机器学习的model_early跑通，并且在KNN LR RF SVM XGBoost上试验了TEP2015和TEP2017的RPgene和RPmRNA数据。2015上RPmRNA性能明显更低，2017上相仿且2017稍高
> 2015，2017，2022的RPgene和RPmRNA bedgraph100bins深度学习效果很差，学习会在几个epoch后进入平台，loss和accuracy都不变或略微波动。
> 只是用三个数据集的nc画图，batch effect很小，在t检验下甚至不显著，去除内含子在大多数数据集和参考下会进一步减小batch；

### week8
- 学习跑一下TEP2015,2017,2022的TPM数据，看看nc下是不是也没有了batch effect
- 可能100个bins信息实在太少，或许可以换成1000或10000尝试，用更激进的卷积策略以减少层数（也可以考虑resnet）；做完2015的组内验证；做完2015，2017，2022的交叉验证
- 或许可以了解一下transfromer
- 做完2015和2017所有特征的常规机器学习，并进行cross验证；与2022 cross的话先只考虑RPgene和RPmRNA
> 没有重新跑TPM数据，目前已有的TPM只含2015和2017
> 在不改变CNN模型的情况下，完成了2015，2017，2022之间的交叉验证，交叉验证仍然只含RPmRNA和RPgene的数据，交叉验证的训练集永远是较大数据集。出乎意料的是，交叉验证得到的各个性能要远优于组内验证，特别是TEP2017到TEP2015
> 完成了所有常规机器学习，2022只考虑了RPgene和RPmRNA，feature selection只选取了训练集与label的ANOVA分析的前1000个features

### week9
- 整理之前的batch effect数据，统一使用KS检验和d score，三数据集使用ANOVA和平均d score
- 整理之前的mRIN t检验数据，统一使用非参数t检验。使用的参考为genes，longest_transcripts, exon1, RPgene, RPmRNA
- 尝试将100bins脚本改为1000bins，gtf参考从RP改为56407个gene
- 补全2022的ks数据
> 完成了前期数据整理，提交SRT中期报告。
> 未完成脚本修改

### week11
- 尝试将100bins脚本改为1000bins，gtf参考从RP改为56407个gene
- 补全2022的ks数据
> 实验表明在当前去除内含子的1000bins算法下，使用56407genes作为参考所需时间为RP参考的几百倍，在当前算力下不具备可行性。
> 完成了TEP2022 genes和exon1的计算

### week12
- 尝试使用TCGA数据
- 测试不去除内含子在56407genes参考下的单样本100bins和1000bins处理速度；尝试截断start-end前500或前1000核苷酸，所谓CNN输入，测试去除内含子和不去除内含子的单样本处理速度
- 考虑一下去除non-cancer组中的有症状对照组，让negative组更纯一些
> 完成了RPgene和RPmRNA参考上的di-clips（500+500）和1000bins文件的准备
> 仍使用混合的症状对照与NC共同作为阴性组

### week13
> 完成在TEP2015,2017,2022数据集上基于RPgene和RPmRNA参考的两种格式的1DCNN学习，结果与mKS相同组的五种模型结果合并，使用热图展示F1 score和AUC
> 完成TEP2015，2017，2022数据集的TPM计算
> 部分TCGA样本的数据预处理完成
> 完成基于gene，longest_transcript，exon1，RPgene的TPM的五种模型的机器学习
> 
### week14
> 完成基于个各数据集TPM矩阵的特征与筛选，以减少1DCNN数据预处理时的特征筛选工作量
> 完成在TEP2015,2017,2022数据集上基于gene，longest_transcript，exon1，RPgene的1DCNN学习，结果与mKS相同组的五种模型结果合并，使用热图展示F1 score和AUC。数据处理方法为di-clips（1000）
> 对TEP2015,2017,2022的NC数据，使用RPgene作为参考基因，通过KS检验比较TPM和mRIN之间的批次效应显著程度

### week15-week18：期末考试与夏季学期课程
---
## SRT milestones table（秋）
1. 上学期的cfomics任务继续完善基于mKS的调参，与刘匼等师兄完善特征筛选和机器学习流程
2. Find a novel computational feature better than mRIN; 考虑从RNA表达数据出发，从degradation或secretion的生物学意义进行计算
    - 考虑使用机器学习的方式从DFPKM模拟一种degradation score，仿照
    - 考虑仿照2022训练一种从序列/表达量出发判断RNA亚细胞定位或外泌体包装情况的模型 
> 重新开一个新的SRT项目？结掉cfomics的SRT？
- before week1：新feature的可行性调研
- 9月：部分TCGA数据预处理，计算mKS值和TPM值；TEP mKS数据的数据分割尝试和调参尝试
- 10月：TEP mKS数据的基因选择（feature selection）尝试
- 期中及后续：尝试以RNA seq结果为输入，模拟PARE-seq测序得到的降解DPKM数据；或许可以尝试transformer进行回归学习

  ### week7
> 学习了自注意力机制的基本算法和在pytorch上的部分市县，了解了几个使用自注意力机制的经典模型架构，如bert，transformer，alphafold
> 完成了对RNAlight全部代码的复现
> 完成了对bert-chinese-base-claasificater的微调训练复现

### week8
> 使用exoRbase构建存在于EV中的longRNA数据集；使用来源于RNAlight文章的细胞核/细胞质定位longRNA定义unEV定位RNA（同时出现的RNA分类到EV中）
> 使用RNAlight提供的LR和SVM调参策略对lncRNA和mRNA进行了ev-unev分类，AUC仅有0.61-0.63，原达不到RNAlight文章中核质定位分类AUC0.75的水平
- 额外计划使用bert分类对上述序列进行尝试，可以先从RNAlight文章中提供的核质定位数据开始。bert模型架构参考DNAbert或者Enformer

### week9
- 阅读小RNA定位的综述
以下构建根据实验室已有数据三选一：
- 利用实验室在20种细胞组分中测序的数据，构建一个血浆-血小板-EV定位的sRNA数据集
- 额外构建一个细胞亚定位sRNA的数据集，优先利用RNAlocate数据库构建。可以优先考虑miRNA
- 调研使用DNAbert模型或者exoGRU模型对RNAlight文章工作提升的可能性
> 调研与RNA subcelluar location相关的综述文章和相应ML预测文章，目前研究以long RNA和microRNA为主，数据集主要使用RNAlocate导出

### week10
- 获取一个tsRNA列表（bed文件？），随后从encode来源的GSEGSE24565 seq数据集产生一份带有亚定位标记的tsRNA数据集
- 从RNAlocate构建一个piRNA数据集，考虑优先去除一部分数量过于冗余的exosome定位piRNA
- 从实验室SLE数据集的血浆、血小板、外泌体三个组分中生成一份tsRNA数据集
- 使用LR对试验性数据集进行多分类实验，使用RNAlight的k-mer frequency embedding策略
- 尝试将exoGRU的sRNA embedding策略分离出来，将来应用到tsRNA和piRNA中


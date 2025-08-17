# 二代测序的基本知识点，以及常见问题记录

### 如何做两个组学的联合分析：    
表观组（ATAC-seq、ChIP-seq、CUT&Tag） 与 转录组（RNA-seq）联合分析：    
- 1.联合分析是为了解析染色质开放区域（组蛋白，转录因子，RNA polymerase II 等）与基因表达的关联。利用组学技术分析启动子、增强子或其他调控元件是否处于开放状态。
- 2.基因表达水平的差异：结合RNA-seq，评估这些区域中基因在不同条件下的表达差异，明确染色质开放性与基因表达是否存在直接联系。
- 3.推测特定处理或调控因子的作用机制：识别调控靶基因，将表观组（ATAC-seq、ChIP-seq、CUT&Tag）中识别的结合位点，与下游DEG整合，推测转录因子可能直接调控的靶基因集合。
- 4.构建调控网络：明确上游调控因子如何调控下游基因表达。
- 5.筛选疾病相关转录因子及其靶基因。
- 6.假设ATAC-seq结果中，获得了TF-motif-peak-gene的关系时，需要使用ChIP-seq或CUT&Tag等验证手段补充证据链！

具体绘图：
- 1.韦恩图：即表观组中差异peak（或称DAR），与转录组中的DEG，取交集。（注：一个DEG可能对应多个差异peak，是正常的。）
- 2.四象限图：通常优先研究1，3象限，即染色质开放性增加且基因表达增加的基因，或染色质开放性减少且基因表达减少的基因。（注：一个DEG可能对应多个差异peak，是正常的。此外，基因数少，也是正常的，这时候考虑放宽阈值。有过往的研究，是仅做p value的筛选，或仅做log2FC的筛选的，只要后续实验验证成功，证据链是完整的。）
- 3.表观组检测到的peak靶基因热图：对转录组中，选择P值最小的Top100基因绘制热图，同时标记这些基因是否在表观组存在peak（T / F）。
- 4.对这些有交接的基因，做GO/KEGG富集分析（同时在这些通路中标注上调及下调的基因有多少个）。

### 软件选取横向对比    

|  比对软件   | 适用组学  |  特点 |
| :----: | :----: | :----: |
| BWA  | ChIP-seq、CUT&Tag、ATAC-seq | 适用于全基因组或全外显子测序的比对 |
| Bowtie  | ChIP-seq、CUT&Tag、ATAC-seq | 为较短的DNA序列（35bp）设计，快速比对 |
| Bowtie2  | ChIP-seq、CUT&Tag、ATAC-seq | 长序列（50-100bp），内存占用小，多线程 |
| HISAT2  | ChIP-seq、CUT&Tag、ATAC-seq、m6A-seq、RIP-seq | 支持RNA-seq、DNA-seq比对。有更高的敏感性，快速，支持剪切位点识别和转录组重构 |

|  peak calling 软件   | 适用组学  |  特点 |
| :----: | :----: | :----: |
| MACS2/3  | ChIP-seq、CUT&Tag、ATAC-seq | 可处理单端，双端，通过双峰模型提高结合位点的空间分辨率，去重，归一化，使得数据之间有可比性 |
| SEACR  | CUT&RUN、CUT&Tag | 专门为CUT&RUN、CUT&Tag而制作的peak calling软件。能够将内参或IgG的非特异性结合片段或PCR重复片段去除（待补充） |
| exomepeak  | m6A-seq、RIP-seq | 支持多个生物学重复，内部自动去除PCR重复和多重比对序列，peak鉴定基于外显子的连接位点，增强peak鉴定的准确性 |
| exomepeak2  | m6A-seq、RIP-seq| 考虑重复性和GC偏差矫正 |

|  差异peak分析软件  | 适用组学  |  重复性 |  特点 |
| :----: | :----: | :----: | :----: |
| MAnorm  | ChIP-seq、CUT&Tag、ATAC-seq | 无 | 使用共同峰建立标准化模型，通过经验贝叶斯方法来估计信号变化。 |
| DiffBind  | ChIP-seq、CUT&Tag、ATAC-seq  | 有 | 用不同统计模型计算（DESseq2，edgeR） |
| exomepeak  | m6A-seq、RIP-seq | 有/无 |  Fisher精确检验 |
| exomepeak2  | m6A-seq、RIP-seq| 有/无 | 通过GLM方法进行差异分析，考虑重复性，使用DESeq2将IP和input视为成对样本来鉴定DMR |

|  motif分析软件   | 适用组学  |  特点 |
| :----: | :----: | :----: |
| homer  | ChIP-seq、CUT&Tag、ATAC-seq、m6A-seq、RIP-seq | 使用ZOOPS评分和超几何分布来确定motif富集，可识别和分析转录因子结合位点的motif（findMotifsgenome.pl） |
| MEME  | ChIP-seq、CUT&Tag、ATAC-seq、m6A-seq、RIP-seq | 运用EM算法分析motif，FIMO扫描已知motif，Tomtom用于motif比较，CentriMo用于识别特定位置富集的motif |



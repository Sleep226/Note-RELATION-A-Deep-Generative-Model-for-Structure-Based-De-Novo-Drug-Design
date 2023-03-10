#RELATION:A Deep Generative Model for Structure-Based De Novo Drug Design（基于结构的从头药物设计的深度生成模型）
- De Novo
	- 叫做从头测序，不需要任何基因序列信息，即可对某个物种来进行测序，用生物信息学的分析方法对序列进行拼接组装，从而获得该物种的基因组序列图谱，目前广泛应用于从头解析未知物种的基因组序列，基因组成以及相关物种的进化特点等
- 读论文步骤
	- 先弄懂方法
	- 再弄懂背景和数据
	- 最后结果
##Abstract
- 本文提出了一种基于3D的生成模型，称为RELATION。在RELATION模型中，BiTL算法专门用于提取蛋白质-配体复合物的所需几何特征并将其转移到潜在空间进行分子生成。药效团调节和基于对接的贝叶斯采样被应用于广阔化学空间的导航，以设计具有所需几何性质和药效团特征的分子。作为概念验证，使用RELATION模型设计了AKT1和CDK2两个靶点的抑制剂。计算结果表明，RELATION模型可以有效地生成具有良好亲和力和药效团特征的新分子。
	- BiTL算法：bidirectional transfer learning双向迁移学习
		- **迁移学习**(Transfer Learning)是一种机器学习方法，就是把为任务 A 开发的模型作为初始点，重新使用在为任务 B 开发模型的过程中。迁移学习是通过从已学习的相关任务中转移知识来改进学习的新任务，虽然大多数机器学习算法都是为了解决单个任务而设计的，但是促进迁移学习的算法的开发是机器学习社区持续关注的话题。 迁移学习对人类来说很常见，例如，我们可能会发现学习识别苹果可能有助于识别梨，或者学习弹奏电子琴可能有助于学习钢琴。
##Introduction
- 在药物发现早期鉴定出的候选药物在后期临床试验中失败的风险很高（约95%），导致资源浪费。从头药物设计旨在通过利用先进的计算技术生成具有新颖结构的潜在候选药物来加速药物开发并降低成本。
- 传统的从头药物设计方法可以分为两类：基于配体的和基于结构的。经典的基于配体的方法，需要一组经过实验验证的活性化合物作为起点，活性化合物共享的共同特征可以随后被利用来指导新型候选药物的设计。基于结构的方法，通过递增地添加、删除、插入或替换嵌入目标结合口袋中的化学支架的片段来创造新的小分子。
- 基于深度学习的一些先进的生成算法已被用于新药设计，如递归神经网络、编码−解码器(Enc-Dec)、生成对抗网络(GAN)和强化学习(RL)等。但大多数基于DL的生成算法都是以配体为中心的。因此，分子被表示为1D的SMILES串或2D的分子图。
> ***配体（ligand）***    
> *同锚定蛋白结合的任何分子都称为配体。*在受体介导的内吞中, 与细胞质膜受体蛋白结合, 最后被吞入细胞的即是配体。根据配体的性质以及被细胞内吞后的作用, 将配体分为四大类:Ⅰ.营养物, 如转铁蛋白、低密度脂蛋白(LDL)等; Ⅱ.有害物质, 如某些细菌; Ⅲ.免疫物质, 如免疫球蛋白、抗原等; Ⅳ.信号物质, 如胰岛素等多种肽类激素等。  
> 
- 锚定蛋白是个啥？？？ 
	- 锚定蛋白是一种比较大的细胞内连接蛋白
 
> 细胞本身具有受体receptor，外来的物质及信号可以和受体选择性地结合，这些信息分子就是配体（ligand）
> 
- 在高中时候我学习的“激素调节”部分，就是由由内分泌器官（或细胞）分泌的化学物质进行的调节。其中的**激素**就是第四类配体。
	- 特点：①微量和高效 ②通过体液运输 ③作用于靶器官，靶细胞（激素一经作用，立即被灭活）

- 但是一维/二维表示的一个明显缺点是生成模型无法捕获3D分子几何形状对配体结合的关键影响，可能不足以用于基于结构的从头药物设计中的目标特异性任务。
- 除此之外，仍有少部分模型是将配体或/和蛋白质的3D特征纳入基于DL的生成架构而开发的，但这部分模型没有同时解决从头药物设计中的关键点，如配体结合模式和生成分子的药效团特征。
- *所以*作者提出了RELATION（REceptor-LigAnd interacTION）(受体-配体相互作用),这是一种基于Enc-Dec的从头药物设计生成模型。
> ***Enc-Dec***  
> **所谓编码，就是将输入序列转化成一个固定长度的向量；解码，就是将之前生成的固定向量再转化成输出序列。**    
> Encoder-Decoder（编码-解码）是深度学习中非常常见的一个模型框架，比如无监督算法的auto-encoding就是用编码-解码的结构设计并训练的；比如这两年比较热的image caption的应用，就是CNN-RNN的编码-解码框架；再比如神经网络机器翻译NMT模型，往往就是LSTM-LSTM的编码-解码框架。因此，准确的说，Encoder-Decoder并不是一个具体的模型，而是一类框架。Encoder和Decoder部分可以是任意的文字，语音，图像，视频数据，模型可以采用CNN，RNN，BiRNN、LSTM、GRU等等。所以基于Encoder-Decoder，我们可以设计出各种各样的应用算法。  

- 与现有的DL方法不同，模型以具有原子物理化学性质的配体−受体复合体的三维网格构象为输入，因此，它在生成具有良好的药效团特征和结合模式的特定靶点的分子方面明显更高效。
- 采用结构域分离网络(DSN)进行双向迁移学习（TL），来促进配体和配体−蛋白复合物之间的信息交换。为了不断提高模型的有效性，药效团的约束和基于对接分数的抽样分别由条件生成和对接分数抽样获得。最后，利用该模型设计了AKT1和CDK2的潜在抑制剂。
	- AKT1、CDK2是一种基因编码（癌基因）
##Results and Discussion
###Quality and Properties of the Generated Molecules生成的分子的质量和性质
![不同生成模型的性能](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\不同生成模型的性能.png)  
> Novel_1：在在 RELATION 模型中，该指标是指生成的化合物在源数据集中找不到的比例，而在其他现有的基于 3D 的方法中，它指的是生成的化合物对其训练数据集是新颖的。  
> Novel_2：在现有抑制剂数据集中未找到的生成化合物的比例。  
> Int.Div.：表示内部多样性。  
> FCD:在RELATION模型中，这个度量是指生成的分子与现有抑制剂之间的FCD值;而在其他现有的基于 3D 的方法中，它是指训练数据集的 FCD 值。 (Frechet ChemNet 距离)  
> 
- 用于评估生成的分子与参考数据集之间的化学和生物特性的相似性。 

 
- 表总结了不同的基于 DL 的分子生成模型的比较。可以清楚地观察到，我们的 RELATION 模型的性能在有效性、唯一性、新颖性方面优于其他现有的基于 3D 的生成模型, 以及生成分子的多样性，表明 RELATION 模型在避免对化学空间中无效和重复分子的采样方面具有很大优势。
- 从表1中可以看到，可以发现具有双向TL的RELATION（AAE）和RELATION（VAE）模型比其他模型产生了更高的有效性、唯一性和内部多样性分数。FCD（Fréchet ChemNet距离）值可用于评估生成分子与参考数据集之间化学和生物性质的相似性。显然，TL模型产生的FCD值比非TL模型相对较低，表明使用TL技术时生成分子的化学和生物性质与现有抑制剂更相似。
	- AAE(Adaptive Arithmetic Encoder)自适应算术码编码器
		- 算术编码是一种无失真的编码方法，能有效地压缩信源冗余度，属于熵编码的一种。算术编码的一个重要特点就是可以按分数比特逼近信源熵，突破了Haffman编码每个符号只不过能按整数个比特逼近信源熵的限制。对信源进行算术编码，往往需要两个过程，第一个过程是建立信源概率表，第二个过程是对信源发出的符号序列进行扫描编码。而自适应算术编码在对符号序列进行扫描的过程中，可一次完成上述两个过程，即根据恰当的概率估计模型和当前符号序列中各符号出现的频率，自适应地调整各符号的概率估计值，同时完成编码。尽管从编码效率上看不如已知概率表的情况，但正是由于自适应算术编码具有实时性好、灵活性高、适应性强等特点，在图像压缩、视频图像编码等领域都得到了广泛的应用。 
		- 自适应算术编码在一次扫描中可完成概率模型建立过程和扫描编码过程。
		- 自适应算术编码在扫描符号序列前并不知道各符号的统计概率，这时假定每个符号的概率相等，并平均分配区间[0，1]。然后在扫描符号序列的过程中不断调整各个符号的概率。同样假定要编码的是一个来自四符号信源{A，B，C，D}的五个符号组成的符号序列：ABBCD。编码开始前首先将区间[0,1]等分为四个子区间，分别对应A，B，C，D四个符号。扫描符号序列，第一个符号是A，对应区间为[0,0.25]，然后改变各个符号的统计概率，符号A的概率 为2/5，符号B的概率为1/5，符号C的概率为1/5，符号D的概率为1/5，再将区间[0,0.25]等分为五份，A占两份，其余各占一份。接下来对第二个符号B进行编码，对应的区间为[0.1,0.15],再重复前面的概率调整和区间划分过程。具体的概率调整见表1。
		- ![AAE](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\AAE.png)  
		- 随着符号序列中符号个数的不断增多，自由适应算术编码估计得到的各符号的概率将趋于各符号的真实概率。 
	- VAE（Variational auto-encoder）变分自编码器
		> 它提供了一个将概率图跟深度学习结合起来的一个非常棒的案例
		- 可以大致分成Encoder和Decoder两部分（如下图）。对于输入图片，Encoder将提取得到编码：一个mean vector和一个deviation vector，然后将这个编码（两个vector）作为Decoder的输入，最终输出一张和原图相近的图片。
		- ![VAE](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\VAE.png) 
		- 在 VAE 中，它的 Encoder 有两个，一个用来计算均值，一个用来计算方差。
			- 它本质上就是在我们常规的自编码器的基础上，对 encoder 的结果（在VAE中对应着计算均值的网络）加上了“高斯噪声”，使得结果 decoder 能够对噪声有鲁棒性；而那个额外的 KL loss（目的是让均值为 0，方差为 1），事实上就是相当于对 encoder 的一个正则项，希望 encoder 出来的东西均有零均值。
			- 另外一个 encoder（对应着计算方差的网络）的作用是动态调节噪声的强度的。
		- 直觉上来想，当 decoder 还没有训练好时（重构误差远大于 KL loss），就会适当降低噪声（KL loss 增加），使得拟合起来容易一些（重构误差开始下降）。
		- 反之，如果 decoder 训练得还不错时（重构误差小于 KL loss），这时候噪声就会增加（KL loss 减少），使得拟合更加困难了（重构误差又开始增加），这时候 decoder 就要想办法提高它的生成能力了。
		- 总的来说，重构的过程是希望没噪声的，而 KL loss 则希望有高斯噪声的，两者是对立的。所以，VAE 跟 GAN 一样，内部其实是包含了一个对抗的过程，只不过它们两者是混合起来，共同进化的。
		- ![VAE](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\VAE结构.png) 


---
![不同生成模型的性能](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\inhibitors抑制剂.png)   

- 根据图中的非TL列可以看出，与现有抑制剂相比，VAE和对抗自编码器（AAE）生成的分子分布在完全不同的空间，
- 当使用单向TL算法重新训练模型时，生成的分子分布与现有抑制剂之间的重叠明显增加。
- 如图中的双向TL列所示，RELATION（VAE）和RELATION（AAE）生成的分子几乎完全与现有抑制剂重叠，表明通过在VAE和AAE架构上使用双向TL，生成的分子和现有抑制剂覆盖了相似的化学空间。这些结果也与表1所示的数据（FCD值）一致。
- 因此，随着双向TL算法和几何构象的应用，作者提出的RELATION模型可以捕获训练数据的潜在特征，RELATION模型在生成分子的有效性、唯一性、新颖性和多样性等方面的性能优于其他现有的基于3D的生成模型。 
###Stability of the Results with Respect to Input Orientations.输入方向的结果的稳定性
![不同生成模型的性能](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\不同方向输入的FCD值.png)   

- 该任务的关键问题之一是创建一个对源数据集和目标数据集的方向不敏感的生成模型。当从不同的角度显示内部数据点时，输入数据点的网格格式总是看起来不同，但它包含有关底层真实分子或配体-蛋白质复合物的相同信息。
- 作者使用了10个具有不同方向的AKT1源数据集-目标数据集（在生成分子的质量和属性部分中提到）来训练关系（AAE）模型。最终结果可以发现生成的分子集的质量没有表现出明显的变化。
###Comparison of RELATION and Pharmacophore-BasedRELATION.  （RELATION和基于药效团的RELATION的比较）
![不同生成模型的性能](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\生成分子的药效团分布.png)   
> 贝叶斯优化（Bayesian Optimization，BO）  

- 作者进一步将条件VAE（CVAE）中的药效团特征引入到RELATION的训练中，以使生成过程更适合目标特异性任务。
- 对比的RELATION是指基于AAE的RELATION（因为它在FCD指标方面的表现优于基于VAE的RELATION）。生成分子的药效团分数（rel.SFCR）的分布如图所示。
- 对于AKT1和CDK2，RELATIONpha（基于药效团的RELATION模型）生成的分子比原始RELATION模型生成的分子具有更高的药效团分数。这表明通过将药效团特征引入RELATION，生成的分子可以增强与预设药效团模型的匹配。
###Bayesian Optimization in RELATION.RELATION模型的贝叶斯优化
- 我们的RELATIONpha模型有一个缺陷，无法产生高比例的有效分子。为了提高RELATIONpha模型的采样效率，并在相当大的化学空间中产生一组具有良好对接构象的有效分子，应该使用更强大的采样框架。
- 如图所示，生成分子的药效团分数得到了提高，其中BOdock的性能略优于BOqsar。BOdock生成的分子的对接分数与原始RELATION模型生成的分子相比有显著提高，但BOqsar生成的分子的QSAR分数仅略有变化。还发现，BOdock生成的分子表现出与现有抑制剂相似的药效团和对接分数分布。这些结果表明，BOdock产生的分子更有可能同时具有良好的药效团和对接特征，因此，BOdock可能是靶向特异性任务的更好选择。
###Docking Results of the BO Sampling Process.BO采样过程的对接结果
![不同生成模型的性能](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\RELATION模型不同采样方式生成的化学空间.png)  

- 为了进一步研究基于BO采样的RELATION模型的性能，作者将不同模型生成的有效分子与AKT1抑制剂再次进行了T-SNE分析。如图所示，RELATION和RELATIONpha模型不能有效地探索AKT1抑制剂的化学空间（红色圆圈中标记的点）。
	- T-SNE（t-distributed Stochastic Neighbor Embedding）（t分布-随机邻近嵌入）
		- 如果想象在一个三维的球里面有均匀分布的点，不难想象，如果把这些点投影到一个二维的圆上一定会有很多点是重合的。所以，为了在二维的圆上想尽可能表达出三维里的点的信息，采取的方法：把由于投影所重合的点用不同的距离（差别很小）表示。这样就会占用原来在那些距离上的点，原来那些点会被赶到更远一点的地方。t分布是长尾的，意味着距离更远的点依然能给出和高斯分布下距离小的点相同的概率值。  
		> t-SNE本质是一种嵌入模型，能够将高维空间中的数据映射到低维空间中，并保留数据集的局部特性。  
		> t-SNE可以算是目前效果很好的数据降维和可视化方法之一。  
		> 该算法在论文中非常常见，主要用于高维数据的降维和可视化。
- 然而，将BO采样引入RELATION模型后，采样分子在化学空间中的分布比原始RELATION模型更加分散，这表明生成分子的与AKT1空间更相似。
- 此外，根据点的颜色梯度，从基于BO的RELATION模型中采样的分子比从原始RELATION模型中采样的分子表现出更有利的对接分数。
##Conclusion
- 在这项研究中，我们提出了一种新的基于深度学习的方法，即RELATION，用于基于蛋白质-配体复合物的 3D 结合构象的分子生成。 DSN 被用作 RELATION 的主要架构，以促进双向 TL 中配体和配体-蛋白质复合物之间的信息交换。随着药效团约束和基于BO的采样的引入，RELATION 模型可以生成具有所需特性的新化合物。
- 针对 AKT1 和 CDK2 进行目标特异性化合物设计以研究 RELATION 模型的性能。首先，我们对 RELATION 模型生成的分子进行了分子质量评估和T-SNE分布分析。发现在RELATION模型中实现的双向TL在模拟现有抑制剂的物理化学性质方面比其他方法具有很大的优势。其次，为了能够创建具有所需特征的新分子，将条件生成和基于 BO 的采样引入到 RELATION 架构中。对接计算结果表明，优化后的RELATION生成的分子与现有抑制剂具有更好的结合亲和力和更高的相似性，具有更高的药效团匹配分数。总体评估结果证明了我们的 RELATION 模型对此类特定目标任务的有效性。我们预计我们的模型将成为基于结构的从头药物设计的宝贵工具。
##Experimental Section
###Data Set Preparation（数据集准备)  
- 总体来说，由于RELATION引入了双向TL，所以模型训练使用了源数据集和目标数据集两种数据集：  
	- 源数据集：基于 ZINC Clean Lead 数据库准备的。
		- 去除了 带电原子 或 除了碳、氮、氧、硫、氟、氯、溴和氢之外的原子
		- 然后选择分子量为200 ~ 600同时，logP（通过在RDKit中实现的Crippen的模型计算）在- 2 ~ 6之间的分子作为源数据集
		- 最终源数据集包含1,200,000个小分子。
	- 目标数据集：基于BindingDB,ChEMBL,and PDB data bank，集了407个AKT1抑制剂和1017个CDK2抑制剂(IC50 <50nM)。
		- IC50 (half maximal inhibitory concentration)是指被测量的拮抗剂的半抑制浓度。IC50值可以用来衡量药物诱导凋亡的能力，即诱导能力越强，该数值越低。
- 然后，将这两个抑制剂数据集分别插入AKT1 (PDB ID: 4GV151)和CDK2 (PDB ID: 4KD161)的结合袋中。
- 然后将两个靶点的抑制剂对接到靶标蛋白，只保留配体周围5 Å的原子作为蛋白配体复合物数据集
- 最后，源数据集和目标数据集中的每个分子由其坐标（张量的前三个维度）和特征向量定义的4D张量表示。
###Architecture of RELATION（RELATION的体系结构）
![RELATION模型架构图](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\RELATION模型架构图.png)  

- 基于VAE的RELATION模型主要由两个部分组成，如图所示。第一部分是3D卷积编码器，包括私有编码器和共享编码器。
	- **Pha+3D**
	- 全称：3D pharmacophores
	- 在描述和分析大分子与配体相互作用时，从大分子靶标的三维药效团的角度出发的化学功能分子特征是最有趣的相似度量。
		- 而本文做的模型就是以具有原子物理化学性质的配体−受体复合体的三维网格构象为输入
- 第二部分是caption-LSTM解码器。
- 训练数据转换为4D张量后，进入编码器通过八个隐藏层。
	- 四维totensor：1.batch_size 2.channel 3.像素长 4.像素宽
- 第一层包含64个滤波器，然后在每个奇数层上加倍，最后一层包含512个滤波器。每个偶数层后面都有一个额外的池化层，内核大小、步幅和填充为2，以执行下采样。
	> kernel和filter区别  
	> 一个"kernel"更倾向于是2D的权重矩阵。而'filter"则是指多个Kernel堆叠的3D结构。如果是一个2D的filter，那么两者就是一样的。但是一个3Dfilter, 在大多数深度学习的卷积中，它是包含kernel的。
	> 在3D卷积中，滤波器的深度要小于输入层的深度（也可以说卷积尺寸小于通道尺寸）。所以，3D滤波器需要在数据的三个维度上移动（图像的长、宽、高）。在滤波器移动的每个位置，执行一次卷积，得到一个数字。当滤波器滑过整个3D空间，输出的结果也是一个3D的。
- 利用ReLU激活函数对3D-CNN模型进行训练，生成并合并一个1024维的嵌入向量。
	- 非线性变换变换ReLu（激活函数）
		- 将大于零的数返回自己本身，小于0的数都返回为0
		> 目的：给网络当中引入非线性特征，因为非线性越多，才能训练出符合各种曲线、各种特征的一个模型。若都是线性，则模型的泛化能力就不够好  
- 在最终输出层之前，应用三层 LSTM 解码器将潜在向量转换为 SMILES 令牌序列，其中词汇输入的大小和隐藏大小分别为 39 和 1024
	- SMILES分子式（simplified molecular input line entry specification）
		- 是一种用于输入和表示分子反应的线性符号,是一种ASCII编码
		> 优点：唯一性、节省空间
	- 编码器和解码器都不是固定的,可选的有CNN/RNN/BiRNN/GRU/LSTM等等，你可以自由组合。[Encoder_Decoder](E:\MarkDown笔记\Encoder_Decoder.md)  
	> 第一次发现markdown文件还可以交叉引用另一个markdown文件
- 我们还尝试通过将 GAN 框架引入架构来探索基于AAE 的关系模型的有效性。在基于AAE的RELATION模型中，添加了一个额外的鉴别器。输入数据通过共享和私有编码器转换为潜在代码H，然后由解码器网络解码。另一方面，鉴别器网络将预测给定向量是由AAE生成还是从N(0, I) 中采样的向量。鉴别器由两个密集层组成，输出大小为 128。
	- [Gan](E:\MarkDown笔记\GAN.md)
###Loss Function of RELATION（关系的损失函数）
![](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\总损失函数.png)  
 
- 受DSN的启发，总损失函数定义如下：
	- DSN(domain separation networks域分离网络)
	- ![](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\DSN损失函数.png )
- Lsim和βLdiff的引入使隐藏层能够同时使用源数据集和目标数据集进行双向TL。
	- 与单向 TL 不同，在生成过程中不仅要考虑源数据集中的复合物和目标数据集中的配体之间的相似性，还要考虑源数据集（结构多样性）和目标数据集（蛋白质-配体亲和力）也被保留。
- 其中，α，β和γ表示控制每个损失项的权重因数，并且分别被设置为0.01、0.075和0.25
	- 设置的参数根据是启发点DSN
- Llantent（隐藏层）表示编码器被先验正则化，先验遵循均值和单位方差为零的多元高斯分布
- Lcaption为重构损失函数
- Ldiff用于保证隐藏层小分子和复合物各自数据的特点
- Lsim（similarity）用于评估配体和复杂数据集之间的相似性
###Assessment
- 微调是执行此类迁移任务最常用的 TL 技术。生成神经网络通常使用数百万个分子（源域）进行训练，这允许模型生成有效的化学结构，然后，网络由小数据集（目标域）重新训练。
- 实际上，微调技术是一种单向TL。特征从源域转移到目标域，然后，再训练的模型可以生成偏向目标域属性的分子。在我们的 RELATION 模型中，生成的分子利用两个数据集的属性通过双向 TL 进一步利用抑制剂的化学空间。
- 几个广泛使用的基准（公式 8-11）用于评估生成的分子集 (G) 与现有（或训练）集 (E) 的质量。 Validity（公式 8）用于评估 G 中 SMILES 字符串的有效率，其中 NG 和 VG 分别是生成的分子数和 G 中有效 SMILES 字符串的数量。 Novelty（公式 9）用于评估存在于 G 中但不存在于 E 中的化合物的比例。唯一性（公式10）用于评估G中非重复和有效分子的比率。内部多样性用于评估G中化合物的多样性，其中公式11中的最后一项计算平均Tanimoto系数（T）生成的分子。计算公式 12 定义的 Fréchet ChemNet 距离 (FCD) 以测量 G 中生成的分子与训练数据之间的相似性，其中 m 和 Σ 分别指的是 ChemNet 倒数第二层激活的平均向量和协方差矩阵。
![](E:\MarkDown笔记\基于结构的从头药物设计的深度生成模型图片\评估.png )

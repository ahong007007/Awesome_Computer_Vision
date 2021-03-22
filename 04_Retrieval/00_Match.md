# match

## Table of Contents

- [survey](#survey)
- [Dataset](#Dataset)
- [benchmark](#benchmark)
- [Detector](#Detector)
- [Descriptors](#Descriptors)

## survey

overview/review/survey

- TPAMI综述，包含从SIFT到2017年的图像检索发展。
  - [SIFT Meets CNN: A Decade Survey of Instance Retrieval](https://arxiv.org/pdf/1608.01807.pdf)

- CVPR2017论文，比对SIFT、SIFT-PCA、 DSP-SIFT、 ConvOpt、 DeepDesc 、TFeat、 LIFT等算法的性能。
  - [2017][CVPR][Comparative Evaluation of Hand-Crafted and Learned Local Features](https://demuc.de/papers/schoenberger2017comparative.pdf)

- [2019][A Benchmark on Tricks for Large-scale Image Retrieval](https://arxiv.org/pdf/1907.11854.pdf)
- [微信扫一扫，图像检索](https://mp.weixin.qq.com/s/fiUUkT7hyJwXmAGQ1kMcqQ)
- <https://github.com/willard-yuan/awesome-cbir-papers>
- <https://paperswithcode.com/task/image-retrieval>
- <https://github.com/shamangary/awesome-local-global-descriptor>
- [Image Registration: From SIFT to Deep Learning](https://www.sicara.ai/blog/2019-07-16-image-registration-deep-learning)

- Covering all SOTA detector, descriptor for slam.
  - <https://github.com/luigifreda/pyslam>

- https://local-features-tutorial.github.io/
  - <https://local-features-tutorial.github.io/pdfs/Local_features_from_paper_to_practice.pdf>

---

## Dataset

- Phototourism :a 715-image reconstruction of Notre Dame Cathedral in Paris.
- The Phototourism dataset,training 25.6k，test:4107.
  - <http://phototour.cs.washington.edu/datasets/>

- HPatches:580 image pairs。包含三个任务: patch verification, image matching and patch retrieval。
  - [A benchmark and evaluation of handcrafted and learned local descriptors]
  - <https://github.com/hpatches/hpatches-benchmark>

- MegaDepth
  - 1,070,468 images from 196 different scenes and reconstructed by COLMAP along with their depth maps and intrinsics/extrinsics matrices.
  - <https://research.cs.cornell.edu/megadepth/>

- Aachen Day-Night dataset:Reference images:4,328,Query images:922 (824 daytime, 98 nighttime)
  - [Long-team visual localization](https://www.visuallocalization.net/benchmark/)

- Oxford 5k and Oxf105k
- Paris 6k Par106k

- [Google Landmarks Dataset v1](https://github.com/cvdfoundation/google-landmark)
  - <https://www.kaggle.com/google/google-landmarks-dataset>

- [Google Landmarks Dataset v2](https://github.com/cvdfoundation/google-landmark)
  - <https://drive.google.com/file/d/1d-xOKHTedhUjk5rsNmIInhyRMCUJbPC7/view>
    - ● Recognition Training set
    - ○ 4.1M images (3.7x increase)
    - ○ 200K unique landmarks (4x increase)
    - ● Retrieval Index set
    - ○ 762k images (1⁄3 decrease)
    - ○ 101k unique landmarks (6.6x increase)
    - ● Test set
    - ○ 118k images (~same)
    - ○ 1.2k have ground truth (~same)
    - ○ Split into “Public” (1⁄3) and “Private” (2⁄3)
    - ○ Most are “distractor queries”
- [Google Landmark Retrieval 2019](https://www.kaggle.com/c/landmark-retrieval-2019)
  - <https://github.com/cvdfoundation/google-landmark>
  - <https://landmarksworkshop.github.io/CVPRW2019/>
  - The goal of the Landmark Recognition 2019 challenge is to recognize a landmark presented in a query image,
   while the goal of Landmark Retrieval 2019 is to find all images showing that landmark.

- 图像街景识别数据集。
  - [Mapillary Street-Level Sequences: A Dataset for Lifelong Place Recognition](https://research.mapillary.com/img/publications/CVPR20c.pdf)
  - Extended CMU Seasons
  - InLoc
  - RobotCar Seasons
  - SILDa Weather and Time of Day Dataset

---

## benchmark

- CVPR workshop:Local Features & Beyond
  - <https://image-matching-workshop.github.io/>

- CVPR workshop:Long-term Visual Localization
  - <https://www.visuallocalization.net/benchmark/>

- Image Matching Challenge 2020
  - <https://vision.uvic.ca/image-matching-challenge/>
  - [Image Matching across Wide Baselines: From Paper to Practice](https://arxiv.org/pdf/2003.01587.pdf)

- Map-based Localization for Autonomous Driving
  - <https://sites.google.com/view/mlad-eccv2020/home>

## Google Landmark Retrieval 2019

- 百度基于PaddlePaddle的开源方案。鉴于PaddlePaddle小众，工业项目设计实在不敢用。
  - [2019][2nd Place and 2nd Place Solution to Kaggle Landmark Recognition and Retrieval Competition 2019]
- The 1st Place Solution of the Google Landmark 2019 Retrieval Challenge and the 3rd Place Solution of the Recognition Challenge.
  - [Two-stage Discriminative Re-ranking for Large-scale Landmark Retrieval](https://arxiv.org/pdf/2003.11211.pdf)
  - <https://github.com/lyakaap/Landmark2019-1st-and-3rd-Place-Solution>

---

## End-to-end matching pipeline

- 最近因为项目需要，重读图像匹配领域经典算法SIFT，追根溯源寻求特征匹配来龙去脉。这几年算法大多直接CNN，个人失去探索本源和对原理的理解。根据Opencv对SIFT总结，对比原论文，主要有以下几个部分：
  - Scale-space peak Selection：对多尺度图像高斯模糊处理，图像金字塔生成Difference of Gaussians(DoG)， 搜索DoG空间极值点,获取潜在特征点。特征是在多尺度空间获取， 所以具有多尺度不变形。
  - Keypoint Localization：Hessian matrix计算主曲率，去除位于图像边缘和无明显差异特征点。
  - Orientation Assignment：使用直方图统计邻域内像素的梯度和方向。梯度直方图将360°平均分配36个bins。以直方图中最大值作为该关键点的主方向。为了增强匹配的鲁棒性，只保留峰值大于主方向峰值80％的方向作为该关键点的辅方向。
  - Keypoint descriptor：特征点为中心选16x16的区域，划分为16个4*4子区域。每个4*4计算8个方向直方图，得到128维度特征向量。
  - Keypoint Matching:采用最接近距离与第二最接近距离之比0.8，去除无效匹配点。
  - [Distinctive Image Features from Scale-Invariant Keypoints](https://www.cs.ubc.ca/~lowe/papers/ijcv04.pdf)
  - <https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_feature2d/py_sift_intro/py_sift_intro.html>

- match一篇经典论文。
  - [CVPR][2012][Three things everyone should know to improve object retrieval](https://www.robots.ox.ac.uk/~vgg/publications/2012/Arandjelovic12/arandjelovic12.pdf)
  
- 洛桑联邦理工学院(EPFL)提出的CNN一个框架实现detection+orientation estimation+feature description，准确性完胜经典特征匹配方法SIFT。
  - 网络结构：基于Siamese Network，训练集特征点来自于SfM的特征点(SIFT提取的特征？)，输入是特征点所在的多尺度image patch(# TODO 每个image patch对应一个特征点？).STN修正图像块得到特征点检测和方向估计.
  - 训练过程：首先训练描述符，然后用来训练方向估计，最后训练特征点检测.
  - 对比试验：论文把三个结构分布用SIFT替代，交叉试验论文只有LIFT的三个部分效果最好。
  - 缺点：速度慢。一般论文没有实时性对比数据，默认实时性不行。
  
  - [2016][ECCV][LIFT: Learned Invariant Feature Transform](https://arxiv.org/pdf/1603.09114.pdf)
  - <https://github.com/cvlab-epfl/LIFT>
  
- 香港科技大学提出GeoDesc，整合多视图几何约束的局部特征子学习方法，在数据生成、数据采样、和损失函数三个方面对匹配进行改善。
  - 数据生成：借助传统SFM方法，得到三维点及其对应的一系列像素块的对应关系。进一步基于Delaunay triangulation对点云滤波。
  - 数据采样：通过像素块/图片对的几何相似度估计，构建"硬"样本，硬是指“同一三维点对应的不同像素块差异尽可能大，不同三维点对应的像素块差异尽可能小”。
  - 损失函数：包括结构损失函数以及几何损失函数。
  - [2016][ECCV][GeoDesc: Learning Local Descriptors by Integrating Geometry Constraints](https://arxiv.org/pdf/1807.06294.pdf)  

- 中科院自动化所提出，CVPR2017论文，提取一种基于孪生网络提取图像块描述子方法。论文主要有三个创新工作：
  - progressive sampling strategy：随机采样获取正样本和负样本，通过L2范数计算特征描述子的距离矩阵。
  - 损失函数包括：描述子相似性，描述子紧密性，中间过程特征。
  - [2017][CVPR][L2-Net: Deep Learning of Discriminative Patch Descriptor in Euclidean Space](http://www.nlpr.ia.ac.cn/fanbin/pub/L2-Net_CVPR17.pdf)
  - <https://github.com/yuruntian/L2-Net>

- Magic Leap公司提出的一种自学习训练特征检测与匹配方法。实现特征点匹配是需要像素级像素级匹配，很难人工直接标注，主流基于CNN的方法是SfM提取特征点或者三维点云匹配点训练。
论文提出的自学习方法，合成数据集训练网络。分为三个阶段：
  - Interest Point Pre-Training：利用基本几何元素(直线，多面体等)渲染得到真值，训练MagicPoint网络得到提取基本形状元素特征点的模型。
  - Interest Point Self-Labeling：对一般图像(MS-COCO数据集)做单应变换，MajicPoint模型对图像提取特征点，获取匹配点真值并训练网络。
  - Joint Training：对任意两张图像的两对图像warp求loss，优化匹配点距离(非匹配点距离大，匹配点距离小)，得到特征点的描述符。
  - 疑问：描述符的feature map分辨率为原始图像的1/8，8x8的图像patch公用一个描述符？
  - 缺点：论文没有公布训练集和训练代码，youtube有论文模型的演示效果，在高清图像和视频表现欠佳。
  - [2018][CVPRW][SuperPoint: Self Supervised Interest Point Detection and Description](https://arxiv.org/pdf/1712.07629.pdf)
  - <https://github.com/MagicLeapResearch/SuperPointPretrainedNetwork>

- NIPS2018论文。索尼，epfl提出局部特征提取方法。
  - 缺点：训练和测试数据集简单，容易过拟合。室内数据集是ScanNet，室外使用25 photo-tourism image。
  - [2018][NIPS][LF-Net: Learning Local Features from Images](https://arxiv.org/pdf/1805.09662.pdf)
  - <https://github.com/vcg-uvic/lf-net-release>
  
- ICCV2017论文，Google提出Google-Landmarks Dataset数据集和DELF(DEep Local Feature)算法。
Google-Landmarks包含100万图像，11万检索图像,图片分布在187个国家，4872城市。DELF基于CNN计算实现大规模数据检索，包括特征提取和匹配。
backbone基于ResNet50(ImageNet预训练),图像首先预处理(输入图像分辨率中心裁剪和缩放，用于训练)，训练过程包括两个阶段：Descriptor 
Fine-tuning和Attention-based训练。模型训练集只需要分类的标注，没有像素级的标注。
为了提高图像检索效率，使用PCA把特征维度减少到40。
  - google基于CNN的匹配三年连发三篇定会文章，果然高产。
  - 疑问：论文描述训练过程， 有裁剪和缩放到224*224，有裁剪和缩放到720*720，以何为准？分别对应两阶段训练的策略。
  - Descriptor Fine-tuning和Attention-based是怎样的过程？
  - [github](https://github.com/tensorflow/models/tree/master/research/delf)
  - [2017][ICCV][Large-Scale Image Retrieval with Attentive Deep Local Features](https://arxiv.org/pdf/1612.06321.pdf)
  - [2018][CVPR][Revisiting Oxford and Paris: Large-Scale Image Retrieval Benchmarking](https://arxiv.org/pdf/1803.11285.pdf)

- Google出品DELF升级版，基于检测和检索融合的图像匹配方法。论文主要有三个贡献：
  - 1.标注Google Landmark Boxes dataset(86k images)数据集，相当于图像的目标检测框真值。
  - 2.训练检测器实现高效区域检索：网络模型backbone输出局部特征和目标检测框。
  - 3.借助于区域特征融合R-ASMK(regional aggregated selective match kernel)，实现图像鉴别：第一阶段生成VLAD描述子，第二阶段基于求和池化和归一化。
  - 论文提出的模型以大欺小，基于Google Landmarks dataset数据训练的模型DELF-GLD，在ROxford 和RParis 数据集实现state-of-art水平。
  - DELF在线计算平台<https://colab.research.google.com/github/tensorflow/hub/blob/master/examples/colab/tf_hub_delf_module.ipynb#scrollTo=mVaKXT3cMSib>
  - [2019][CVPR][Detect-to-Retrieve: Efficient Regional Aggregation for Image Search](https://arxiv.org/pdf/1812.01584.pdf)

- 巴黎多芬纳大学,微软等联合提出D2-Net，基于CNN提取描述子特征。传统提取特征的SIFT等是先检测关键点再提取描述子方式(detect-then-describe)，特征是稀疏的。
而论文提出detect-and-describe：先基于backbone提取特征，在local descriptor维度计算soft-NMS,在channel维度计算ratio-to-max，最后归一化计算图像像素对应的描述子，
提取的特征是稠密的，不受光照/低纹理特征影响。训练集基于MegaDepth数据集，可提取匹配对，不需要标注。在测试阶段使用多尺度detection和参考SIFT算法修订描述子，提高匹配算法的鲁棒性。
论文实验在HPatches验证匹配性能，验证三维重建能力和Day-night数据集验证定位能力，表现state-of-art性能。
  另外特征D2-Net提取与Loss不容易理解，可参考github源码。

  - 疑问：开源代码代码中训练使用的是model.py中的SoftDetectionModule，测试使用的是model_test.py的HardDetectionModule，流程不太一样，待解决。
  - 目前开源代码给的backbone是VGG19，ResNet50/ResNet101是否有更好的表现？特征提取时可以用GPU实现，匹配需要用CPU，对模型加速？
  - Aachen Day-Night localization dataset数据集图片在5K+左右量级。
  - D2-Net和R2D2都依赖dense gt correspondences, 比如D2-Net依赖MegaDepth，而R2D2是用EpicFlow自己插值出来的，获取成本和精度都是麻烦事。
  - 使用缺点：D2-net多尺度提取1.8w*512特征，显存12G，M40GPU, 4-5s/picture。两张图的图片匹配需要30s。匹配特征容易集中，既图像的一部分匹配点较多，另一部分没有匹配点。
  - keypoint localization不太准， 难以集成到SfM或者SLAM对geometry很敏感的任务。
  - [2019][CVPR][D2-Net: A Trainable CNN for Joint Description and Detection of Local Features](https://arxiv.org/pdf/1905.03561v1.pdf):star: :star: :star: :star: :star:
  - [Supplementary Material](https://dsmn.ml/files/d2-net/d2-net-supp.pdf)
  - [github](https://github.com/mihaidusmanu/d2-net)

- Landmarks CVPR19 workshop论文。Google Landmark检索第一名和识别第三名。看这篇论文用了很多tricks,Google-Landmarks-v2数据清洗，backbone集成6个模型：FishNet-150,ResNet-101和ResNetXt101等，
损失函数结合人脸识别的cosine-based softmax losses。cosine annealing,mean-pooling (GeM)，finetuning,Discriminative-Reranking等。从实验数据看，Ensemble 6 models仅提升1%，但是实时性应该打折扣。
  - [2019][Large-scale Landmark Retrieval/Recognition under a Noisy and Diverse Dataset](https://arxiv.org/pdf/1906.04087v2.pdf)
  - <https://github.com/lyakaap/Landmark2019-1st-and-3rd-Place-Solution>
  - <https://drive.google.com/file/d/1QmC4UKRhIXNW-sa8jxV5b7I6QbPKvJsi/view>

- 论文基于LF-Net框架，提出增加感受野和改进是损失函数，在HPatches训练网络。缺点：HPatches数据量少，网络容易过拟合。
  - [2019][CVPR][RF-Net: An End-to-End Image Matching Network based on Receptive Field](https://github.com/Xylon-Sean/rfnet)
  - [2019][ICCV][ELF: Embedded Localisation of Features in Pre-Trained CNN](https://github.com/abenbihi/elf)
  
- NIPS2019论文。对于重复特征区域(棋盘格，树木等场景)，显著性的特征不容易区分，需要置信度区分。论文在输出descriptors和reliability同时，输出repeatability。
  - [2019][NIPS][R2D2: Repeatable and Reliable Detector and Descriptor](https://arxiv.org/pdf/1906.06195.pdf)

- 强化学习在图像特征匹配中的应用。
  - [2019][Reinforced Feature Points:Optimizing Feature Detection and Description for a High-Level Task](https://arxiv.org/pdf/1912.00623.pdf)

- 香港科技大学等提出ASLFeat，目标是提取准确的局部特征和匹配能力。
  - 论文认为匹配性能的提升有两个限制：特征点缺乏shape-awareness几何形变能力，关键点定位不准确难以解决相机模型的鲁棒性。
  - 论文提出三个创新：deformable convolutional network解决几何形变；特征金字塔实现 low-level和high的特征融合；在D2net基础上，改进peakiness measurement。
  - loss函数设计：在D2net基础上改进hardest-contrastive,更容易收敛。
  - 模型在图像匹配(HPatches)效果明显，但是在3D reconstructionn(ETH benchmark)和Visual Localization(Aachen Day-Night dataset)等验证结果貌似和D2Net,R2D2不相上下，没有state-of-art水平。
  - ASLFeat缺点：大尺度旋转的问题没有很好解决(谁来拯救形变？)， 特征点提取10K，匹配阶段会很耗时。
  - [ASLFeat: Learning Local Features of Accurate Shape and Localization](https://arxiv.org/pdf/2003.10071.pdf)
  - <https://github.com/lzx551402/ASLFeat>

- 神经网络提取描述子和特征点，需要SfM或者其他方式获取的真值，而真值获取相对较为困难。论文提出用相机姿态估计的方法得到对极几何约束，构建弱监督学习。
  - 相机姿态如何获取？SfM,和直接获取特征点真值有何区别？
  - 测试集如果不是同一个相机拍摄，内参不一致，相机姿态如何获取？可适配所有匹配类型？
  - [Learning Feature Descriptors using Camera Pose Supervision](https://arxiv.org/pdf/2004.13324.pdf)

- 首先输入两张图->dense feature提取->4D CNN直接回归match->邻域一致性约束refine match->最终match.
  - 缺点：内存消耗过大，速度慢，效果差。
  - [NIPS2018][Neighbourhood consensus networks](https://arxiv.org/pdf/1810.10510.pdf)

- 论文在NCNet基础上，提出可训练的特征提取、匹配和匹配过滤(网络包括匹配的所有过程)，直接输出匹配关系。
  - 为了解决之前dense match的问题，首先将feature进行knn match 然后得到top k的最近邻，之后构造4D匹配空间的时候将没在topk的地方设置成0，这样其实就是一个sparse矩阵，减少计算量。
  - [CVPR2020][Efficient Neighbourhood Consensus Networks via Submanifold Sparse Convolutions](https://arxiv.org/pdf/2004.10566.pdf)

---

## Detector

- CVPR2019论文科技大学提出，两种增强局部特征描述符上下文信息的方法：high level图像表示的视觉上下文信息和关键点分布的几何上下文信息。
  - [2019][CVPR][ContextDesc: Local Descriptor Augmentation with Cross-Modality Context](https://arxiv.org/pdf/1904.04084.pdf):star: :star: :star: :star: :star:
  - <https://github.com/lzx551402/contextdesc>
  - [2019][CVPR][SOSNet:Second Order Similarity Regularization for Local Descriptor Learning]
  - <https://github.com/yuruntian/SOSNet>

---

## Descriptors

- 继承L2Net，对Patch提取特征向量。
  - [Working hard to know your neighbor's margins: Local descriptor learning loss](https://arxiv.org/pdf/1705.10872.pdf)
  - <https://github.com/DagnyT/hardnet>

- 论文提出对描述子距离度量的改进。First Order Similarity (FOS) loss近似认为是描述子的L1距离，论文提出Second Order Similarity Regularization(SOS)，用FOS+SOS作为损失函数度量特征向量的距离。
  - [SOSNet: Second Order Similarity Regularization for Local Descriptor Learning](https://arxiv.org/pdf/1904.05019.pdf)

- NIPS2019论文，浙江大学提出，主要解决在不同视角图像的匹配关系。论文不是在旋转图像上直接提取特征，而是包含两个分支：直接在旋转图像的特征金字塔提取特征和在变化图像的特征金字塔提取特征，再经过分组卷积和
Bilinear Pool，提取像素的描述符。模型需要和特征检测器(Superpoint/DoG/LF-Net)配合，不是end-to-end方式。如论文使用的评测数据集HPSequences和SUN3D数据量都不足1K,对比试验也仅仅基于SIFT和GeoDesc，实验数据不具有代表性，但是提出对图像/特征均进行映射变变换，具有参考意义。
  - [NIPS][2019][GIFT: Learning Transformation-Invariant Dense Visual Descriptors via Group CNNs](https://arxiv.org/pdf/1911.05932.pdf)

---

## Geometric-verification

- RANSAC in 2020: A CVPR Tutorial
  - <http://cmp.felk.cvut.cz/cvpr2020-ransac-tutorial/>
- Benchmarking Robust Estimation Methods
  - <http://cmp.felk.cvut.cz/cvpr2020-ransac-tutorial/presentations/RANSAC-CVPR20-Mishkin.pdf>

DEGENSAC GC-RANSAC  

- Robert B. Fisher等人提出。
  - 随机抽样一致（RANSAC）是一种通过使用观测到的数据点来估计数学模型参数的迭代方法。
  - [1981][Random sample consensus: a paradigm for model fitting with applications to image analysis and automated cartography](https://apps.dtic.mil/dtic/tr/fulltext/u2/a460585.pdf)

- Czech Technical University(捷克理工大学)提出对RANSAC的改进。
  - 论文提出了一种阈值一致性算法，RANSAC需要设定内点阈值？
  - 新的模型质量评估函数和新的迭代条件。
  - [MAGSAC: marginalizing sample consensus](https://arxiv.org/pdf/1803.07469.pdf)

- CVPR2019论文，对RANSAC的改进。包含第三方python库pymagsac，可以无缝替代RANSAC。
  -[2019][MAGSAC++, a fast, reliable and accurate robust estimator](https://arxiv.org/pdf/1912.05909v1.pdf)
  -<https://github.com/ducha-aiki/pymagsac>

- ETH Zurich提出，基于Attentional Graph Neural Network+Optimal Matching Layer，解决RANSAC匹配不准确的问题。
  - [CVPR2020][SuperGlue: Learning Feature Matching with Graph Neural Networks](https://arxiv.org/pdf/1911.11763.pdf)
  - <https://github.com/magicleap/SuperGluePretrainedNetwork>

- ETH Zurich提出，AdaLAM的全称是Adaptive Locally-Affine Matching(自适应局部仿射匹配)，本文提出了一种高效快速外点滤除算法：论文首先找到初始匹配点，置信度高且分布较好的点作为种子，在局部区域选择匹配点。
  - 痛点：在图像匹配任务中初始匹配中外点较多，目前难以高效快速地滤除外点。论文提出一种假设，局部图像之间满足放射变换(如果有语义分割那？局部的类别是已知的？)
  - [AdaLAM: Revisiting Handcrafted Outlier Detection](https://arxiv.org/pdf/2006.04250.pdf)
  - <https://github.com/cavalli1234/AdaLAM>

- <https://vision.uvic.ca/image-matching-challenge/submit/index.html#tac>
  - OpenCV RANSAC: 100k. Note that OpenCV fixes the number of iterations to 1k, but we have a patched version which removes this limitation. We recommend not using this option as it is not available on all the evaluation clusters and typically performs the worst.

---

## Tricks

Siamese and triplet networks

## 待记录

Global: GeM pooling [Radenovic et al., PAMI’18]
CVPR’19], or simply embeddings before classifier

patchmatch

<https://zhuanlan.zhihu.com/p/31402513>

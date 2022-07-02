---
title: 《Computational Methods of Acquisition and Processing of 3D Point Cloud Data for Construction Applications》阅读笔记
date: 2021-10-30 17:09:36
categories:	阅读笔记
tags:
  - 三维点云
---



### 	一、信息

Qian Wang1 · Yi Tan2,3  · Zhongya Mei4
Received: 12 August 2018 / Accepted: 18 February 2019 
© CIMNE, Barcelona, Spain 2019

Archives of Computational Methods in Engineering 

https://doi.org/10.1007/s11831-019-09320-4



<!--more-->

---



### 二、点云数据采集方法

1.**3D激光扫描**：

激光扫描仪通过发射激光束来测量与目标的距离并从目标中检测反射信号。主要有两种不同的测量方法，飞行时间技术和相移技术。

飞行时间技术：使用飞行时间技术的扫描仪发射激光脉冲并测量反射脉冲的行 进时间t。由于激光的速度是已知的，因此可以从行进时间推断距离测量T。

相移技术：使用相移技术的扫描仪发出幅度调制的连续波（AMCW）并测量发射和反射信号之间的相移。可以基于相位获得距离测量转换和调制连续波的波长。

使用相移原理的扫描仪比使用飞行时间技术的扫描仪的测距精度和测量速度更高 ，使用飞行时间技术的扫描仪有更大的测量范围。



2.**摄影测量**：

摄影测量定义为获得有关物理对象和环境的可靠信息的艺术、科学和技术，是一种通过记录、测量和解释摄影图像和记录的辐射图案、电磁能量和其他现象的过程。

这些图像仅使用来自不同位置的相机拍摄，从而捕获目标对象的不同部分。然后，算法将估计这些图像的相对位置，并最终将这些图像转换为3D点云。



3.**视频测量**：

类似于摄影测量，但将视频流作为输入数据而不是图像集合。视频测量可以使点云数据逐步重建，因为视频帧是连续的并且信息都建立在前一帧的基础上。

视频测量对比摄影测量的一个优点是重建过程中较少的人工干预，因为可以通过测量或跟踪连续的视频帧之间的有趣特征来实现不同图像中的目标点的搜索。



4.**RGB-D相机**：

由RGB相机和深度传感器组成，RGB相机采用RGB图像，深度传感器以每像素为基础找到深度信息。最终，通过用深度信息映射RGB图像来生成彩色点云（包含XYZ坐标和RGB颜色）。



5.**立体相机**：

立体相机是一种相机系统，具有两个或多个具有单独图像传感器的镜头。由于一个镜头相对于另一个镜头的相对位置和方向已知，因此可以基于所获取的2D图像获得3D点云数据。



---



### 三、点云数据处理过程

1.**数据清洗**：

由于实际施工现场环境的复杂性，所获取的点云数据始终包含各种类型的噪声数据，这对于预期目的而言是无用的。此外，由于特定传感器的局限性，也发生各种类型的错误数据。因此，需要用于滤除噪声的算法来滤除无用数据并仅保留感兴趣的数据。

混合像素：一种错误的激光扫描点，它发生在激光束正好落在物体边缘并一分为二的时候。激光束的两个部分落在两个不同的物体上，产生两种不同的反射激光信号。最后，激光扫描仪接收到两个反射信号并产生混合像素。

传感器环绕噪声：激光扫描数据中另一种类型的噪声数据，当距离扫描仪到目标对象的实际距离超过激光扫描仪的非模糊距离时发生。

幽灵几何：在激光扫描数据中捕获人或设备是不可避免的，这种移动物体只出现在一些扫描中， 并导致多次扫描数据不一致。



2.**数据配准**：

对准从公共坐标系中的不同位置收集的多个点云。由于大尺寸对象和遮挡物体，始终需要在不同位置进行数据采集（特别是激光扫描）以捕获目标对象的不同部分。在共享坐标系中对齐这些点云的过程称为数据配准，数据配准可以分为粗配准和精细配准。

粗配准：包括点描述符定义，关键点检测，粗配准。首先定义点描述符描述每个点，突出重要特征，然后从点云检测关键点，原始点云减少到稀疏的关键点集。最后，通过两个点云重叠区域上检测到的关键点提取变换矩阵并配准两个点云。

精细配准：最常用的方法是迭代最接近点（ICP）算法。



3.**数据分割**：

将点云数据分段为多个有意义的段或群集。这些段可以代表不同的对象或者代表可以促进点云数据的进一步处理的不同几何基元。所有方法都分为六个类别，包括基于聚类、基于边缘、基于区域、基于图形、基于模型拟合、混合的。

**基于聚类**：根据点的某些特征使用聚类算法进行分段点云数据。

**基于边缘**：基于边缘的分割算法检测3D点云数据中区域的边界以获得分段区域。

**基于区域**：通常从一个或多个种子点开始一个区域 ，然后迭代地生长区域以包括根据某些标准的相邻点。但也存在一些不需要种子点的方法。这种类别的方法称为未特性区域分割方法。

**基于图形**：在图形模型中，每个顶点对应于点云数据中的点，而图形的边缘用于连接相邻点。

**基于模型拟合的分段方法**：基于模型拟合的分段方法起源于人造物体通常可以分解成简单的几何基元，例如平面、球形、圆柱体和其他基元。因此 ，原始形状装配到点云数据中，具有相同数学表示的点被标记为一个段。在现有的基于模型拟合的分段方法中，Hough 变换（HT）和随机样本共识（RANSAC）方法是最广泛的采用。

**混合**：在混合技术中，组合两个或多于两种方法以从点云数据中检测。



**优缺点**：

基于聚类：优点：易于理解和实现。缺点：准确性问题，对数据中的噪声敏感，受邻域定义的影响。

基于边缘：优点：快速分割。缺点：精度问题，对噪声敏感，对不均匀密度的点云敏感。

基于区域：优点：处理噪音更准确。缺点：在分割和确定区域边界的准确性上有问题。

基于图形：优点：处理不均匀密度或噪音复杂点云数据有更好的性能。缺点：无法实时处理，并且需要培训或其他系统来协助。

基于模型拟合的分段方法：

1.Hough变换（HT）：优点：快速且稳健地对抗异常值。缺点：较慢，对分段参数更敏感；

2.随机样本共识（RANSAC）：优点：快速且稳健地对抗异常值，能够在合理短的时间段内处理大量点云数据。缺点：在处理不同点云源时的数据准确性。

混合：优点：多种方法的优点使结果更准确。缺点：包含所选方法的所有缺点



4.**对象识别**：

识别点云数据中的某些对象。例如，要从点云数据中创建语义丰富的建筑物，建筑元素，如墙壁，窗口和门，必须从点云数据中识别。

**几何形状描述符**：不同的对象实例或对象类通常具有不同的几何形状。因此，几何形状描述符描述了基于某些几何特征的对象形状，通常用于识别某个对象实例或对象类。通常以三个步骤执行：离线库生成，在线搜索，最终验证。

**硬编码知识**：对于墙壁、屋顶和地板等主要建筑部件，它们通常具有不同的几何特性，使用硬编码知识将这些段分类为对象类法是一种简单有效的识别算法。但是，它仅适用于在硬编码知识中定义的约束情况，难以将该方法扩展到具有更复杂几何形状的其他物体类别。

**监督学习**：使用机器学习算法来训练可以将点云数据分类为对象类的分类器。基于学习的物体识别有两种策略，基于点的分类，每个点使用点的本地特征分别分类为对象类。对于基于段的分类，点云数据通常使用数据分段算法划分为有意义的段，并且每个数据段都使用每个段的特征分类为对象类。

**BIM‑vs‑Scan**：当整个结构建筑信息模型（Building Information Modeling）可用时，普遍采用更简单的基于BIM‑vs‑Scan的对象识别方法。





---



### 四、参考文献（前20）

1.Bradley C, Vickers G, Milroy M (1994) Reverse engineering of  quadric surfaces employing threedimensional laser scanning.  Proc Inst Mech Eng Part B J Eng Manuf 208(1):21–28

2.Son S, Park H, Lee KH (2002) Automated laser scanning system for reverse engineering and inspection. Int J Mach Tools  Manuf 42(8):889–897

3.Varady T, Martin RR, Cox J (1997) Reverse engineering  of geometric models—an introduction. Comput Aided Des  29(4):255–268

4.Yu X, Hyyppä J, Kaartinen H, Maltamo M (2004) Automatic detection of harvested trees and determination of forest  growth using airborne laser scanning. Remote Sens Environ  90(4):451–462

5.Gaveau DL, Hill RA (2003) Quantifying canopy height underestimation by laser pulse penetration in small-footprint airborne laser scanning data. Can J Remote Sens 29(5):650–657

6.Hollaus M, Wagner W, Maier B, Schadauer K (2007) Airborne  laser scanning of forest stem volume in a mountainous environment. Sensors 7(8):1559–1577

7.Rosser N, Petley D, Lim M, Dunning S, Allison R (2005) Terrestrial laser scanning for monitoring the process of hard rock  coastal clif erosion. Q J Eng Geol Hydrogeol 38(4):363–375

8.Heritage GL, Milan DJ (2009) Terrestrial laser scanning  of grain roughness in a gravel-bed river. Geomorphology  113(1–2):4–11

9.Liu X (2008) Airborne LiDAR for DEM generation: some critical issues. Prog Phys Geogr 32(1):31–49

10.Fröhlich C, Mettenleiter M (2004) Terrestrial laser scanning— new perspectives in 3D surveying. Int Arch Photogramm  Remote Sens Spat Inf Sci 36(Part 8):W2

11.Olsen MJ, Kuester F, Chang BJ, Hutchinson TC (2009) Terrestrial laser scanning-based structural damage assessment. J  Comput Civ Eng 24(3):264–272

12.Zhang C, Arditi D (2013) Automated progress control using  laser scanning technology. Autom Constr 36:108–116

13.Leite F, Cho Y, Behzadan AH, Lee S, Choe S, Fang Y, Akhavian R, Hwang S (2016) Visualization, information modeling,  and simulation: grand challenges in the construction industry.  J Comput Civ Eng 30(6):04016035

14.Kwon S, Lee M, Lee M, Lee S, Lee J (2013) Development of  optimized point cloud merging algorithms for accurate processing to create earthwork site models. Autom Constr 35:618–624

15.Bosche F, Haas CT, Akinci B (2009) Automated recognition  of 3D CAD objects in site laser scans for project 3D status  visualization and performance control. J Comput Civ Eng  23(6):311–318

16.Wang C, Cho YK (2015) Smart scanning and near real-time  3D surface modeling of dynamic construction equipment from  a point cloud. Autom Constr 49:239–249

17.Tang P, Huber D, Akinci B, Lipman R, Lytle A (2010) Automatic reconstruction of as-built building information models  from laser-scanned point clouds: a review of related techniques. Autom Constr 19(7):829–843

18.Wang Q, Kim M-K, Cheng JC, Sohn H (2016) Automated quality  assessment of precast concrete elements with geometry irregularities using terrestrial laser scanning. Autom Constr 68:170–182

19.Volk R, Stengel J, Schultmann F (2014) Building information  modeling (BIM) for existing buildings—literature review and  future needs. Autom Constr 38:109–127

20.Kim M-K, Cheng JC, Sohn H, Chang C-C (2015) A framework for dimensional and surface quality assessment of precast  concrete elements using BIM and 3D laser scanning. Autom  Constr 49:225-238


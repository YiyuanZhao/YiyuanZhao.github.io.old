---
layout: project
title:  基于MATLAB的数据可视化App
code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/VASP_Plotter"
---

# 背景
[使用DFT对VSe2的研究](/research/VSe2_Ab_initio)产生了大量的基于VASP软件包的数据处理需求，每种计算构型需要处理MB级的数据。流程涉及数据的预处理、分割、可视化三个阶段。由于不同计算的数据特征不同，需要采用不同的区域进行可视化。除了数据可视化外，还需要对计算数据进行格式处理，进行进一步的模型计算。项目主要解决下列问题：
 - 多批次相同数据组织格式的数据可视化。
 - 组织后续模型计算需要的数据结构。

# 解决方案
读取VASP的计算文件，针对不同的数据分别在不同图表中进行可视化，并给出导出接口。

对于DFT计算，有多种需要可视化的计算结果，简要介绍如下：
 - 总态密度(total density of states, tDOS)：电子态在给定能量区域内的分布，在所有能量区域的积分为总电子数。
 - 能带结构(band structure)：在k空间内沿着特定路径上，电子的E-k(色散)关系，能带结构决定了材料的诸多物理性质(如导电性)。

![tdos](/image/app_tdos.gif)

 - 分波投影态密度(projected density of states)：电子态在不同轨道(s,p,d)上投影在给定能量区域内的分布，显示了电子的轨道分布。

![tdos](/image/app_pdos.gif)

 - Fat Band：包含k空间内电子贡献的能带结构，可以给出能带结构中每个轨道投影的贡献。

![tdos](/image/app_fatband.gif)

 - 瓦尼尔拟合(Wannier90 Fit)：在紧束缚模型下画出的能带结构与DFT能带结构的结果比较。

![tdos](/image/app_wannierFit.gif)

 - 3D 能带结构：在整个k空间内的色散关系。

![tdos](/image/app_3dBandStructure.gif)

# 源代码
参见页面顶部[View the project code on GitHub]()按钮。
# 演示Demo
Notes: 需要MATLAB R2020a版本/Windows

Powershell:
```shell
git clone git@github.com:YiyuanZhao/ProfileProjectSourceCode.git
cd ./ProfileProjectSourceCode/VASP_Plotter/src
matlab .
```
接下来在打开的MATLAB中输入：

MATLAB Terminal
```matlab
VaspPlotter
```
在新打开的APP Designer界面中按``F5``启动App。

在打开的界面中选择``Select Folder``，选择包含数据文件的文件夹，等待读入后即可开始可视化过程。

![tdos](/image/app_init.gif)

# 技术栈
MATLAB

* * *
### [back](/)
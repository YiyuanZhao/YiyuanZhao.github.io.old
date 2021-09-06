---
layout: project
title:  基于Power BI的数据可视化
code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/VASP_Plotter"
---

# 背景
项目[基于MATLAB的数据可视化App](./matlabDataVisualization)解决了基本的数据可视化需求。但对于各种构型(Configuration)的计算，有时需要根据研究的需求进行对比，以下是我研究中遇到的实际层次结构：

── material
   └── Magneti_Configuration
       └── layerDistance
           └── AtomNumber
               └── dosData

每个层次结构包含的数据集大小为：

| 类型       | 数据条数 |
|------------|----------|
| 材料       | 3        |
| 磁性构型   | 3        |
| 层间距     | >9       |
| 原子数     | 42       |
| 态密度数据 | 2000     |

总的数据条数则为所有层次的乘积，约为680万条，实际的数据量接近千万条。在研究中有可能进行任意两个数据集的比较(e.g. 比较材料A在铁磁、层间距条件1，第1个原子与材料2限定条件下的比较)，此时根据需求手动处理数据显然不现实。除此以外，不同的数据集可能会发生更新(例如相同参数的计算得到了更精确的结果)。因此在研究中产生了下面的需求：

 - 千万条数据规模的存档与查询
 - 数据集的实时更新
 - 任意选择的图表可视化

# 解决方案
为了解决上面的问题，采取数据初筛后使用MySQL存储，使用Power BI可视化的方案。

 - 由于不同构型的态密度数据源不包含构型的信息，使用c++读入原始数据后，添加相关标签，为导入MySQL准备，数据输出为文件格式。
 - 建立database和table，使用读入文件的方式导入数据库，并建立索引加快检索速度。
 - 在power BI中建立数据库查询，设计展示方式并发布。

# 源代码
powerbi
# 演示Demo
环境需求：Windows，g++

powershell:
```powershell
git clone git@github.com:YiyuanZhao/ProfileProjectSourceCode.git
cd ./ProfileProjectSourceCode/powerBIAutomation/src
g++ -g processDoscar.cpp -o processDoscar.exe -Wall -static-libgcc
.\processDoscar.exe "VSe2" "Primary" "3.12" "NM"
mysql mysql  -u <USERNAME> --local-infile=1 -p
```
在MySQL中建立数据库，表格和导入数据：

sql:
```sql
create database dos;
use dos;
create table `nm_DOSCAR` (
    `material`          varchar(30) not null,
    `cellType`          varchar(30) not null,
    `layerDistance`     float not null,
    `magConfiguration`  varchar(20) not null,
    `atomIdx`           int not null,
    `energy`            float not null,
    `s`                 double not null,
    `p_y`               double not null,
    `p_z`               double not null,
    `p_x`               double not null,
    `d_xy`              double not null,
    `d_yz`              double not null,
    `d_x2_r2`           double not null,
    `d_xz`              double not null,
    `d_x2_y2`           double not null,
    `tot`               double not null,
    `integrated`        double not null,
    constraint Doscar_id primary key (`material`, `cellType`, `layerDistance`, `magConfiguration`, `atomIdx`, `energy`),
    index energyIdx(energy)
)
engine = innodb default charset = utf8;

load data local infile 'processedDoscar.dat' replace into table nm_DOSCAR
character set utf8
fields terminated by ' '
lines terminated by '\r\n';
```

通过Power BI的database connector，链接到MySQL数据库，选择合适的数据，制作dashboard即可完成数据的可视化操作。
* * *

# 技术栈
C++，MySQL，Power BI
### [back](/)
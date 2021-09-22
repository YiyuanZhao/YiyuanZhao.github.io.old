---
layout: project
title:  Linux自动化shell脚本
code_url: "https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/shellScriptAutomation"
---

# 背景
在研究中，遇到了大量机械重复的数据处理工作、定时监控计算情况等需求，因此可以采用shell脚本的方式减少重复工作。

# 源代码
可以从此处下载[源代码](https://github.com/YiyuanZhao/ProfileProjectSourceCode/tree/main/shellScriptAutomation)和演示集。

# 演示Demo
环境要求：Linux  
你可以使用以下命令克隆仓库：
```shell
git clone git@github.com:YiyuanZhao/ProfileProjectSourceCode.git
repositoryRoot=$(pwd)
```

## 计算弛豫前后的晶格变化
DFT计算在弛豫晶格时，晶格常数和本身的原子位置都会发生变化，如果变化非常剧烈，就有可能计算结果不可信，``calcDiff.sh``就实现了原生shell计算位置变化百分比的脚本。

```shell
cd $repositoryRoot/ProfileProjectSourceCode/shellScriptAutomation/calculateDifference
chmod +x calcDiff.sh
bash calcDiff.sh
```
## 汇总计算特征信息
DFT计算会给出诸多信息，需要从中提取重要的信息并进行汇总，同时探测计算中是否发生异常/错误。

```shell
cd $repositoryRoot/ProfileProjectSourceCode/shellScriptAutomation/dataSummary
chmod +x dataSummary.sh
bash dataSummary.sh
```

你也可以将最后的命令替换为``bash dataSummary.sh -xml``，将会把所有的信息汇总到一个xml中，适合程序化的进一步处理。

## 集群任务的智能提交
计算集群采用SGE调度系统，使用bash脚本来控制提交的任务资源调度。例如可以指定CPU核数、计算节点等。提交任务可能出现下面的情况：

 - 指定计算节点和CPU数量
 - 仅指定CPU数量

方案1可能会因为所指定的计算节点已经被占用而提交失败，方案2则有可能出现跨节点的任务分配，然而跨界点的计算因为网络延迟的原因，计算效率底下。  
因此，可以使用脚本来探测可用的计算节点，自动生成任务脚本并提交，免去了手动更改任务脚本的时间，这对于大规模计算来讲(提交任务数>15)，可以省下巨大的时间。

脚本定制了不同参数(-d: 运行完成后删除提交脚本，[0-9]：指定CPU数量，-b：建立任务脚本后不提交，-S：建立监视，每隔15分钟扫描一次，若任务完成则向指定邮箱发送邮件，-pe：并行模式)用来定制化的场景。  
由于环境难以搭建，此脚本不提供运行demo，只在工程中给出源代码。

* * *

# 技术栈
shell，powershell

# shell脚本中涉及的命令TOP20
迄今为止，在自动化方向，已经设计了30+的脚本，下面给出在脚本文件夹下，对所有``.sh``文件中，所有命令频次汇总（不完全统计）。在文件下运行下列命令：

```shell
grep -v '#' ./*.sh| sed -E "s/ +/\n/g" | grep -E "^[a-z]+$" | sort | uniq -c | sort -rg | head -20
```
其中``grep -v '#'``筛选了所有的非注释行，``sed -E "s/ +/\n/g"``以一个（或多个）空格为单位进行语义分割，替换为换行符，方便进一步处理，``grep -E "^[a-z]+$"``剔除了定义的变量(变量定义中会出现``=``而不会被选中)、字符串变量(不以字母开始)、文件路径(路径中含有``/``而被剔除)，``sort``对所有行按字母顺序进行初步排序，``uniq -c``统计每个词的出现频率，``sort -rg``将字符串视作数字(第一部分是纯数字)并降序排序，``head -20``筛选出排序后的前20项。

命令运行的结果如下：
```shell
     88 awk
     73 grep
     68 echo
     36 shift
     29 sed
     27 tail
     27 in
     26 then
     24 if
     24 fi
     20 else
     18 nl
     13 print
     13 cd
     12 esac
     12 cat
     12 case
     11 source
      9 exit
      7 not
```
### [back](/)
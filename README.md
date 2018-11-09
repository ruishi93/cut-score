## singsound cut score

**背景 **：CAT量尺中的DSR题目采用的是CS（CMU audio analyzer）的评分结果，分值为0—100，以58分作为cut score，将作答反应转换为0/1，以此放到IRT中计算题目难度。现在打分引擎跟换为singsound，需要对singsound的分数找一个同样的cut score，对接到原来的CAT量尺上，使得同样的题目估计出的难度飘移最小。



**预期**：基于之前的分析，singsound的评分结果普遍高于cs评分，因此预估singsound的cut score 大于 58分。



**实现过程**：

- 方法1——计算二值序列的相似性：基于一批学生在CS和singsound上的打分（50w+条做答数据），以58为CS的cut score，得到一个【0/1】序列，singsound的cut score遍历【20—75】之间的数，得到51个【0/1】序列，计算这51个序列与CS【0/1】序列的相似性（MSE）。
  - 过滤音质不好的记录：cut score为31
  - 未过滤音质：cut score为31

- 方法2——从效果上难度漂移最小：基于以上的的【0/1】序列，转化为【user-item】作答矩阵，送入IRT估计题目难度，以MSE的方式计算难度飘移动。
  - 过滤音质不好的记录：cut score为38
  - 未过滤音质：cut score为32

  ————注：音质过滤规则为，基于singsound返回的音质标签（infoID）和分数，过滤掉音质不好（infoID非0）且评分低于60分的记录

  

**结果**：比较出乎意料，一个原因是CS本身不是很准，也可以验证CS和singsound本身打分的pattern不一样。



**文档说明**：

[cut_score.ipynb](./cut_score.ipynb) ：在jupyter中，方法1的实现，方法2送入R估计的数据准备，方法2结果计算；

[cut_score.R](./cut_score.R) : 在R中方法2的实现，fix非DSR题目的难度，估计各个cut score下DSR题目的难度

data（file）：

 * [yuting__Resetting_dsr_cutpoint_with_Singsound](./data/yuting__Resetting_dsr_cutpoint_with_Singsound)：yuting给的数据
* [data_final_with_ss.csv](./data/data_final_with_ss.csv)(音质好的所有做题记录)/ [data_final_with_ss_all](./data/data_final_with_ss_all.csv)(所有数据，未过滤音质)：基于yuting文档中的[resetting_dsr_cutscore_assumption_32.Rmd](./data/yuting__Resetting_dsr_cutpoint_with_Singsound/resetting_dsr_cutscore_assumption_32.Rmd)生成——传入jupyter中
* [good_cs.csv](./data/good_cs.csv) / [good_ss.csv](./data/good_ss,csv) : 音质好的记录生成的CS和singsound的作答矩阵——jupyter中生成，导入R中
* [all_cs.csv](./data/all_cs.csv) / [all_ss.csv](./data/all_cs.csv) : 未过滤音质的记录生成的CS和singsound的作答矩阵——jupyter中生成，导入R中
* [fix_diff.csv](./data/fix_diff.csv) / [all_fix_diff.csv](./data/all_fix_diff)：音质好的记录生成的非DSR题目，需要fix的难度——jupyter中生成，导入R中










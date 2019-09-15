# 评价标准

## jaccard相似度

1. 两个集合交集的大小与并集的大小之间的比值。jaccard相似度的缺点是只适用于二元数据的集合。

   例子：若两个人都有属性集合（糖尿病，心脏病，精神病），是否得病决定该属性为1还是为0。则(1,0,1)和(0,0,0)的jaccard相似度为2/3，因为这两个人只有心脏病的属性是相同的。

2. Tanomoto相似度与jaccard相似度很像，只是Tanomoto计算的是bitmap的相似度，bitmap的每个像素相当于二元属性。

   我认为物体分割算法的评判标准jaccard有点像Tanomoto相似度。

# batch_size,iteration,epoch

例如：训练集有1000个样本，batch_size为10，那么训练完整个样本需要100个iteration和1个epoch。
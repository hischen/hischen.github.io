---
layout: post
title: "KDD Cup 2020 Challenges for Modern E-Commerce Platform"
subtitle: "Multimodalities Recall "
date:   2019-12-12 12:00:00
author: "hischen"
header-img: "img/post-bg-rwd.jpg"
header-mask: 0.3
catalog:    true
tags:
  - KDD Cup 2020
  - Modern E-Commerce Platform
  - CCF BDCI
  - Multimodalities Recall
---

最近几个月参加了由KDD2020、阿里巴巴集团等举办的天池大数据竞赛算法大赛KDD Cup 2020 Challenges for Modern E-Commerce Platform: Multimodalities Recall 赛题，在参赛的1433支队伍里获得 16名的成绩，这里简单复盘一下我们的方案。

## 赛题任务
For this competition, we have prepared the real-scenario multimodal data from the mobile Taobao, one of the largest e-commerce platforms. The dataset consists of Taobao search queries and product image features, which is organized into a query-based multimodal retrieval task.

Given a search query in natural language form, the participating teams are required to implement a model to rank a collection of candidate products based on their image features. Most of these queries are noun phrases searching for products with specific characteristics. The images of the candidate products are provided by the sellers displaying the product features. Candidate products most relevant to the query are regarded as the ground truth of the query, which are expected to be top-ranked by the participating models. We demonstrate an example query as follows:

It should be noted that we do not provide product titles and description texts in this task, since we focus on evaluating the model's ability to conduct multimodal understanding between vision and language rather than text matching.

## 数据集

The dataset download application link will be available for contestants after the registration for the competition.

Training set
The whole training set consists of ~3M pairs of query and ground-truth product image features. These sample pairs can be taken as the positive examples to train your retrieval model. For each product image, we release the features, locations and classification labels of each detected object. (To avoid the copyright issues, we only release around 9k raw product images in the valid set in this competition for reference.)

The whole training records can be accessed in train.tsv (unzipped from multimodal_train.zip). Before the whole training set is officially released, we provide a sampled set containing 10k pairs, which can be accessed in train.sample.tsv (unzipped from multimodal_train_sampleset.zip). Each row in these tsv files represents a single sample pair. These tables have 9 columns separated by tabs:

product_id: the index of the product
image_h: the height of the product image
image_w: the width of the product image
num_boxes: the number of detected object bounding boxes for the image
boxes: a [num_boxes, 4] shaped 2-D array specifying the location of each object bounding box in the image. For each row of this array, the 4 values specify the top/left/bottom/right coordinates of the corresponding box. This array is encoded into a base64 string, you can recover it into a numpy-array using np.frombuffer(base64.b64decode(THIS_ENCODED_BASE64STRING), dtype=np.float32).reshape(NUM_BOXES, 4) in python
features: a [num_boxes, 2048] shaped 2-D array specifying the 2048-dimension feature computed by the detector of each object bounding box in the image. This array is also encoded into a base64 string, you can recover it into a numpy-array using np.frombuffer(base64.b64decode(THIS_ENCODED_BASE64STRING), dtype=np.float32).reshape(NUM_BOXES, 2048) in python
class_labels: a [num_boxes] shaped 1-D array specifying the category-id of each object. There are 33 object classification categories in this dataset. The correspondence between each category-id and its category name is given in multimodal_labels.txt. The array is also encoded and can be recovered by np.frombuffer(base64.b64decode(THIS_ENCODED_BASE64STRING), dtype=np.int64).reshape(NUM_BOXES)
query: a natural language query which matches the corresponding product
query_id: the index of the query
Valid & testing set
The valid set consists of around 500 queries and the testing set A and B both consist of around 1k queries. For each query in these sets, we prepare a candidate pool of around 30 products for ranking. Each candidate product image is processed in the same way as the training set.

Following the competition schedule, the records of the valid set, testing set A and B will be given in valid.tsv, testA.tsv and testB.tsv respectively (unzipped from multimodal_valid.zip, multimodal_testA.zip and multimodal_testB.zip). In these tables, each row represents a candidate product-query pair, which is also specified by 9 columns in the same schema as the training set (except for the product in the pair is a candidate rather than a ground-truth). The query-ids in the valid and testing sets are independent from the training set and each other. However, the product-ids share the same code system as the training set, which means the same product-id in the valid/testing set and training set refers to the same product.

We provide the ground-truth products for each evaluation query of the valid set in valid_answer.json. The ground-truth products for each evaluation query are not ranked in order. This json file is in the following format:

{
  "query-id":
  [
   "ground-truth product-id 1",
   "ground-truth product-id 2",
   ...,
   "ground-truth product-id n"
  ]
}
The ground-truth products for the testing set A & B will not be released.

As mentioned above, we provide 9k raw product images in the valid set for reference, which can be accessed from multimodal_validpics.zip.

## 评分标准

We will compute the nDCG@5 on the testing dataset for each participating model, which evaluate the correspondence between the retrieved products and the ground truth. The final ranks of each participating team will be determined by the nDCG@5 on the testing set B.

## 方案一
模型结构：  
*query侧：*
1.	query分词后通过双向gru输出具有上下文特征的embedding
2.	后接一层全连接映射到固定的维度
3.	再通过一层 share dnn layer 尽可能输出到一定的特征空间

*img侧：*
1.	每个box的特征通过双向gru输出具有上下文特征的embedding
2.	后接一层全连接映射到固定的维度
3.	再通过一层 share dnn layer 尽可能输出到一定的特征空间

**双向 match_attention ：*

img2text attention

1.	将text每一个word的embedding与img全部feature的embedding做內积
2.	通过softmax得到注意力分数
3.	根据分数对 img 各个 feature 对应的 embedding 加权求和，得到img2text embedding
4.	将text word 的embedding与img2text的embedding计算內积作为logit

**text2img attention*

1.	将img每一个feature的embedding与text全部word的embedding做內积
2.	通过softmax得到注意力分数
3.	根据分数对 text 各个 word 对应的 embedding 加权求和，得到 text2img embedding
4.	将img feature 的 embedding 与 text2img 的embedding计算內积作为logit

两个模型融合得到最终模型

## 方案二

特征处理：
query: 将query用bert的tokenizer分词后，使用fasttxt和glove各300维的词向量拼接起来作为query的表示，然后通过双向LSTM输出具有上下文特征的embedding。再将得到的embedding与bert对query的编码向量拼接得到最终对query的表示。
Image: 将image中每个box对应的label用拼接得来的600维的词向量来表示，然后依次进行conv1d，maxpooling1d，再与对应的image 2048维的feature拼接起来。将拼接得到的特征与位置特征（top/image_h, left/image_w, bottom/image_h, right/image_w, box_area/(image_w* image_w)）相加，再通过双向LSTM输出具有上下文特征的特征，得到最终对于image的表示。
模型：
1.	将得到的query表示与得到的image表示中第一个box的特征拼接，用BCE loss分类。
2.	将得到的query表示与每一个box的表示做scaled-dot-product attention，然后通过softmax进行归一化得到注意力分数，再根据分数对image的表示中各个box的feature加权求和，得到最终image的特征。最后将query特征和image特征拼接起来用BCE loss分类。

## 方案三



整体方案借鉴了UNITER，采用了预训练+微调的两个步骤。
预训练阶段：
1.	预处理
a)	与bert类似，采用wordpiece tokenier进行分词，以概率随机屏蔽一定单词，并将图片特征一起纳入到屏蔽范围。不同的是，在屏蔽单词后，将不再屏蔽图片特征；图片特征仅仅在没有屏蔽单词的情况下完成。
b)	Bert配置没有采用预训练参数，完全从零开始训练；
2.	预训练
a)	预训练的目标函数包括：
i.	重构图像特征的 L2 loss
ii.	分类特征类别的损失：交叉熵损失
iii.	分类[MASK]词语的损失: 交叉熵损失
b)	预训练采用 linear warmup的方式，Learning rate 1e-4, Batch size=256, 一共训练20 Epoch后结束。
微调阶段
1.	预处理
a)	我们计算了不同query之间的相似性，根据相似度的高低排序，取Top-k生成了Hard Negtive Samples， 并以正负样本比例为 1 : K 的比例进行微调。
2.	训练
a)	微调使用的损失函数为 Triplet Loss, Margin。 
b)	Batch size = 32, Learning rate =1e-5 , 一共训练5个epoch后结束。

模型融合
	训练了多个不同类型的bert，将每个模型最后的输出结果直接相加作为最终结果。这里用到的模型有bert-6，bert-9,  Roberta-6, Roberta-9. 这里bert-6代表6层的bert莫循。


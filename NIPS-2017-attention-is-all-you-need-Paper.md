<img src="C:\Users\shanj\AppData\Roaming\Typora\typora-user-images\image-20201123103626230.png" alt="image-20201123103626230" style="zoom: 100%;" />

## Attention is all you need

### abstract

> The dominant sequence transduction models are based on complex recurrent or convolutional neural networks that include an encoder and a decoder. The best performing models also connect the encoder and decoder through an attention mechanism. We propose a new simple network architecture, ==**the Transformer, based solely on attention mechanisms, dispensing with recurrence and convolutions entirely.**== Experiments on two machine translation tasks show these models to be ==**superior in quality**== while being ==**more parallelizable**== and requiring significantly ==**less time**== to train. Our model achieves 28.4 BLEU [^BLEU]on the **WMT 2014 English-to-German translation task**, improving over the existing best results, including ensembles, by **over 2 BLEU**. On the **WMT 2014 English-to-French translation task**, our model establishes a new single-model state-of-the-art BLEU score of 41.0 after training for 3.5 days on eight GPUs, a small fraction of the training costs of the best models from the literature.

Transformer,完全基于注意力机制，完全省去循环和卷积

dataset： WMT 2014 English-German dataset（consisting of about 4.5 million sentence pairs），WMT 2014 English-French dataset（consisting of 36M sentences）

### 1. Introduction

recurrent models 存在一些限制sequential computation

attention mechanisms 允许对依赖关系建模而不考虑输入或输出序列中的距离

transformer 这样模型架构，可以描述输入和输出之间的全局依赖关系

### 2. Background

卷积神经网络复杂度太高，很难学习长距离之间的依赖关系，In the Transformer this is reduced to a constant number of operations, albeit at the cost of reduced effective resolution due to averaging attention-weighted positions, an effect we counteract with **Multi-Head Attention**（多头注意力）

**self-attention**（intra-attention）将单个序列不同位置联系起来以计算序列的表示

**end-to-end memory networks**（端到端记忆网络） based on a recurrent attention mechanism instead of sequence-aligned recurrence and have been shown to perform well on simple-language question answering and language modeling tasks.

### 3. Model Architecture

##### 3.1 Encoder And Decoder  Stacks

<img src="C:\Users\shanj\AppData\Roaming\Typora\typora-user-images\image-20201124164347512.png" alt="image-20201124164347512"  />

##### 3.2 Attention

输入键值对（K,V）查询向量Q

additive attention:
$$
Attention(Q,K,V)=softmax(v^T\tanh(WK^T+UQ^T))V
$$
dot-product attention:
$$
Attention(Q,K,V)=softmax(QK^T)V
$$
scaled dot-product attention:
$$
Attention(Q,K,V)=softmax(\frac{QK^T}{\sqrt{d_k}})V
$$
dot-product attention和additive attention，两者理论上复杂度相似，**dot-product attention is much faster and more space-efficient in practice**,since it can be implemented using highly optimized matrix multiplication code. 点积模型在实现上可以更好地利用矩阵相乘，计算效率更高。当输入向量的维度比较高时，点积模型的值会比较大，导致softmax函数的梯度很小，通过缩放点积模型来解决。

**multi-head attention**，用多个查询向量q并行计算，每个注意力关注输入信息的不同部分。单个注意力的平均操作会抑制这一点

==The Transformer uses multi-head attention in three different ways:==

1 in "encoder-decoder attention" layer,==查询向量q来自前一个decoder layer，k和v来自当前encoder的输出==。This allows every position in the decoder to attend over all positions in the input sequence（==编码器的每个位置都可以关注到输入序列的所有位置==）

2 encoder中含有自注意力层（self-attention layers），在自注意力层中，==k,v,q都来自encoder的前一层输出==。Each position in the encoder can attend to all positions in the previous layer of the encoder.（==encoder的每一个位置都可以关注到encoder前一层中的所有位置==）

3 decoder中，self-attention layers in the decoder allow each position in the decoder to attend to all positions in the decoder up to and including that position.==self-attention layers 使得decoder中的每个位置都可以关注到所有位置包括当前的==

##### 3.3 Position-wise Feed-Forward Networks

each of the layers in our encoder and decoder contains a fully connected feed-forward network, which is applied to each position separately and identically. encoder和decoder之间包含一个全连接层

##### 3.4 Embeddings and Softmax

use learned embeddings to convert the input tokens and output tokens to vectors of dimension $\ d_{model}$ （输入输出token转换成$\ d_{model}$维度的向量）

use the usual learned linear transformation and softmax function to convert the decoder output to predicted next-token probabilities.（通过一些线性变换和softmax函数把decoder output转换为预测下一个token的概率）

share the same weight matrix between the two embedding layers and the pre-softmax
linear transformation. In the embedding layers, we multiply those weights by $\ \sqrt{d_{model}}$ （两个embedding layers之间的权重和 pre-softmax linear transformation的权重是共享的）

##### 3.5 Positional Encoding

模型中没有使用recurrence和convolution，通过加入相对或者绝对位置信息来让模型利用序列的顺序，positional encoding加在encoder和decoder stacks底部（加在开始的时候）

### 4 Why Self-Attention

把自注意力层的几个方面与hidden layer in typical sequence transduction encoder or decoder比较，使用自注意力有三个考虑：

一是每层的计算复杂度，二是可以并行化的计算量，以需要的最小sequential operations衡量，第三是网络中长程依赖的路径长度（==Learning long-range dependencies is a key challenge in many sequence transduction tasks. One key factor affecting the ability to learn such dependencies is the length of the paths forward and backward signals have to traverse in the network.==）



[^BLEU]: 一种机器翻译的评价指标，用于评估模型生成的句子(candidate)和实际句子(reference)的差异的指标


---
title: 'TRANS->深度学习中的那些重要想法——历史简要回顾'
description: '回顾深度学习中经住时间考验的想法，涵盖大量理解现代深度学习研究的必要知识。如果刚入门深度学习，这是个不错的起点。'
toc: true
hide: false
layout: post
categories: [translations, deeplearning]
author: Denny Britz
---

翻译自[原文](https://dennybritz.com/blog/deep-learning-most-important-ideas/)

深度学习这个领域瞬息，大量研究论文和想法会让人不知所措。即便是经验老道的研究员也很难跟公关(company PR)说真正的突破是什么。本文的目的是回顾那些**经得住时间考验**的想法，且这些想法也许更加靠谱。这些想法以及其发展被反复使用，堪称劳模。

如果你是今天才开始接触深度学习，理解和实现这当中每一项技术会给你带来很好的基础，去理解近期的研究以及着手于自己的项目。这是最好的入门方式。而且按历史先后顺序来学习，能更好地理解当前技术出自哪里、为何出现。**换言之，这里会列出尽量少的且必要的知识，去理解现代深度学习的研究。**

深度学习的一个独特之处是它的应用（计算机视觉、自然语言、语音处理、强化学习等）都共同涉及到大多数技术，就像 CV（计算机视觉）领域的专家可以很快地着手 NLP（自然语言处理）研究。具体的网络结构会有所不同，但概念、方法，甚至代码都是几近相同的。这里会尝试列出不同领域的想法，但关于这个需要注意几点：

- 本文目标不是剖析这些技术，也不会给出代码。冗长复杂的论文浓缩成一段并不是件简单的事。相反，这里会给出每个技术的概况、它的历史上下文，和论文及其实现的链接。这里*极力*推荐用`PyTorch`从零开始复现这些论文的成果，而不是在已有代码基础上改或者采用高级库。

- 这个列表是基于作者的知识，有很多令人兴奋的子领域，作者尚未接触。本文会集中在上面提到的那些领域，毕竟大多数人也是更加关注这些。

- 本文只讨论一些官方/半官方的可用的开源实现。有些研究很难复现，因为涉及很大的工程挑战，比如`DeepMind`的`AlphaGo`和`OpenAI`的`Dota2 AI`。

- 有些技术会在同一时间内公布，而这里不会一一列出。全面不是本文的目的，照顾新手才是，所以要挑那些涵盖域较广的想法。比如，目前有上百种 GAN 的变体，但要理解 GAN 的概念，随便学哪个都行。

## 2012 - 用 AlexNet 和 Dropout 处理 ImageNet

**论文:**

- [ImageNet Classification with Deep Convolutional Neural Networks (2012)](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks)

- [Improving neural networks by preventing co-adaptation of feature detectors (2012)](https://arxiv.org/abs/1207.0580)

- [One weird trick for parallelizing convolutional neural networks (2014)](https://arxiv.org/abs/1404.5997)

**实现：**

- [AlexNet in PyTorch](https://pytorch.org/hub/pytorch_vision_alexnet)

- [AlexNet in TensorFlow](https://github.com/tensorflow/models/blob/master/research/slim/nets/alexnet.py)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/alexnet-full.png 'Source: https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks')

AlexNet 被认为近期深度学习和人工智能爆发时期的代表，它是之前出自 Yann LeCun 之手的 LeNet 基础上发展来的深度卷积神经网络。得益于 GPU 的强大算力和先进的算法，Alexnet 在 ImageNet 数据集分类问题上击败了当时的所有方法，证明了神经网络确实可行。AlexNet 也是第一个使用 Dropout 的网络，而如今 Dropout 成为了提升深度学习模型泛化能力的关键组件。

AlexNet 的结构、卷积层序列、ReLU 激活函数和最大池化层，成为了后来 CV 构架拓展和建立的公认标准。如今像 PyTorch 的软件库非常强大，加上对比近期的网络结构，AlexNet 显然更加简单，所以可以通过几行代码就可以实现 AlexNet。但值得注意的是，包括上面链接，很多 AlexNet 的实现多多少少会有些区别，具体可以看[One weird trick for parallelizing convolutional neural networks](https://arxiv.org/abs/1404.5997)。

## 2013 - 用深度强化学习玩雅达利

**论文：**

- [Playing Atari with Deep Reinforcement Learning (2013)](https://arxiv.org/abs/1312.5602)

**实现：**

- [DQN in PyTorch](https://pytorch.org/tutorials/intermediate/reinforcement_q_learning.html)

- [DQN in TensorFlow](https://www.tensorflow.org/agents/tutorials/1_dqn_tutorial)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/deep-q-learning-value.png 'Source: https://deepmind.com/research/publications/human-level-control-through-deep-reinforcement-learning')

基于近期图像识别的重大突破和显卡的快速发展，DeepMind 的一个团队设法训练一个网络，通过输入原生像素来玩雅达利游戏。更厉害的是，在没有告知任何游戏规则的前提下，用*相同*的神经网络结构去学习 7 种不同的游戏，充分展现了这方法的泛化能力。

强化学习不同于像图像分类的监督学习，前者需要一个代理在一定时间步骤内获得最大奖励（比如赢得比赛），而不是预测一个标签。因为代理直接与环境交互，且每个动作都会影响环境，所以训练样本不是独立同分布。这使得许多机器学习模型的训练不稳定，这可以通过经验回放（experience replay）解决。

尽管这方面没有算法上特别瞩目的革新，但这项研究巧妙地将现有的技术结合起来，包括 GPU 上训练的卷积神经网络和经验回访，加上一点数据处理的技巧，就能达到出乎意料的出色效果。这给了人们相当的自信，去挑战用深度强化学习解决更复杂的任务，比如[围棋](https://deepmind.com/research/case-studies/alphago-the-story-so-far)、[Dota 2](https://openai.com/projects/five/)、[星际争霸 2](https://deepmind.com/blog/article/alphastar-mastering-real-time-strategy-game-starcraft-ii)等等。

此后，雅达利游戏已经称为强化学习研究的标准基准。最初的方法只是达到在 7 款游戏上击败人类，但多年来的发展和进步，现在的方法开始在更多的游戏上击败人类。尤其一款以需要长期规划著名的游戏叫《蒙特祖玛的复仇》（ Montezuma's Revenge），被认为是最难解决的问题。直到最近，这些技术才在全部 57 款游戏中击败人类。

## 2014 - 采用注意力的“编码器-解码器”网络

**论文：**

- [Sequence to Sequence Learning with Neural Networks](https://arxiv.org/abs/1409.3215)

- [Neural Machine Translation by Jointly Learning to Align and Translate](https://arxiv.org/abs/1409.0473)

**实现：**

- [Seq2Seq with Attention in PyTorch](https://pytorch.org/tutorials/intermediate/seq2seq_translation_tutorial.html#)

- [Seq2Seq with Attention in TensorFlow](https://www.tensorflow.org/addons/tutorials/networks_seq2seq_nmt)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/seq2seq-cn.gif 'Source: https://ai.googleblog.com/2017/04/introducing-tf-seq2seq-open-source.html')

深度学习最令人印象深刻的成果大多集中在由卷积神经网络驱动的视觉相关的任务。尽管 NLP 社区已经使用了 LSTM 和编码器-解码器结构，在[语言模型](http://karpathy.github.io/2015/05/21/rnn-effectiveness/)和翻译上取得了成功，但直到注意力机制的出现，该领域开始表现得出奇地好。

当处理语言时，每个符号（如字母、单词、符号），会被输入到循环网络（如 LSTM）中，并保留先前输入的部分记忆。换句话说，一个句子就像一段时序序列，而每个符号就是一个时步。这些循环模型通常很难解决时间间隔长的依赖关系。当处理一段序列时，它们容易“忘记”早期的输入，因为梯度需要传递很多个时步。因此通过梯度优化这些模型很困难。

新的注意力机制会减轻这种问题。它为网络引入一种“快捷连接”（shortcut connections）的选择，自适应地反馈到早期的时步中。这些连接允许网络在作输出时决定哪些输入是重要的。最典型的例子就是翻译：每当输出一个单词，它通常都对应到一个或多个特定的输入单词。

## 2014 - Adam 优化器

**论文：**

- [Adam: A Method for Stochastic Optimization ](https://arxiv.org/abs/1412.6980)

**实现：**

- [Implementing Adam in Python](https://d2l.ai/chapter_optimization/adam.html)

- [PyTorch Adam implementation](https://pytorch.org/docs/master/_modules/torch/optim/adam.html)

- [TensorFlow Adam implementation](https://github.com/tensorflow/tensorflow/blob/v2.2.0/tensorflow/python/keras/optimizer_v2/adam.py#L32-L281)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/optimizer-benchmark.png 'Source: http://arxiv.org/abs/1910.11758')

神经网络是使用优化器，通过最小化损失函数（如平均分类误差）来训练的。优化器负责调整网络的参数，使网络学习达到目标。大部分优化器都是[基于随机梯度下降（SGD）的各种变种](https://ruder.io/optimizing-gradient-descent/)。然而这些优化器本身都包含了比如学习率的可调参数。设置合理不仅可以减少训练时间，还能将结果收敛到一个更好的局部最优点。

大型研究院通常通过高成本的超参数搜寻算法，伴随着复杂的学习率调度器，来找出简单但对超参数敏感的优化器（比如 SGD）中最好的一个。即使有时候效果确实会超过现有基准，但是通过大量的资金“砸”出来的。这种细节一般在学术论文中忽略不提，因此对于那些预算没那么足的研究者会无法优化他们的优化器，从而陷入糟糕的结果中。

Adam 优化器主张用梯度的一阶矩和二阶矩来自动调整学习率。调整后的结果具有较好的鲁棒性，且对超参数的选择不那么敏感。换言之，Adam 不需要像其他优化器那样大费周章地进行调整即可使用。尽管“完全体”的 SGD 效果会更好，Adam 让研究更加通畅，因为如果出了问题，首先可以先排除优化器的锅。

## 2014/2015 - 生成对抗网络（GANs）

**论文：**

- [Generative Adversarial Networks](https://arxiv.org/abs/1406.2661)

- [Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks](https://arxiv.org/abs/1511.06434)

**实现：**

- [DCGAN in PyTorch](https://pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html)

- [DCGAN in TensorFlow](https://www.tensorflow.org/tutorials/generative/dcgan)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/early-gan-images.png 'Source: https://developers.google.com/machine-learning/gan/gan_structure')

生成模型（比如变分自编器）的目标，是生成仿真的数据样本，比如那些好像在哪里见过的人脸图片。GANs 这类网络需要对整个数据分布（想象有多少像素）进行建模模拟，而不是像*判别器*那样只分类成狗或猫。

GANs 背后的基本思想是先后训练两个网络：生成器和判别器。生成器的目标是生成能欺骗识别器的样本，而判别器经过训练，可以区分真图片和生成图片。一段时间过后，识别器会越来越擅长识别假图片，而生成器也会越来越擅长生成欺骗识别器的图片，图片看起来也会越来越真实。早期的 GAN 只能生成模糊的低分辨率图片，而且训练很不稳定。但随着时间推移，变种和改良版（如 DCGAN，Wasserstein GAN，CycleGAN，StyleGAN 等）能够生成高分辨率的真实图片和视频。

## 2015 - 残差网络（ResNet）

**论文：**

- [Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385)

**实现：**

- [ResNet in PyTorch](https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py)

- [ResNet in Tensorflow](https://github.com/tensorflow/tensorflow/blob/v2.2.0/tensorflow/python/keras/applications/resnet.py)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/resnet-slice.png)

研究人员一直在 AlexNet 这个突破之上挖掘更好的卷积神经网络（如 VGGNet，Inception 等）。ResNet 就是这一系列快速发展的下一代。时至今日，ResNet 的变体仍在各种任务和建立复杂结构中作为基准模型被应用。

除了在[ILSVRC 2015 分类挑战](http://image-net.org/challenges/LSVRC/2015/)中获得优胜，ResNet 的独特之处在于它与其它网络明显不同的深度。论文中最深的网络有 1000 层，在基准测试中表现得还不错，尽管比起对应的 101 层和 152 层网络还是略逊一筹。由于梯度消失的存在，训练这么深的网络是一个具有挑战性的优化问题，序列模型也会有这样的问题。很少研究者相信训练这么深的网络会有优秀且稳定的效果。

ResNet 应用了恒等捷径连接（identity shortcut connections），来帮助梯度传递。解释这些连接的一种说法是 ResNet 只需要学习两层之间的增量而不是完整的变换，这就显得简单很多。受 LSTM 的门机制的启发，这种恒定连接也是高速网络（Highway Networks）的一种特例。

## 2017 - Transformers

**论文：**

- [Attention is All You Need](https://arxiv.org/abs/1706.03762)

**实现：**

- [PyTorch: Sequence-to-Sequence Modeling with nn.Transformer and TorchText](https://pytorch.org/tutorials/beginner/transformer_tutorial.html)

- [Tensorflow: Transformer model for language understanding](https://www.tensorflow.org/tutorials/text/transformer)

- [HuggingFace Transformers Library](https://github.com/huggingface/transformers)

![The Transformer - model architecture](https://dennybritz.com/assets/deep-learning-most-important-ideas/transformer-architecture.png 'Source: https://arxiv.org/abs/1706.03762')

带上面提到过的注意力机制序列对序列（Sequence-to-Sequence）模型表现不错，但由于需要序列计算的循环特性，还是存在一些瑕疵：难以并行，因为每次只处理一个输入。每个时步都依赖上一个时步，这就使得把时步扩展成很长的序列很困难。即使用上注意力机制，这些模型仍会困在分析这些长期依赖的复杂性上。大部分“工作”似乎是在循环层里完成的。

Transformers 模型通过用多个前馈自注意层（feed-forward self-attention layers）完全代替循环层，并行处理所有输入，并在输入和输出之间生成相对较短（相当于更容易优化梯度下降）的路径，来解决这些问题。这使得模型训练得更快、容易扩展，以及能够处理更多数据。为了让网络了解输入的顺序（这在循环网络里是隐式的），Transformers 模型使用位置编码，想要知道 Transformers 到底是如何运作的，建议看[这份指南](http://jalammar.github.io/illustrated-transformer/)

要说 Transformers 模型表现比所有人预期要好，都算低估了 Transformers。接下来几年，它将成为今天大多数 NLP 和其他序列任务的标准构架，甚至还影响到计算机视觉的构架中。

## 2018 - BERT 和微调的 NLP 模型

**论文：**

- [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/abs/1810.04805)

**实现：**

- [Fine-Tuning BERT with HuggingFace](https://huggingface.co/transformers/training.html)

![](https://dennybritz.com/assets/deep-learning-most-important-ideas/bert-training.png)

预训练是指训练一个模型来完成某个任务，然后用经过学习的参数来初始化模型，再经过学习来完成其他有关联的任务。直观来说就是，一个已经学会分辨猫和狗的模型，也会学习到一些关于图像以及毛茸茸的动物的基本知识。当这个模型微调成可分类狐狸，人们会期望他比从头学习的模型表现要好。同样地，一个学会从一个句子中预测下一个词的模型，应该也学到了一些人类语言模式的基本知识。人们会期望它成为一些相关工作（如翻译、情感分析）的良好初始。

预训练和微调已经在 CV 和 NLP 顺利应用，但一直依赖都是视觉领域的标准，使得在 NLP 中用好会更具有挑战性。大多目前表现最优（SOTA，state-of-the-art）的成果仍来自完全监督模型。随着 Transformers 模型的出现，研究者终于开始预训练的工作，研究出 ELMo、ULMFiT 和[OpenAI 的 GPT](https://openai.com/blog/language-unsupervised/)。

BERT 是同类发展的最新产物，甚至可以认为它开启了 NLP 的新纪元。与其他模型一样在预测单词上预学习不同，它在预测句子中哪些单词会被故意去除以及两个句子之间是否存在联系，这些事情上预训练。注意这些任务不需要标注的数据，它可以在任何文本上训练。预训练模型可能学到一些一般的语言属性，就可以进行微调进而解决监督问题（如问答系统、情感预测）。BERT 在各种任务都表现得惊人地好，像 HuggingFace 这样的公司提供了下载，并微调了类 BERT 模型来解决 NLP 任务。到目前为止，BERT 发展了如 XLNet、RoBERTa 和 ALBERT 等变体。

## 2019/2020 及以后 - 大型语言模型、自监督学习？

[《苦痛的教训》](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)一文中清晰地描述了深度学习的历史趋势。算法的并行化（更多的数据）和更多的模型参数，一次又一次地战胜了“聪明的技术”。当 OpenAI 的[GPT-3](https://arxiv.org/abs/2005.14165)，一个 1750 亿参数的普通架构语言模型，只需普通地训练展现出出乎意料地泛化能力时，这个趋势似乎会在 2020 年继续下去。

具有同样趋势的还有对比性自监督学习（contrastive self-supervised learning），比如[SimCLR](https://arxiv.org/abs/2002.05709)，可以更好地利用未标注的数据。随着模型越来越大和训练得越来越快，能够在网络上高效使用大量未标注数据的技术和学习通用知识的迁移学习系统会变得有价值且广泛采用。

## 这里还有……

如果你觉得论文不够看：

- [Distributed Representations of Words and Phrases and their Compositionality (2013)](https://papers.nips.cc/paper/5021-distributed-representations-of-words-and-phrases-and-their-compositionality)

- [Speech recognition with deep recurrent neural networks (2013)](https://arxiv.org/abs/1303.5778)

- [Very Deep Convolutional Networks for Large-Scale Image Recognition (2014)](https://arxiv.org/abs/1409.1556)

- [Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift (2015)](https://arxiv.org/abs/1512.00567)

- [Rethinking the Inception Architecture for Computer Vision (2015)](https://arxiv.org/abs/1512.00567)

- [WaveNet: A Generative Model for Raw Audio (2016)](https://arxiv.org/abs/1609.03499)

- [Mastering the game of Go with deep neural networks and tree search (2016)](https://storage.googleapis.com/deepmind-media/alphago/AlphaGoNaturePaper.pdf)

- [Neural Architecture Search with Reinforcement Learning (2017)](https://arxiv.org/abs/1611.01578)

- [Mask R-CNN (2017)](https://arxiv.org/abs/1703.06870)

- [Dota 2 with Large Scale Deep Reinforcement Learning (2017-2019)](https://arxiv.org/abs/1912.06680)

- [The Lottery Ticket Hypothesis: Finding Sparse, Trainable Neural Networks (2018)](https://arxiv.org/abs/1803.03635)

- [EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks (2019)](https://arxiv.org/abs/1905.11946)

---
layout: post
comments: true
title: Post Template
author: UCLAdeepvision
date: 2024-01-01
---


> This block is a brief introduction of your project. Test


<!--more-->
{: class="table-of-content"}
* TOC
{:toc}

## Main Content
Your survey starts here. You can refer to the [source code](https://github.com/lilianweng/lil-log/tree/master/_posts) of [lil's blogs](https://lilianweng.github.io/lil-log/) for article structure ideas or Markdown syntax. We've provided a [sample post](https://ucladeepvision.github.io/CS188-Projects-2022Winter/2017/06/21/an-overview-of-deep-learning.html) from Lilian Weng and you can find the source code [here](https://raw.githubusercontent.com/UCLAdeepvision/CS188-Projects-2022Winter/main/_posts/2017-06-21-an-overview-of-deep-learning.md)

## Basic Syntax
### Image
Please create a folder with the name of your team id under /assets/images/, put all your images into the folder and reference the images in your main content.

You can add an image to your survey like this:
![YOLO]({{ '/assets/images/UCLAdeepvision/object_detection.png' | relative_url }})
{: style="width: 400px; max-width: 100%;"}
*Fig 1. YOLO: An object detection method in computer vision* [1].

Please cite the image if it is taken from other people's work.

## Unlearnable Examples
# Introduction
Unlearnable examples is an AFR technique designed to render training data ineffective for Deep Neural Networks (DNNs). The primary objective is to ensure that DNNs trained on these modified examples perform no better than random guessing when evaluated on standard test datasets Unlike traditional privacy-preserving methods that obscure or obfuscate information to protect individual privacy, unlearnable examples aim to maintain the visual quality of the data. For instance, an unlearnable selfie should remain visually appealing and suitable for use as a social profile picture, achieved by adding imperceptible noise that does not introduce noticeable defects.

DNNs are susceptible to adversarial, or error-maximizing, noise—small disturbances designed to increase a model’s error during testing However, applying this type of noise in a sample-wise manner during training does not effectively prevent DNNs from learning. To address this limitation, error-minimizing noise is introduced, which aims to deceive the model into perceiving that there is no valuable information to learn from the training examples. This results in unlearnable examples that hinder the DNN’s ability to generalize, making its performance comparable to random guessing on standard test data.

Error-minimizing noise can be generated in two forms: sample-wise and class-wise. Class-wise error-minimizing noise proves to be more effective than random noise and remains robust against training techniques like early stopping. On the other hand, sample-wise error-minimizing noise is identified as the only method capable of making training examples truly unlearnable, outperforming both random and error-maximizing noise strategies.

# Objectives

Suppose the clean training dataset consists of \( n \) clean examples, \( \mathbf{x} \in \mathcal{X} \) inputs, \( y \in \mathcal{Y} = \{1, \dots, K\} \) labels, and \( K \) is the total number of classes. We denote its unlearnable version by \( \mathcal{D}_u = \{(\mathbf{x}_i, y_i)\}_{i=1}^n \), where \( \mathbf{x} = \mathbf{x} + \delta \) is the unlearnable version of training example \( \mathbf{x} \in \mathcal{D}_c \) and \( \delta \in \Delta \subset \mathbb{R}^d \) is the “invisible” noise that makes \( \mathbf{x} \) unlearnable. The noise \( \delta \) is bounded by \( \|\delta\|_p \leq \epsilon \), where \( p \) is the \( \ell_p \) norm, and is set to be small such that it does not affect the normal utility of the example.

The DNN model will be trained on \( \mathcal{D}_c \) to learn the mapping from the input space to the label space: \( f : \mathcal{X} \rightarrow \mathcal{Y} \). The goal is to trick the model into learning a strong correlation between the noise and the labels: \( f : \Delta \rightarrow \mathcal{Y}, \Delta = \mathcal{X} \), when trained on \( \mathcal{D}_u \), the unlearnable version:

$$
\arg\min_{\theta} \mathbb{E}_{(\mathbf{x}, y) \sim \mathcal{D}_u} \left[ L(f(\mathbf{x}), y) \right]
$$

where \( L \) is the classification loss such as the commonly used cross-entropy loss.

The noise forms are as follows: **sample-wise noise**, \( \mathbf{x}_i = \mathbf{x}_i + \delta_i \), \( \delta_i \in \Delta_s = \{\delta_1, \dots, \delta_n\} \), while for **class-wise noise**, \( \mathbf{x}_i = \mathbf{x}_i + \delta_{y_i} \), \( \delta_{y_i} \in \Delta_c = \{\delta_1, \dots, \delta_K\} \). Sample-wise noise requires generating unique noise for each individual example, which can limit its practicality. In contrast, class-wise noise applies the same noise to all examples within a specific class, making it more efficient and flexible for real-world applications. However, as we will observe, class-wise noise is more susceptible to detection and exposure.



| **Setting**               | **Task**             | **Metric**                         | **Result**                             |
|---------------------------|----------------------|------------------------------------|----------------------------------------|
| **Partially Unlearnable** | **Face Recognition** | Accuracy for Unlearnable Identities | 16%                                    |
|                           |                      | Accuracy for Clean Identities       | 86%                                    |
| **Partially Unlearnable** | **Face Verification** | Area Under Curve (AUC)              | Reduced compared to clean setting      |
| **Fully Unlearnable**     | **Face Verification** | Area Under Curve (AUC)              | 0.5321 (Clean Setting AUC: 0.9975)     |


### Table
Here is an example for creating tables, including alignment syntax.

|             | column 1    |  column 2     |
| :---        |    :----:   |          ---: |
| row1        | Text        | Text          |
| row2        | Text        | Text          |



### Code Block
```
# This is a sample code block
import torch
print (torch.__version__)
```


### Formula
Please use latex to generate formulas, such as:

$$\tilde{\mathbf{z}}^{(t)}_i = \frac{\alpha \tilde{\mathbf{z}}^{(t-1)}_i + (1-\alpha) \mathbf{z}_i}{1-\alpha^t}$$

or you can write in-text formula $$y = wx + b$$.

### More Markdown Syntax
You can find more Markdown syntax at [this page](https://www.markdownguide.org/basic-syntax/).

## Reference
Please make sure to cite properly in your work, for example:

[1] Redmon, Joseph, et al. "You only look once: Unified, real-time object detection." *Proceedings of the IEEE conference on computer vision and pattern recognition*. 2016.

---

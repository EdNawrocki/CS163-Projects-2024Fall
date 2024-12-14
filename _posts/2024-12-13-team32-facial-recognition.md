---
layout: post
comments: true
title: Anti-Facial Recognition Technology
author: Edward Nawrocki, Donovan Rimer, Tyler Cho
date: 2024-12-13
---


> This block is a brief introduction of your project. You can put your abstract here or any headers you want the readers to know.


<!--more-->
{: class="table-of-content"}
* Introduction
* Fawkes
    * Motivation
    * Optimizing Cloak Perturbations
    * Results
* MTCNN-Attack
    * Motivation
    * Optimizing Patches
    * Results
{:toc}

---

## Introduction
ayaya

## MTCNN-Attack

### Motivation

An alternative approach to the identity masking that Fawkes proposes is preventing the identification of facial features altogether. In their proposed model MTCNN-Attack, authors Kaziakhmedov et al. aim to use physical domain attacks to prevent facial features from being properly identified by multi-task cascaded CNNs (MTCNNs). MTCNNs make use of several sub-networks to identify faces:

1. Proposal networks (P-Nets): performs preliminary facial detection by proposing bounding boxes that contain potential face candidates
2. Refine networks (R-Nets): improves detection by eliminating false positive proposals and refining the locations and dimensions of the remaining boxes
3. Output network: detects facial landmark locations and outputs the final bounding box(es)

Their method of choice attacks the first stage of this process, as it presents an opportunity to minimize the most computation costs. By placing grayscale-patterned patches (a common physical domain attack method) onto the cheek areas of face images and using various transformation techniques to ensure robustness, their model has displayed success in preventing the recognition of faces.

### Optimizing Patches

Three distinct loss functions are used to develop adversarial patches. 
- L2 loss and Lclf were both used to quantify face classification loss. 
- To make the grayscale patches as natural as possible, total variation loss Ltv was defined for a given pixel p(i, j) to penalize sharp transitions and noise within the patch patterns:

![Total Variation Loss Function]({{'/assets/images/TylerCho/Ltv.png' | relative_url}})
{: style="width: 400px; max-width: 100%;"}
*Equation X. The loss function for total variation loss. Taken from [src]*

- Testing was also performed on samples where portions of the face were obscured by surgical masks; the authors found that, in these cases, the amount of black used could be penalized to make the patches look more natural. They defined black penalty loss, Lblk, as the following for a given pixel p(i, j):

![Black Loss Function]({{'/assets/images/TylerCho/Lblk.png' | relative_url}})
{: style="width: 400px; max-width: 100%;"}
*Equation X+1. The loss function for black loss. Taken from [src]*

Together, the total loss was defined using the function below, where α and β are scaling factors that control the contributions of total variance loss and black loss, respectively.

![Total Loss Function]({{'/assets/images/TylerCho/L.png' | relative_url}})
{: style="width: 400px; max-width: 100%;"}
*Equation X+2. The loss function for the total loss, combining Lclf, Ltv and Lblk with their weighted factors. Taken from [src]*

Both α and β were hyperparameters that were each individually optimized for. These three losses are evaluated together, summed, and back propagated to the patches for them to update. This training process continued for 2000 epochs per pair of patches.

![MTCNN-Attack Attack Pipeline]({{'/assets/images/TylerCho/pipeline.png' | relative_url}})
{: style="width: 800px; max-width: 200%;"}
*Fig. 3. The attack pipeline for MTCNN-Attack. A pair of patches is applied to N images and goes through data augmentation. The resulting images are fed through proposal networks to calculate classification loss, which is used alongside two other custom losses to update the patches through backpropagation. Taken from [src]*

Another concern was the applicability of this methodology in real-time scenarios. In realistic cases, factors like lighting or angling differences could decrease the effectiveness of the patches. To account for this, Kaziakhmedov et al. used multiple images with different head positions and lighting for each sample, and marked each of the patch boundaries for each image. This allowed them to implement Expectation-over-Transformation (EoT) and projective transformations, which increase model effectiveness by ensuring that the patches are mapped correctly.

![Patch Mapping for a Sample]({{'/assets/images/TylerCho/projmap.png' | relative_url}})
{: style="width: 800px; max-width: 200%;"}
*Fig. 4. An example of the sample input and the resultant patch mapping for the MTCNN-Attack model. Both EoT and projective mapping were used to ensure optimal patch placement. Taken from [src]*

### Results

Tests were performed to evaluate the probability of misdetection for both the bare-faced (cheeks) and masked datasets, and both unpatched and patched tests were considered. 2000 epochs were trained for the patches, and each result was averaged over a series of 1000 frames for each scale step factor. Scale step factors were valued at {0.709, 0.82, 0.9, and 0.95}. Over the range of scale steps, the probability of misdetection averaged slightly below 0.9 for the patched tests, and slightly below 0.2 for the unpatched for cheek; for masked, the model performed slightly worse, averaging slightly above 0.8 for patched and slightly above 0.2 for unpatched. 

![Test Results]({{'/assets/images/TylerCho/results.png' | relative_url}})
{: style="width: 800px; max-width: 200%;"}
*Fig. 5. The test results for both cheek and masked trials. Both tests show a strong misdetection performance for patched trials, especially when compared to unpatched trials. Taken from [src]*

There are several issues that are worth consideration, however. Firstly, the completely unpatched database used to match patched and unpatched samples with people only contained images of the people in the sames (a “targeted” attack); this means that further investigation must be performed to evaluate whether or not the learned patches perform as well for the general human population, or whether transferability is an issue that must be addressed in the future. Secondly, there is an element of impracticality associated with this project, as one must be wearing the black-and-white checkered marks to denote where the patches should be located. Generally, this may be deemed unsuitable for everyday use. But overall, the results show a considerable improvement with the learned patches as opposed to unpatched, suggesting that the model conceivably could be used to prevent facial identification in the future.


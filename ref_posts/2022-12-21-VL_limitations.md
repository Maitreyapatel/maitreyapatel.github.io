---
layout: distill
title: What is going on with multi-modal representation learning?
date: 2022-12-21 11:59:00-0400

authors:
  - name: Maitreya Patel
    url: "maitreyapatel.com"
    affiliations:
      name: ASU



description: Reviewing limitations of existing VL research.
comments: true
categories: vision-language analysis


toc:
  - name: Introduction
  - name: Pre-training of LLMs vs. Multi-modal models
  - name: Limitations of these strategies
  - name: Possible Solutions
  - name: Relative pre-training?
  - name: Data Augmentations
  - name: Conclusion

_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }


---

## Introduction

The multi-modal system refers to the idea of combining the vision+text+speech. More specifically, Vision-Language (VL) is becoming more accurate and accessible. Especially, Text-to-Image (T2I) diffusion models were the key highlight of 2022. Apart from T2I other VL tasks such as Captioning, Question-Answering, etc. are equally important and the latest proposed methodologies keep pushing the performance with the use of external data (LAION-2B), different pre-training strategies (SimVLN, BLIPs, etc.), increasing the computational complexity, and removing various spurious biases via either data augmentation or casual interventions. 

Although T2I results are quite a breakthrough and show huge potential, Visual Grounding and Visual Reasoning methods seem to be saturated in terms of performance improvements. Recent various benchmark studies (Winoground etc.) the existing models' ability to understand compositionality. For example, **"[grass] in a [mug]"** is `semantically different` than **"[mug] in a [grass]"**. These experiments show that current approaches struggle to accurately distinguish these semantic perturbations, which suggests that current approaches are a `glorified` and `advanced` version of learning the correlations between visually seen and textually present entities. 

**Let's achieve the 100% accuracy on winoground with this article.**

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/9.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/7.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    show the examples of winoground or other interesting dataset
</div>

Since the raise of GPT3 and prompt/instruction learning, everyone knows the potential and impact of these systems in day-to-day life. It has been shown that LLMs contain a vast amount of commonsense knowledge and they do not require additional fine-tuning to have a decent performance. However, it seems that research in VL is stuck in learning the correlations. Even if these methods can overcome the challenge of compositionality, can they do commonsense reasoning?

There are several future directions to can be taken from this point onwards to build a true Artificial General Intelligence (AGI). Jokes aside, we do not know what AGI even means but it would be sufficient if we can even develop Human-Level AI (HLAI), which can learn to reason about commonsense in the real-world and overcome the compositionality.

> **Direction 1:** Good news! We have LLMs with good reasoning capabilities. If we can design a system that can effectively ground the videos/images for LLMs to do the reasoning. Or maybe let LLMs control the VL models to get more accurate information from visuals. 

The above direction is really simplistic and might work for a short period of time but still, this is not sufficient. `Spoiler alert:` Captions are not sufficient enough to develop a valid context for LLMs. Theoretically, each image has infinite observations and possibilities, which is hard to impossible to iterate through efficiently. Therefore, we need to create the systems following direction 2:

> **Direction 2:** Answer is pretty straight forward!! We need to design multi-modal systems which have the same capabilities as LLMs (like GPT3). Initial work (such as Flamingo) has shown its potential for this. But we are just at the beginning phase and there is so much to be done. `Can concepts from causality be the key to achieving this?` 


With this article, the authors would dive deep into the technical details to validate the above-proposed hypothesis. This study assumes to have some amount of prior knowledge about Vision-Language systems and it is divided into several sections. **Section 2** summarizes the existing VL pre-training strategies, **Section 3** compares the pre-training strategies of LLMs vs. VL models, **Section 4** shows the limitations of these methods using existing benchmark results, **Section 5** proposes the quick and easy solutions to overcome these limitations to show the future potential directions, and **Section 6** discusses various questions which need to be (or hopefully will be) answered soon.

Reach out to the authors for feedback or questions!


## Pre-training of LLMs vs. Multi-modal models

Wheather it is LLMs or Multi-modal models, different pre-training strategies have shown the impact. These pre-training methods can be broadly classified into two broad categories: 1) Discriminative, and 2) Generative.

Discriminative strategies involve various sub pre-trainings such as next sentence prediction or image-text alignment, and masked token (either image patch or word token) predictions. Initial results on BERT/RoBERTa, VinVL/ViLT etc. have shown the great results on down-stream tasks. 

Generative modeling strategies are getting more and more popular recently. We all know about GPT3 and how it impacted the reseach coomunity. Because of the generative pre-training, we have seen many immergent abilities like prompt/instrunction learning. Similarly, VL models are also being adapated to generative modeling and they have shown quite a promising results. 


## Limitations of these strategies

Inspiring from Language-Modeling, existing SotA VL models are all about increasing the model complexity and adding more data. No doubt this will improve the results on existing benchmarks and that's impressive for sure. But the question still remains that do these models have the semantic understandings or not? With the recent benchmarks (as listed below) it can been seen that these models performs very poorly and suggests that they are just learning correlation.

1. Winoground -- Compositional Image-Text Alignment
2. VALSE -- Compositional semantic understanding on diverse categories 
3. VQA-LOL -- Logical Reasoning
4. Crepé -- Compositional Hard-Negative sampling based Image/Text Retrieval
5. Visor -- Relative Spatial Reasoning


### Results on winogrond:

As shown in Fig. (1) the winoground focuses on learning semantic understanding from the images. By just swapping the two words in text the meaning completely changes. And VL models fail to detect these semantic perturbations. From Table, we can observe that all SotA VL models performs close to random. 


<div class="row">
    {% include figure.html path="assets/img/7.jpg" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    winoground table
</div>


### Results on Crepé:

Similarly, Crepé analyses the compositionality at different levels of difficulty. And again the results are really poor. 

<div class="row">
    {% include figure.html path="assets/img/7.jpg" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Crepé table
</div>


## Possible Solutions

After just glancing through the challenges of VL models it becomes obvious that we need to answer the following questions:

1. How can we avoid the spurious correlations being learned by the model?
2. Can these models have commonsense reasoning abilities?
3. Are existing pre-training sufficient enough to get the breakthrough? 


### How can we avoid the spurious correlations being learned by the model?

This is really broad question to ask. And this issue is observed in almost all machine learning problems. Community has started adapting the concepts for causality to minimize learning the spurious correlations. For example, VL-Bias attempts to remove the language bias by performing interventions, which resulted in model being robust to some spurious correlations (For example, model stopped predicted all bananas being yellow to actually observing the image and answer the correct color of the bananas). Similarly, Visual-Commonsense RCNN attempt to remove the object-object relation based spurious correlation for object-detection.

Therefore, we strongly believe that causal interventions are the way to move forward.


### Can these models have commonsense reasoning abilities?

We have seen that LLMs have impressive commonsense reasoning skills. While, VL models seem to struggle a lot. For example, to reason about the consequences of kicking a football vs. brick model first needs to understand the visual aspects correctly (where the first problem becomes quite relevant) and then do the reasoning about the consequences.


### Are existing pre-training sufficient enough to get the breakthrough? 

After looking into previous two questions, we know that next goals to achieve. **But HOW??**

To answer this question, first we need to forget about the adding the data or increasing the models' computational complexity. To make the significant improvement, we need to design the better pre-training strategies and once we achieve the promising results on smaller scale, adding the more resources will further boost the performance. 

Now, that's cleared up, what are the best ways to improve the pre-training strategies?

Well, we can't answer that :/ This is the research problem that we need to work on. But in this article, we propose potential immediate next steps which shows the promising results.
`Spolier:` We can get achieve 100% ACCURACY on winoground benchmark without task specific fine-tuning. And our answer is **curriculum learning**. 

As we previosuly discussed that VL models relys on the Image-Text Alignment and Mask Token Predictions. However, the Image-Text Alignment is done at random with 50% being the correct pair and another 50% either image or text is randomly selected from the corpus. Let's understand the issue in this pre-training by considering an intuitive example. Suppose, we have an ground truth image-text pair (``a mug in a grass``). Now, we create the another example where we change the text randomly (``a horse is running on the seashore``)) and keep the same image. As we can see that by selecting the random text for creating negative example is really easy for models to overfit. To learn the accurate semantic meaning models need to learning to differentiate the semantically similar examples. Contrastive learning strategies are great but if we look at mathamatically what are the chances of getting a positive & negative example pairs which are semantically close to each other? Yesss, you are thinking it correctly! It's close to **zero**!!!!!!!

Okay, so obvious solution is to just train the model on semantically similar data and boom you are good to go. But sadly that's not the way to move forward. Because of the computational compexity of the recent models it becomes really difficult to train the models hard examples and expect it to achieve the SotA performance. Research in LLMs has shown that training these huge models itself is challenging. And therefore, we need to design a curriculum learning strategies where we increase the difficulty of the dataset slowly and let the model converge. 

Now, let's see how to achieve the SotA performance on winoground and crepé without any prior knoweldge or even fine-tuning. 


#### Maybe we can do small scale experiment and put some results here.

## Impact of Constrastive vs. Comparitive pre-training

Contrastive learning is well research training strategies. Is it really possible to improve it? If so how? 

In Computer Vision, Contrastive Learning is used as self-supervised training method and to be honest results are impressive. Here, we augment the input images using statistical methods and use these augmented examples for pre-training the model. However, to learning the semantic meaning within the images, maybe we can do the semantic image perturbation (i.e., Data Augmentations). However, this process is really expenstive in terms of resources and time (more about this in next section). Maybe we need something different here. 

How can we do the comparative pre-training? Let's take the inspiration from the one of the closest problem that is `NLVR2`. It contains the problem statement, where model needs to compare two input images with respect to the given input text. And the way this dataset is designed is really useful. It contains the two images which are really close to each other in terms of semantics and needs to reason about the similarity and dissimilarity between them. So, thw question is ``what if we train the VL model on such comparative setting?`` To understand the impact of these, we take various  fine-tuned models on NLVR2 and evaluate their performance on Winoground and Crepé. Results shows that these models perform >90% on these benchmarks without any task related fine-tuning. 

Although this results are awesome and quite promising. These models have very low confidance at the same time. So there are still room for improvements but these pre-training strategy seems to be quite useful.


## Data Augmentations
TBD

## Conclusion
TBD
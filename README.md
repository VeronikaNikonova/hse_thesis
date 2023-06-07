# Discovering semantic shifts in the Russian language in news and social media using word embedding models

This research is focused on discovering and detecting diachronic semantic shifts in the Russian language with the use of different word embedding models that represent different approaches: 
<l>
  <li>static approach (Skip-gram with negative sampling model [4]),</li>
  <li>dynamic approach (Dynamic word embeddings model [6]) and </li> 
  <li>contextualized approach (BERT model [1]).</li></l> <br>

We use 
[ruBert-base](https://huggingface.co/ai-forever/ruBert-base) version of the BERT model.<br>
<br>
We apply the indicated models to the News corpus and to the Social media corpus in order to reveal social, political, cultural, etc. changes through semantic changes that are assessed as the similarity (namely, cosine similarity) between embeddings of a word in different time slices (from 2000 to 2019 and from 2007 to 2019 correspondingly).<br>
<br>
We analyze the revealed changes and compare performance of the models on two tasks: 1) discovering semantic shifts and 2) detecting known shifts.<br>

## Goal
The main goal of this research is to discover semantic shifts in the Russian language that happened in the first 20 years of the 21th century using news and social media data and compare different approaches (static, dynamic and contextualized) in revealing them.<br>

## Data
The repository data contains news and social media datasets separated by year. The whole datasets are too large for uploading them to Github and can be found here: 
[News corpus](https://drive.google.com/file/d/1_lzshaBJ7Klm_7p1Mysg5uk_ovQB0IF9/view?usp=drive_link) and 
[Social media corpus](https://drive.google.com/file/d/1nMPewEakzH_y80quUGVmRktz0Twcepf1/view?usp=drive_link)<br>
<br>
The repository notebooks contains Colab notebooks with code and results of our work separated into 3 groups: news and social media (for the discovering task) and classification (for the detection task). In each group there are 3 notebooks, 1 per each model (SGNS, Dynamic word embeddings model and BERT). <br>

## Models
Here you can find the trained (or fine-tuned) models for each task.<br>
For the News corpus:
[SGNS](https://drive.google.com/drive/folders/1IZ6GIDcCvmix7dLZRZHQ4SkjxxSPwfbC?usp=sharing),
[Dynamic word embeddings model](https://drive.google.com/drive/folders/1KAkuRNKFN40FE6CIRQlwKp8WLYRY3JwR?usp=sharing),
[BERT](https://drive.google.com/drive/folders/1NfuobbC-wFUZmonvmwSZF1F-8tCElQ-L?usp=sharing).<br>
And for the Social media corpus:
[SGNS](https://drive.google.com/drive/folders/1IvE7_Met67r2A8oRUB02XNPV_ZeoIch9?usp=sharing),
[Dynamic word embeddings model](https://drive.google.com/drive/folders/1ZGR3B4Dca7USzsg9vFoqHIkQQDuzQBdK?usp=sharing),
[BERT](https://drive.google.com/drive/folders/1Xrm2Tz91pCcTfO7z8wz4E4towNXwSkP-?usp=sharing).<br>

## Experimental setup
We conduct two types of experiments: 1) discovering semantic shifts (for all the chosen models and for both datasets) and 2) classification task on detecting known shifts (for all the chosen models, but only for the News corpus). In the first task we aim at revealing semantic changes from the data and in the second task we analyze how well models can detect semantic shifts with the use of an annotated dataset.<br>

#### For discovering semantic shifts task we use the following Pipeline:
<ol>
<li> Train (of fine-tune) embeddings model on our data. </li>
<li> Align the embeddings and reduce them to the 2019 vector space (only for SGNS). </li>
<li> Calculate the cosine similarity measure for each of the eligible words (occurrence at least 50 times for the News corpus and 10 times for the Social media corpus and present in the corresponding time periods) for word embeddings of 2000 and 2019 years (for BERT we use word prototype embeddings: averaged embeddings of all the occurrences of the eligible words in the appropriate year). </li>
<li> Obtain top 20 words with the lowest cosine similarity between these time periods (meaning that the semantic shift for these words in the time period is the highest). </li>
<li> Analyze the revealed semantic shifts in terms of their validity and actuality: for each of the words we find the closest neighbors in order to understand the context in which they are used in each period and make the decision on presence/ absence of semantic shift.</li>
</ol>

#### For classification task the Pipeline is the following: 
<ol>
<li> Take embedding models from the previous task, retrain them if necessary. </li>
<li> Calculate the cosine similarity measure for each of the words in the classification list (for BERT again we use word prototypes: averaged embeddings of all the occurrences of the words in classification list [2] in the appropriate years). </li>
<li> Train Random Forest Classifier on top on the obtained feature (cosine similarity). </li>
<li> Evaluate the models with quality metrics (F1 score, as the main metric, and balanced accuracy, precision and recall, as additional metrics). </li>
</ol>

## Main results
The comparison of the performance of the three models on semantic shifts discovering and detecting tasks gave us the following results. The BERT model showed the best performance on the discovering task while SGNS showed better results on detecting known shifts task. Despite the fact that BERT is the most powerful model, it showed poorer performance on classification task. There may be several reasons for that: 
<l>
<li> As it was noticed in [5], contextualized models, like BERT, show state-of-the-art performance in detecting semantic shifts for high-polysemy words, while for low-polysemy words the performance of such models drops considerably, demonstrating poorer results in comparison with word2vec models, and in our dataset there are a lot of low-polysemy words. </li>
<li> In our research we used averaged word embeddings for each of the occurrences of a word during corresponding year in order to obtain one word embedding per each word in each time slice, because it is computationally efficient and this technique showed good performance in [3]. However, in [5] averaging showed worse performance than clustering of word embeddings and even underperformed word2vec model in a bilingual semantic change task. Thus, using clustering technique instead of averaging for the purpose of our classification task could have yielded better results. </li>
<li> Even fine-tuning base version of the BERT model requires significant resources. We used its base variation (ruBert-base) and fine-tuned it on our data only for 1 epoch, if we used the large version or fine-tuned the model for 2 epochs, the model could have shown better performance. </li>
</l>

## References
[1] [Devlin, J.; Chang, M.-W.; Lee, K.; Toutanova, K: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding, 2018.](https://arxiv.org/abs/1810.04805) <br>
[2] [Fomin, V., Bakshandaeva, D., Rodina, J., Kutuzov, A.: Tracing cultural diachronic semantic shifts in Russian using word embeddings: test sets and baselines. Komp’yuternaya Lingvistika i Intellektual’nye Tekhnologii: Dialog conference, 2019. 203–218 pp.](https://arxiv.org/abs/1905.06837)<br>
[Datasets](https://github.com/wadimiusz/diachrony_for_russian)<br>
[3] [Martinc, M., Montariol, S., Zosa, E., Pivovarova, L.: Discovery Team at SemEval-2020 Task 1: Context-sensitive Embeddings not Always Better Than Static for Semantic Change Detection. Proceedings of the Fourteenth Workshop on Semantic Evaluation, 2020.](https://paperswithcode.com/paper/discovery-team-at-semeval-2020-task-1-context) <br>
[4] [Mikolov, T., Sutskever, I., Chen, K., Corrado, G., Dean, J.: Distributed representations of words and phrases and their compositionality. Advances in Neural Information Processing Systems. 26, 2013.](https://arxiv.org/abs/1310.4546) <br>
[Code](https://code.google.com/archive/p/word2vec/)<br>
[5] [Montariol, S.: Models of diachronic semantic change using word embeddings. Université Paris-Saclay, 2021.](https://theses.hal.science/tel-03199801/document) <br>
[6] [Yao, Z., Sun, Y., Ding, W., Rao, N., Xiong, H.: Dynamic word embeddings for evolving semantic discovery. In: Proceedings of the eleventh acm international conference on web search and data mining. Marina Del Rey, CA, USA, 2018. 673–681 pp.](https://arxiv.org/abs/1703.00607)<br>
[Code](https://github.com/yifan0sun/DynamicWord2Vec)<br>

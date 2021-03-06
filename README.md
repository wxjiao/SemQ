# HiGRU: Hierarchical Gated Recurrent Units for Utterance-level Emotion Recognition 

This is the Pytorch implementation of HiGRU: Hierarchical Gated Recurrent Units for Utterance-level Emotion Recognition in NAACL-2019. [[paper]](https://www.aclweb.org/anthology/N19-1037)

## Brief Introduction

In this paper, we address three challenges in utterance-level emotion recognition in dialogue systems: (1) the same word can deliver different emotions in different contexts; (2) some emotions are rarely seen in general dialogues; (3) long-range contextual information is hard to be effectively captured.  We therefore propose a hierarchical Gated Recurrent Unit (HiGRU) framework with a lower-level GRU to model the word-level inputs and an upper-level GRU to capture the contexts of utterance-level embeddings.   Moreover, we promote the framework to two variants, HiGRU with individual features fusion (HiGRU-f) and HiGRU with self-attention and features fusion (HiGRU-sf), so that the word/utterance-level individual inputs and the long-range contextual information can be sufficiently utilized.  Experiments on three dialogue emotion datasets, IEMOCAP, Friends, and EmotionPush demonstrate that our proposed HiGRU models attain at least 8.7%, 7.5%, 6.0% improvement over the state-of-the-art methods on each dataset, respectively.  Particularly, by utilizing only the textual feature in IEMOCAP, our HiGRU models gain at least 3.8\% improvement over the state-of-the-art conversational memory network (CMN) with the trimodal features of text, video, and audio.

<div align="center">
    <img src="/image/HiGRU.png" width="80%" title="Framework of HiGRU."</img>
    <p class="image-caption">Figure 1: The framework of HiGRU.</p>
</div>


## Code Base

### Dataset
Please find the datasets via the following links:
  - [Friends](http://doraemon.iis.sinica.edu.tw/emotionlines): **Friends** comes from the transcripts of Friends TV Sitcom, where each dialogue in the dataset consists of a scene of multiple speakers.
  - [EmotionPush](http://doraemon.iis.sinica.edu.tw/emotionlines): **EmotionPush** comes from private conversations between friends on the Facebook messenger collected by an App called EmotionPush.
  - [IEMOCAP](https://sail.usc.edu/iemocap/): **IEMOCAP** contains approximately 12 hours of audiovisual data, including video, speech, motion capture of face, text transcriptions.

[HiGRUs storage](https://drive.google.com/drive/folders/172g0RdBjlN-lBO2a6iFlWIuzpnwSCSwB?usp=sharing) also provides a full collection of the three datasets in `.json` format preprocessed by us.

#### Prerequisites
- Python v3.6
- Pytorch v0.4.0-v0.4.1
- Pickle

#### Data Preprocessing
For each dataset, we need to preprocess it using the `Preprocess.py` file as:
```ruby
python Preprocess.py -emoset Friends -min_count 2 -max_seq_len 60
```
The arguments `-emoset`, `-min_count`, and `-max_length` represent the dataset name, the minimum frequency of words when building
the vocabulary, and the max_length for padding or truncating sentences.

#### Pre-trained Word Embeddings
To reproduce the results reported in the paper, please adopt the pre-trained word embeddings for initialization. You can download the 300-dimentional embeddings from below:
- Word2Vec: [GoogleNews-vectors-negative300.bin.gz](https://code.google.com/archive/p/word2vec/)
- GloVe: [glove.840B.300d.zip](https://nlp.stanford.edu/projects/glove/)

Decompress the files and re-name them as `word2vec300.bin` and `glove300.txt`, respectively.

#### Train
You can run the `exec_emo.sh` file in **Bash** as:
```ruby
bash exec_emo.sh
```

Or you can set up the model parameters yourself:
```ruby
python EmoMain.py \
-lr 2e-4 \
-gpu 0 \
-type higru-sf \
-d_h1 300 \
-d_h2 300 \
-report_loss 720 \
-data_path Friends_data.pt \
-vocab_path Friends_vocab.pt \
-emodict_path Friends_emodict.pt \
-tr_emodict_path Friends_tr_emodict.pt \
-dataset Friends \
-embedding Friends_embedding.pt
```
**More Details**:
  - The implementation supports both CPU and GPU (but only one GPU), you need to specify the device number of GPU in your arguments otherwise the model will be trained in CPU. 
  - There are **three** modes in this implementation, i.e., `higru`, `higru-f`, and `higru-sf`, as described in the paper. You can select one of them by the argument `-type`. 
  - The default sizes of the hidden states in the GRUs are 300, but smaller values also work well (larger ones may result in over-fitting). 
  - You have to load in the data produced by the `Preprocess.py` file, e.g., including `Friends_data.pt`, `Friends_vocab.pt`, `Friends_emodict.pt`, and `Friends_tr_emodict.pt`, as well as the dataset name `Friends`. 
  - The argument `-embedding` is optional that you can load in the embeddings saved by the first run or the implementation will initialize it every time (which is time-consuming).
  - There are some other arguments in the `EmoMain.py` file, e.g., the decay rate for learning rate, the patience for early stopping. You can find out and change them if necessary.

## Public Impact

### Citation
Please kindly cite our paper:
```ruby
@inproceedings{jiao2019higru,
  title     = {HiGRU: Hierarchical Gated Recurrent Units for Utterance-Level Emotion Recognition},
  author    = {Jiao, Wenxiang and Yang, Haiqin and King, Irwin and Lyu, Michael R},
  booktitle = {Proceedings of the 2019 Conference of the North American Chapter 
               of the Association for Computational Linguistics: 
               Human Language Technologies, Volume 1 (Long and Short Papers)},
  pages     = {397--406},
  year      = {2019}
}
```

### Interesting Variants of HiGRUs
Below we selectively list some variants of our HiGRUs developed by other researchers:
- **Bert-HiGRU**: Keeping Up Appearances: Computational Modeling of Face Acts in Persuasion Oriented Discussions. [[paper]](https://arxiv.org/pdf/2009.10815.pdf)
- **HiTransformer**: Hierarchical Transformer Network for Utterance-level Emotion Recognition. [[paper]](https://arxiv.org/pdf/2002.07551.pdf)
- **HAN-ReGRU**: Hierarchical attention network with residual gated recurrent unit for emotion recognition in conversation. [[paper]](https://link.springer.com/article/10.1007%2Fs00521-020-05063-7)


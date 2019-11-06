# Assignment 2 of 11-731 "Machine Translation and Sequence-to-sequence Models" Fall 2019

This repo forked from the [baseline implementation](https://github.com/pmichel31415/11731-assignment-2-baseline) of a Transformer [(Vaswani et al, 2019)](https://arxiv.org/abs/1706.03762) model. We tried several methods tailored for the low-resource machine translation tasks to get better results on dev set.

## Group Member
* **Jin Cao (jincao)** - *Label smoothing, teacher forcing, preprocessing for parallel corpus and fine tuned hyperparameters* - [Jin](https://github.com/jin0726)
* **Kaiyu Zheng (kaiyuz)** - *Back translation, preprocessing for parallel corpus, fine tuned hyperparameters* - [Kevin](https://github.com/Kevin19961023)
* **Xinyu Chang (xinyuc)** - *Transfer learning, preprocessing for parallel corpus, fine tuned hyperparameters* - [Xinyu](https://github.com/WhiplashCxy)

## Our Results
The translated files are included under /output directory. Here is a summary of our test results upon the dev set, and a comparison to the upper bound of baseline results.

||en-af|en-ts|en-nso|
|-|-|-|-|
|Dev|39.05|38.92|19.81|
|Baseline+|6.58|4.78|1.66

## Requirements

This code was written for python >=3.6 and pytorch 1.1, although higher versions of pytorch might also work. You will need a few additional packages. Here's how you can set up the environment (assuming you have python >=3.6):

```bash
# [OPTIONAL ]Setup virtual environment
pip install virtualenv
virtualenv env
source env/bin/activate
# Install pytorch 1.1.0 (you can also try the latest version although the code hasn't been tested with it)
pip install torch==1.1.0
# Install TQDM (for fancy progress bars)
pip install tqdm
# Install sentencepiece (this is used for subword segmentation)
pip install sentencepiece
# Install sacrebleu (this is used to evaluate BLEU score on detokenized text)
pip install sacrebleu
```

## Preparing the Data

The data has been compiled from 3 language pairs from the [Ukuxhumana](https://github.com/LauraMartinus/ukuxhumana) dataset. You are tasked with translating from English to 3 low resource South African languages: Afrikaans (af), Xitsonga (ts) and Northern Sotho (nso). Download and extract the data in the root of this repo:


```bash
wget http://www.phontron.com/data/cs11731-2019-assignment2.zip
unzip cs11731-2019-assignment2.zip
```

For my baseline I learned a joint BPE model of vocabulary size 8000 for each language pair. The code for all things related to subwords is in `baseline/subwords.py`, take a look to understand how it works. You can reproduce my preprocessing by running `bash scripts/prepare_bpe.sh`.

## Training the Baseline

You can train a baseline system for each language pair by running the commands in `scripts/train_{af,ts,nso}.sh`. Depending on the language pair the models should take between 1 to 3 hours to train, although of course this may vary depending on your GPU.

## Baseline Results

As shown in the training scripts, you can decode from a trained model by running `python baseline/translate.py` with the appropriate arguments. BLEU score is evaluated by calling

```bash
cat [out file] | sacrebleu -w 2 [ref file]
```

Note that both output and reference file should be detokenized. Sacrebleu will perform its own tokenization. This is to make sure that the BLEU scores are comparable irrespective of the tokenization you use in your model. The `-w 2` means that we want BLEU score with 2 decimals, although keep in mind that BLEU differences smaller than 0.5 are unlikely to be statistically significant (on a scale of 100).

For the simple baselines provided here, the scores are as follows:

||en-af|en-ts|en-nso|
|-|-|-|-|
|Dev|31.39|32.10|18.27|
|Test|31.38|33.27|21.20|

### Variance in Scores

Since there is some variance in the final BLEU score depending on the random seed/environment, we also provide the average BLEU over 5 random reruns for each language pair (standard deviation in parentheses):

||en-af|en-ts|en-nso|
|-|-|-|-|
|Dev|31.12 (±1.35)|32.86 (±1.28)|17.68 (±0.47)|
|Test|30.80 (±0.76)|32.63 (±1.31)|21.14 (±0.44)|


---

This code is adapted from a tutorial I wrote for the JSALT 2019 summer school: https://github.com/pmichel31415/jsalt-2019-mt-tutorial

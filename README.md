# Relation Extraction Using Word Embeddings

This library was created as implementation of our method of semantic relationship extraction as a part of my master's thesis.
The goal of our method is to take small set of exemplary pairs defining semantic relation and expand it with new pairs.
This *seed* set can be for example:

> France-Paris, Italy-Rome, Germany-Berlin

Our goal here would be to find new pairs of countries and their respective capitals. Details about this method can be found in [the thesis](thesis.pdf) (in Slovak). We hope we can provide accepted article in English soon.

## Installation

#### 1. Prerequisites

You need to have these libraries installed to run this library. We are indicating versions we are using.

* *Python* (2.7.6)
* *[gensim](https://radimrehurek.com/gensim/)* (0.12.4) library
* *NumPy* (1.11.0) and *SciPy* (0.13.3) libraries (they are also prerequisites of *gensim* so you should have them installed already)

#### 2. Configuration

You can configure several options in our *config.py* file:

* **word2vec_file** - Path to file with word embeddings dataset. We are using binary format used also by word2vec or gensim libraries. Well trained datasets can be found on official [word2vec page](https://code.google.com/archive/p/word2vec/). You need to download such file for our method to work. We were using [Google News dataset](https://drive.google.com/file/d/0B7XkCwpI5KDYNlNUTTlSS21pQmM/edit?usp=sharing) (1.5GB).
* **svm_folder** - Path to folder where files generated by SVM algorithm will be saved. This folder will be used only when running our method with *PU Learning* rating algorithm.
* **default_output_file** - This is where results from our method are being written by default.

## Usage

Method can be accesed through command line by invoking *method.py* file:

```
usage: method.py [-h] [-o OUTPUT] [-t RESULT_COUNT] [-m {1,2,3}]
                 [-n NEIGHBORHOOD] [-d {1,2}] [-s {1,2,3}]
                 input_file
```

Input file is a text file where there is one pairs of exemplary words on each line (examples are in *relations* folder in this repo), e.g.:

```
France Paris
Germany Berlin
Spain Madrid
```

The options are:

```
positional arguments:
  input_file      filename of seed set file

optional arguments:
  -h, --help       show this help message and exit
  -o OUTPUT        filename where results will be written
  -t RESULT_COUNT  number of results returned
  -m {1,2,3}       rating method (1 - avg, 2 - max, 3 - pu)
  -n NEIGHBORHOOD  number of neighbours selected when generating candidates
  -d {1,2}         similarity measure (1 - euclidean, 2 - cosine)
  -s {1,2,3}       normalization method applied to avg method (1 - none, 2 -
                   standard, 3 - softmax)
```

You can also calculate certain statistics from seed sets. These are also mentioned in more detail in the thesis. This code shows how can we work with seed sets in our library. **Warning:** Importing embeddings file will result in loading of word embeddings vector dataset. This could take up to several minutes depending on the size of dataset and the hardware.

```python
import embeddings as embs
seed_set = embs.PairSet.create_from_file(input_file)
set_recall = seed_set.seed_recall()
```

## Results

Result files have similar format to input files. There is however correctness flag at the start of the line:
```
?    Russia Moscow
?    Germany Paris
?    London England
```
When the file is generated the flag is set to *?*. Human evaulator can change these flags to:

* **r** - if given pair is correct
* **p** - if it is partially correct
* **w** - if it is incorrect
 
After such evaluation we can process this file with *ResultFile* class and obtain several statistic about the succes rate of our method:

```python
import result_file as resf
result_file = resf.ResultFile(output_file)
ndcg = result_file.ndcg()
```


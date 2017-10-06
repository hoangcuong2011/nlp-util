# nlp-util
Random utilities for NLP. Many of them were designed for MT (Machine Translation) experiments, but they can still be used for general purposes.

## Table of Contents

- [Corpus Preprocessing](#corpus-preprocessing)
  * [ICWSM 2009 Spinn3r Blog Dataset](#icwsm-2009-spinn3r-blog-dataset)
  * [MSLT (Microsoft Speech Language Translation)](#mslt-microsoft-speech-language-translation)
  * [PPDB (Paraphrase Database)](#ppdb-paraphrase-database)
- [Statistics and Analysis](#statistics-and-analysis)
  * [Word Count](#word-count)
  * [Probability Histogram](#probability-histogram)
  * [Sequence Diff](#sequence-diff)

## Corpus Preprocessing

| Corpus Name | Script | Description |
| :---------- | :----- | :---------- |
| ICWSM 2009 Spinn3r Blog Dataset | Spinn3r-2009-extract.py | Extract select (and clean) text |
| MSLT (Microsoft Speech Language Translation) | MSLT-repack.sh, MSLT-extract.py | Extract monolingual/parallel data |
| PPDB (Paraphrase Database) | PPDB-extract.py | Extract select paraphrases |

#### [ICWSM 2009 Spinn3r Blog Dataset](http://www.icwsm.org/data/)
```
Usage:    Spinn3r-2009-extract.py [-h] -f FILE [FILE ...] [-l LANGUAGES [LANGUAGES ...]]
                                  -e ELEMENTS [ELEMENTS ...] [-u] [-c]
Examples: python Spinn3r-2009-extract.py -f BLOGS-tiergroup-1.tar.gz -e title description -l en -u -c > output.en
Optional arguments:
  -f FILE [FILE ...], --file FILE [FILE ...]
                        Spinn3r tar.gz file(s)
  -l LANGUAGES [LANGUAGES ...], --languages LANGUAGES [LANGUAGES ...]
                        language(s) to be extracted (e.g. en)
  -e ELEMENTS [ELEMENTS ...], --elements ELEMENTS [ELEMENTS ...]
                        element(s) to be extracted (e.g. title, description)
  -u, --unescape        unescape text (e.g. "&amp;"->"&") (default: False)
  -c, --clean           clean text (drop <*>/URLs, condense spaces) (default: False)
```

#### [MSLT (Microsoft Speech Language Translation)](https://github.com/MicrosoftTranslator/MSLT-Corpus)
1. Repack MSLT text (Python has an issue in handling original zip file).
```
bash MSLT-repack.sh /absolute/path/to/MSLT_Corpus.zip
```
2. Extract parallel or monolingual data from MSLT_Corpus.tgz
```
Usage:    MSLT-extract.py -f FILE -s SOURCE [-t TARGET] [-c CATEGORY] [-o OUTPUT]
Examples: python MSLT-extract.py -f MSLT_Corpus.tgz -s fr -t en -c dev -o MSLT.fr-en
          python MSLT-extract.py -f MSLT_Corpus.tgz -s fr > MSLT.fr
Optional arguments:
  -f FILE, --file FILE  input repacked tgz file
  -s SOURCE, --source SOURCE
                        source language (e.g. fr)
  -t TARGET, --target TARGET
                        target language (e.g. en)
  -c CATEGORY, --category CATEGORY
                        dev or test? (default: dev)
  -o OUTPUT, --output OUTPUT
                        output file (used for parallel data)
```

#### [PPDB (Paraphrase Database)](http://paraphrase.org/#/download)
```
Usage:    PPDB-extract.py [-f FILE] [-a FEATURE] [-t THRESHOLD] [-e ENTAILMENT]
Examples: gzip -dc ppdb-2.0-s-lexical.gz | python PPDB-extract.py -e Equivalence > output
Optional arguments:
  -f FILE, --file FILE  unzipped input file(s) (glob patterns are supported)
  -a FEATURE, --feature FEATURE
                        the feature used for filtering
  -t THRESHOLD, --threshold THRESHOLD
                        the threshold used for filtering (feature value >= threshold are kept)
  -e ENTAILMENT, --entailment ENTAILMENT
                        the entailment type(s) used for filtering (regular expression)
```

## Statistics and Analysis

| Script | Description |
| :----- | :---------- |
| word-count.py | Count words |
| probability-histogram.py | Generate a probability histogram |
| sequence-diff.py | Compare sequences and display diffs |

#### Word Count
```
Sample output:

,	63751725
.	61725497
the	60873114
to	35743675
and	34360371
a	29438769
of	28104862
i	27116174
in	20234743
"	16950626
```
```
Usage:   word-count.py [-i INPUT] [-w WHITE_LIST] [-b BLACK_LIST] [-s]
Example: cat file | python word-count.py -w list -s > output
Optional arguments:
  -i INPUT, --input INPUT
                        input file(s) (glob patterns are supported)
  -w WHITE_LIST, --white-list WHITE_LIST
                        only count words in the write list
  -b BLACK_LIST, --black-list BLACK_LIST
                        ignore words in the black list
  -s, --statistics      print statistics (default: False)
```

#### Probability Histogram
- Dependency: [NumPy](http://www.numpy.org/)
```
Sample output:

-1.0	0.015589
-0.8	0.047416
-0.6	0.077869
-0.4	0.137002
-0.2	0.195826
0.0	0.102647
0.2	0.114418
0.4	0.134427
0.6	0.131316
0.8	0.043490
1.0
```
![plot of probability-histogram](figures/probability-histogram.svg)
```
Usage:   probability-histogram.py [-i INPUT] [-c COLUMN] [-n] [-l LOWER] [-u UPPER] [-b BINS] [-p]
Example: cat file | python probability-histogram.py -c 1 -n -p
Optional arguments:
  -i INPUT, --input INPUT
                        input file(s) (glob patterns are supported)
  -c COLUMN, --column COLUMN
                        the index of column that contains values (default: 0)
  -n, --normalize       normalize scores to [-1,1] (default: False)
  -l LOWER, --lower LOWER
                        the lower range of bins
  -u UPPER, --upper UPPER
                        the upper range of bins
  -b BINS, --bins BINS  the number of bins (default: 10)
  -p, --plot            plot the histogram (default: False)
```

#### Sequence Diff
```
Sample output:

1 CONST-0	you can remove it .
....................................................................................................
1 SEQUE-0	you can take it off .
1 SEQUE-1	you can withdraw .
====================================================================================================
3 CONST-0	but , let 's face it , underachiever , dead @-@ end life , okay ?
....................................................................................................
3 SEQUE-0	let us be frank . he 's got a lousy job , he ain 't got no prospects .
           	                     ^  ----                 -----------           -
3 SEQUE-1	let us be frank . he has a lousy job , he no longer has any prospect .
           	                     ^^                     +++++++++++++++
====================================================================================================
```
```
Usage:   sequence-diff.py -f FILE [FILE ...] [-c CONST [CONST ...]]
Example: python sequence-diff.py -f file1 file2 -c file3 > output
Optional arguments:
  -f FILE [FILE ...], --file FILE [FILE ...]
                        input files of sequences to be compared
                        (the first file is the base to be compared with, such as reference translations)
  -c CONST [CONST ...], --const CONST [CONST ...]
                        files of sequences not participating in comparison,
                        such as source sentences to be translated
```
# Nazgul
Nazgul is a neural translation service built on top of Marian (formerly known as AmuNMT). Marcin Junczys-Dowmunt, Tomasz Dwojak, Hieu Hoang (2016). Is Neural Machine Translation Ready for Deployment? A Case Study on 30 Translation Directions (https://arxiv.org/abs/1610.01108). We use the following AmuNMT clone to produce attention info: https://github.com/barvins/amunmt

Nazgul can be used separately, but it pairs up nicely with Sauron: https://github.com/TartuNLP/sauron

## Dependencies

NLTK
 
    pip install nltk

translating needs following NLTK modules: perluniprops, punkt, nonbreaking_prefixes
   
    $ python
    >>> import nltk
    >>> nltk.download()
    Downloader> d perluniprops
    Downloader> d punkt
    Downloader> d nonbreaking_prefixes

## How to run
Download and compile AmuNMT according to instructions from https://github.com/barvins/amunmt
    
To run the nazgul server:
    
    python nazgul_json.py -c config.yml -e truecase.mdl -s 12345 -f fasttext.mdl
    
Flags:
 
    -c -- config file to be used for AmuNMT run
    -e -- truecasing model file
    -s -- socket on which the server will listen (default: 12345)
    -f -- FastText model for input clustering -- only to be used for unsupervised nazgul

## Configuration file 

An example cofiguration (for more info see https://github.com/barvins/amunmt):

    # Paths are relative to config file location
    relative-paths: yes

    # performance settings
    beam-size: 5
    normalize: yes
    cpu-threads: 4
    gpu-threads: 0

    # scorer configuration
    scorers:
      F0:
        path: model.npz
        type: Nematus

    # scorer weights
    weights:
      F0: 1.0

    # vocabularies
    source-vocab: vocab.source.bped.json
    target-vocab: vocab.target.bped.json

    # bpe, disable de-bpe
    bpe: codes.bpe
    no-debpe: yes
    
    # To produce attention info
    return-alignment: yes
 
## Input and output while serving

### Input
Desired input depends on with which flags and configuration file options the service is started.
When all options are enabled, the input can be just regular text, because service sentence- and word-tokenizes, splits into bpe, truecases and after translation turns the output into proper text.

### Output
Output is in json format. Following serves as a sample of output format that is sent to client.

    msg = json.dumps({'raw_trans': trans,
                      'raw_input': raw_in,
                      'weights': weights,
                      'final_trans': detokenized.replace('@@ ', '').encode('utf-8').strip()
                      }, encoding='utf-8')
    c.send(msg)

## Testing
For testing a simple python testing script is in the same folder with server file (test_nazgul_json.py).

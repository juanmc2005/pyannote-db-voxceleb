# VoxCeleb plugin for pyannote.database

This package provides an implementation of the speaker verification protocols used in the `VoxCeleb` paper.


## Citation

Please cite the following reference if your research relies on the `VoxCeleb` dataset:

```bibtex
@InProceedings{VoxCeleb,
  author = {Nagrani, A. and Chung, J.~S. and Zisserman, A.},
  title = {{VoxCeleb: a large-scale speaker identification dataset}},
  booktitle = {{Interspeech 2017, 18th Annual Conference of the International Speech Communication Association}},
  year = {2017},
  month = {August},
  address = {Stockholm, Sweden},
  url = {http://www.robots.ox.ac.uk/~vgg/data/voxceleb/},
}
```

Please cite the following references if your research relies on this package. This is where the whole `pyannote.database` framework was first introduced:

```bibtex
@inproceedings{pyannote.metrics,
  author = {Herv\'e Bredin},
  title = {{pyannote.metrics: a toolkit for reproducible evaluation, diagnostic, and error analysis of speaker diarization systems}},
  booktitle = {{Interspeech 2017, 18th Annual Conference of the International Speech Communication Association}},
  year = {2017},
  month = {August},
  address = {Stockholm, Sweden},
  url = {http://pyannote.github.io/pyannote-metrics},
}
```


## Installation

```bash
$ pip install pyannote.db.voxceleb
```


## Usage

We start by initializing `VoxCeleb2` speaker verification protocol.

```python
from pyannote.database import get_protocol
protocol = get_protocol('VoxCeleb.SpeakerVerification.VoxCeleb2')
```

One can use `protocol.train` generator to train the background model.

```python
for training_file in protocol.train():
    uri = training_file['uri']
    print('Current filename is {0}.'.format(uri))

    # "who speaks when" as a pyannote.core.Annotation instance
    annotation = training_file['annotation']
    for segment, _, speaker in annotation.itertracks(yield_label=True):
        print('{0} speaks between t={1:.1f}s and t={2:.1f}s.'.format(
            speaker, segment.start, segment.end))
   
    break  # this should obviously be replaced
           # by the actual background training
```

Use `protocol.test_trial` to iterate over all test trials:

```python
for t, trial in enumerate(protocol.test_trial()):
    
    file1 = trial['file1']    
    file2 = trial['file2']    
    same = trial['reference']
    
    msg = f"{file1['uri']} vs. {file2['uri']} {'same' if same else 'different'}"
    print(msg)
    
    if t > 10:
        break    
```

Here is how to get the list of all available speaker verification protocols.

```python
from pyannote.database import get_database
voxceleb = get_database('VoxCeleb')

for protocol_name in voxceleb.get_protocols('SpeakerVerification'):
    print(f'VoxCeleb.SpeakerVerification.{protocol_name}')
```

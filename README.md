# rcaudio : A Realtime Audio Recording & Analyze Script


## Introduction 

**rcaudio** Rcaudio is a real-time audio analysis library that allows you to simply record audio through a microphone and analyze.

It supports real-time analysis of :

* The raw audio data
* Volume
* Beat Information

For chinese documentation : [中文文档](http://mhy12345.xyz/technology/python-beat-detection/)

## Usage

### CoreRecorder

`CoreRecorder` is used to fetch raw data. When started, the audio data will be stored in the `CoreRecorder.buffer`.

```python
from rcaudio import CoreRecorder
CR = CoreRecorder(
        time = 10, #How much time to record
        sr = 1000 #sample rate
        )
CR.start()
while True:
    if not CR.buffer.empty():
        x = CR.buffer.get()
        print('*'*int(abs(x)))
```

### SimpleRecorder

In most cases, we use `SimpleRecorder`.  For efficiency consideration, the `SimpleRecorder`  should only be instantiated once. 

This class can register several `Analyzer`. 

When the function `start()` called, It will begin to record through microphone, and refresh the status of all the `Analyzer`

### Analyzers

All class extended from `BaseAnalyzer` can be registered into `SimpleRecorder`. For example `VolumeAnalyzer` can get the current volume of the microphone.


```python
SR = SimpleRecorder()
VA = VolumeAnalyzer(rec_time = 1)
SR.register(VA)
SR.start()
while True:
    print("VOLUME : ",VA.get_volume())
    time.sleep(1)
```

And beat analyzer can predict the beats from the music. (However, there will be some delay.)

```python
SR = SimpleRecorder(sr = 20000)
BA = BeatAnalyzer(rec_time = 15, initial_bpm = 120, smooth_ratio = .8)
SR.register(BA)
SR.start()
while True:
    print(BA.block_until_next_beat())
```


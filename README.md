# musical-key-finder
A python project that uses Librosa and other libraries to analyze the key that a song (an .mp3) is in, i.e. F major or C# minor, using the Krumhansl-Schmuckler key-finding algorithm.

## Analytical tools
We will first analyze part of "Une Barque sur l'Ocean," a complex piece for solo piano by French composer Maurice Ravel. Click the below to be taken to Soundcloud and hear the .mp3 file:

[<img src="unebarquesoundcloud.PNG" width="500">](https://soundcloud.com/jack-mcarthur-6407193/f-minor-segment-of-une-barque-sur-locean)

This piece has several sections with different keys, as we can learn by loading it using the Librosa library and passing it to an instance of the Tonal_Fragment class. We do this below, and output a chomagram, a chart indicating the intensity associated with each pitch class (C, C#, D, etc.) vs. time.
```python
audio_path = 'une-barque-sur-l\'ocean.mp3'
y, sr = librosa.load(audio_path)
y_harmonic, y_percussive = librosa.effects.hpss(y)

unebarque = Tonal_Fragment(y_harmonic, sr)
unebarque.chromagram("Une Barque sur l\'Ocean")
```
<img src="unebarquechromagram.png" width="600">

It is apparent that this clip has distinct sections, in which different pitch classes are most prominent; for example, the section from t=0 sec. to t=22 sec. uses the pitches C# and F# more than any other. We can determine how much each pitch class is used in that chunk with the following code:
```python
unebarque_fsharp_min = Tonal_Fragment(y_harmonic, sr, tend=22)
unebarque_fsharp_min.print_chroma()
```
```
C 	 0.101
C# 	 1.000
D 	 0.410
D# 	 0.094
E 	 0.459
F 	 0.231
F# 	 0.835
G 	 0.317
G# 	 0.366
A 	 0.405
A# 	 0.148
B 	 0.083
```
It appears that C# is used the most often, followed by F#. We can determine the key of the section from t=0 sec. to t=22 sec. with the following code:
```python
unebarque_fsharp_maj = Tonal_Fragment(y_harmonic, sr, tend=22)
unebarque_fsharp_maj.print_key()
```
```
>> likely key: F# minor, correlation: 0.762
```
The key of the song is determined with the Krumhansl-Schmuckler key-finding algorithm, which  correlates the prominence of each pitch class in a sample with typical profiles of a major key and a minor key, and returns the key with the highest correlation coefficient. In this case, the returned key of F# minor is correct, even though F# is not the most prominent pitch in this sample. Many algorithms make the assumption that the most prominent pitch in the sample is the "tonic," or the root of the key; a strength of this algorithm is that it does not make this assumption.
The correlation coefficients of the chroma data can also be printed:
```python
unebarque_fsharp_min.corr_table()
```
```
C major 	 -0.318
C# major 	  0.543
D major 	  0.285
D# major 	 -0.572
E major 	  0.179
F major 	 -0.396
F# major 	  0.524
G major 	 -0.154
G# major 	 -0.175
A major 	  0.474
A# major 	 -0.473
B major 	  0.083
C minor 	 -0.596
C# minor 	  0.630
D minor 	 -0.087
D# minor 	 -0.073
E minor 	 -0.110
F minor 	 -0.155
F# minor 	  0.762
G minor 	 -0.313
G# minor 	 -0.158
A minor 	 -0.222
A# minor 	  0.192
B minor 	  0.131
```
This shows C# minor as a clear second-best option, and allows for comparison between the goodness of fit of all keys.

This algorithm is not perfect, but it does offer warning when it gives a result with low confidence. This can be seen in the next section of the clip of "Une Barque sur l'Ocean," from t=22 sec. to t=33 sec., which is in E minor:
```python
unebarque_e_min = Tonal_Fragment(y_harmonic, sr, tstart=22, tend=33)
unebarque_e_min.print_key()
```
```
likely key: D major, correlation: 0.602
also possible: E minor, correlation: 0.59
```
Other sound clips can also be analyzed, and two more examples are given in the Jupyter notebook [musicalkeyfinder.ipynb](musicalkeyfinder.ipynb) in this repository. The Tonal_Fragment class is also saved in the file [keyfinder.py](keyfinder.py). Analysis of .mp3 files does require FFMpeg to be installed, but .wav and other files can be analyzed by Librosa without it. Analyses are most accurate if the harmonic part of the sample is separated from the percussive part, and if a reasonable length of sound (10 seconds or more) is used.

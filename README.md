# musical-key-finder
A python project that uses Librosa and other libraries to analyze the key that a song (an .mp3) is in, i.e. F major or C# minor, using the Krumhansl-Schmuckler key-finding algorithm.

# Analytical tools
Click the below to be taken to Soundcloud and hear the .mp3 file:
[<img src="unebarquesoundcloud.PNG" width="500">](https://soundcloud.com/jack-mcarthur-6407193/f-minor-segment-of-une-barque-sur-locean)

This piece has several sections with different keys, as we can learn by passing it to an instance of the Tonal_Fragment class.
```
audio_path = 'une-barque-sur-l\'ocean.mp3'
y, sr = librosa.load(audio_path)
y_harmonic, y_percussive = librosa.effects.hpss(y)

unebarque = Tonal_Fragment(y_harmonic, sr)
unebarque.chromagram("Une Barque sur l\'Ocean")
```
<img src="unebarquechromagram.png" width="600">

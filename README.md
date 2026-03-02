# Audio Tone Detection and 12-TET Analysis Toolkit

## Overview

This project provides a lightweight toolkit for musical note modeling and single-tone audio analysis based on the 12-tone equal temperament (12-TET) system. It supports:

* Note-to-frequency and frequency-to-note conversion
* Configurable tuning reference (default A4 = 440 Hz)
* Audio loading and preprocessing
* Waveform, spectrogram, and pitch visualization
* Automatic pitch estimation and note classification for single-tone recordings

The implementation is built on:

* PyTorch
* torchaudio
* librosa
* Matplotlib
* NumPy
* SciPy

---


---

## Architecture

### 1. `Obj12TET`

Base class defining:

* Naming conventions (Eastern European and English)
* Chromatic adjustments (sharps and flats)
* Reference frequency calculation for C0 based on a configurable tuning standard

The default tuning assumes A4 = 440 Hz.

---

### 2. `NoteTone`

Represents a musical note in the 12-TET system.

#### Features

* Parses note labels (e.g., `A4`, `B#3`, `Db5`)
* Computes semitone distance from C0
* Converts note → frequency
* Converts frequency → nearest note
* Supports sharps and basic chromatic adjustments

#### Frequency Model

The frequency is computed as:

[
f = C_0 \cdot 2^{n/12}
]

Where:

* ( n ) is the semitone distance from C0
* ( C_0 ) is derived from the selected tuning reference

#### Example

```python
note = NoteTone("A4")
note.print()
```

---

### 3. `Audio`

Handles audio loading and visualization.

#### Capabilities

* Load waveform from file using `torchaudio.load`
* Plot waveform
* Plot spectrogram
* Detect and plot pitch
* Playback support in Jupyter environments

#### Example

```python
audio = Audio("path/to/file.wav")
audio.plot_waveform()
audio.plot_spectogram()
audio.plot_pitch()
audio.play()
```

---

### 4. `SampleAudio`

Extends `Audio` with automatic silence trimming.

The trimming is based on an energy threshold computed from the waveform. This is useful for recorded instrument samples where leading silence is present.

---

### 5. `SingleToneSample`

Specialized class for detecting the pitch of a sustained single tone.

#### Processing Pipeline

1. Load and trim audio
2. Extract pitch using `torchaudio.functional.detect_pitch_frequency`
3. Filter unrealistic values (e.g., below 15 Hz or above configurable upper bound)
4. Estimate representative frequency:

   * Median if the signal is stable
   * Kernel Density Estimation (KDE) if noisy
5. Map detected frequency to nearest 12-TET note

#### Example

```python
PATH = "Raw_Audio_Data/A2/10.wav"

tone = SingleToneSample(PATH)
tone.note.print()
tone.plot_pitch()
```

---

## Project Structure

```
.
├── Raw_Audio_Data/
│   ├── A2/
│   ├── D3/
│   └── ...
├── main.py
└── README.md
```

---

## Use Cases

* Instrument sample classification
* Pitch detection validation
* Educational demonstrations of 12-TET
* Audio dataset preprocessing
* Signal processing experiments

---

## Limitations

* Designed for monophonic, sustained tones
* Enharmonic handling is limited
* Spectrogram frequency scaling may require refinement for strict logarithmic accuracy
* No real-time audio input support

---

## License

MIT
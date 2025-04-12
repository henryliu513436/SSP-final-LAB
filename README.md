# Chinese Number Speech Recognition using HMM

> An implementation of Hidden Markov Models (HMM) for recognition of Chinese numbers (1-10) with various data augmentation techniques

## Project Overview

This project implements speech recognition for Chinese numbers using Hidden Markov Models. The implementation includes detailed analysis of the test dataset distribution, strategic creation of a balanced training dataset, and application of multiple data augmentation techniques to enhance model performance.


## Objectives

- Analyze the test dataset distribution (gender ratio, audio duration, digit combinations)
- Generate a well-balanced training dataset based on the test set analysis
- Enhance training dataset diversity through multiple data augmentation techniques
- Implement and compare mono-phone and tri-phone HMM models
- Achieve the lowest possible Word Error Rate (WER)

## Methodology

### 1. Test Dataset Analysis

#### Gender Distribution
- Female speakers: 8 people (23%)
- Male speakers: 24 people (77%)

#### Audio Duration Analysis
- Average duration: 5.42 seconds
- Minimum duration: 3.54 seconds
- Maximum duration: 13.00 seconds
- Found that 78% of files have a duration < 6 seconds
- Identified one outlier with robot-like voice characteristics

#### Digit Occurrence Analysis
We performed both individual digit and sequential pattern analysis:

**Individual Digit Occurrences:**
| Digit | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|-------|---|---|---|---|---|---|---|---|---|---|
| Count | 44| 95| 34| 17| 13| 25| 34| 4 | 6 | 14|
| Rate  |15%|33%|12%| 6%| 5%| 9%|12%| 1%| 2%| 5%|

**Sequential Patterns:**
- First digit must be: 1 (100%)
- Second digit: 1 (80%), 0 (20%)
- Third digit: 2 (61%), 9 (16%), 0 (10%), 1 (10%), 8 (3%)
- Identified probabilities for 2-3 digit, 4-5 digit, and full 5-digit sequences

### 2. Training Dataset Creation

Two approaches were used to create the training dataset:
1. Simple count-based approach using individual digit occurrence probabilities
2. Sequential-based approach respecting the observed digit sequence rules

All digit combinations maintained a male-to-female ratio of 2:1 to better reflect the test set distribution while reducing gender bias.

### 3. Data Augmentation

We implemented three primary data augmentation techniques to enhance dataset diversity:

#### Pitch Shifting & Speed Modification
Audio files were speed-adjusted, with pitch shifting to compensate for frequency changes:
- For speed ratio > 0: Pitch_adjust = -12 * log₂(speed ratio)
- For speed ratio < 0: Pitch_adjust = 12 * log₂(speed ratio)

#### Voice Activity Detection (VAD)
- Audio files were segmented into 10ms frames
- Applied 10ms padding to effectively remove silence
- Enhanced signal-to-noise ratio and reduced processing of non-speech segments

#### Voice Conversion
- Applied target speaker ID '0084' (male, age 20-24) to create voice-converted versions
- Generated 1000 additional voice-converted audio files

### 4. Training Dataset Combination

We created multiple training sets using different combinations of augmentation techniques:

| Data Assembly Method | Digit Length | Speed/Pitch | Remove Silence | Voice Conversion | Files |
|---------------------|--------------|-------------|----------------|------------------|-------|
| Method 1            | 9            | No          | No             | No               | 1000  |
| Method 2            | 9            | Yes         | No             | No               | 1000  |
| Method 2            | 9            | Yes         | Yes            | No               | 1000  |
| Method 2            | 9            | Yes         | Yes            | Yes              | 1000  |

This resulted in a total of 4000 training audio files.

### 5. Model Training

HMM models were trained with the following parameters:
- Number of iterations: 160
- Last iteration to increase Gaussians: 120
- Target number of Gaussians: 300

Both mono-phone and tri-phone models were implemented and compared.

## Results

Our experiments yielded progressive improvements in Word Error Rate (WER):

| Trial | WER    | Description |
|-------|--------|-------------|
| 1     | 39.7%  | Initial model |
| 2     | 33.6%  | With pitch/speed augmentation |
| 3     | 31.4%  | With VAD and pitch/speed |
| 4     | 22.0%  | With all augmentation techniques |

**Best Model Performance:**
```
Decode results are in paths:phone_numbers_decode
%WER 22.02 [ 61 / 277, 4 ins, 37 del, 20 sub ] exp/my_mono2/phone_numbers_decode_result/wer_16_0.0
%WER 22.02 [ 61 / 277, 4 ins, 37 del, 20 sub ] exp/my_mono2/phone_numbers_decode_result/wer_16_0.0
```

## Key Findings

1. **Mono-phone vs. Tri-phone Models:**
   - Despite theoretical advantages, tri-phone models did not outperform mono-phone models
   - Possible reasons include:
     - Small and limited phoneme set for Chinese numbers 1-10
     - Relatively independent and stable pronunciations of Chinese numbers
     - Mono-phone models have fewer parameters, reducing overfitting on small datasets

2. **Data Augmentation Impact:**
   - Each augmentation technique contributed to performance improvement
   - The combination of all techniques yielded the best results (22% WER)
   - Voice conversion provided significant benefits despite using a single target speaker

3. **Sequential Pattern Importance:**
   - Respecting the sequential digit patterns observed in the test set was crucial
   - Generated training data maintained similar digit sequence distributions

## Installation & Usage

### Prerequisites
- [Kaldi Speech Recognition Toolkit](https://kaldi-asr.org/doc/install.html)
- Python 3.6+
- SoX (Sound eXchange)
- librosa
- numpy


## Future Work

1. **Advanced Data Augmentation:**
   - Explore SpecAugment for spectrogram augmentation
   - Implement more diverse voice conversion with multiple target speakers
   - Investigate noise addition to improve robustness

2. **Model Improvements:**
   - Experiment with DNN-HMM hybrid models
   - Test end-to-end neural networks (e.g., Transformer-based models)
   - Optimize the number of Gaussians for each phone individually

3. **Feature Extraction Refinements:**
   - Test different MFCC configurations
   - Explore i-vectors for speaker adaptation
   - Implement vocal tract length normalization

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- The instructors and TAs of the Speech Signal Processing course
- [Kaldi Speech Recognition Toolkit](https://kaldi-asr.org/) team

# BLDC Motor Diagnostic System - MATLAB Implementation

---

## Project Description

A comprehensive BLDC motor fault diagnostic system based on current signal analysis using **four feature extraction methods** and **33 machine learning models**.

---

## File Structure

### Main Files

- **`main.m`** - main script managing the feature extraction process  
- **`create_ens_for_method.m`** - configuration of fileEnsembleDatastore for Diagnostic Feature Designer  
- **`generator_wykresow.m`** - generating plots of ML model results  
- **`tree_converter.m`** - decision tree model converter from MATLAB to C code for ESP32S3  

### Feature Extraction Functions

- **`extract_time_domain_features.m`** - Method 1: time-domain analysis + band-pass filtering  
- **`extract_ar_features_durand_kerner.m`** - Method 2: AR(10) model + Durand-Kerner algorithm  
- **`extract_fft_envelope_features.m`** - Method 3: FFT + envelope analysis  
- **`extract_wavelet_features.m`** - Method 4: wavelet transform + Fisher Ratio  

---

## Signal Analysis Methods

### Method 1: Time-Domain Analysis with Band-Pass Filtering

**Number of features:** 49  

**Frequency bands:**
- 100-500 Hz  
- 500-2000 Hz  
- 2000-5000 Hz  
- 5000-10000 Hz  
- 10000-15000 Hz  
- 15000-20000 Hz  

**Time-domain features:**
- Mean  
- Standard deviation  
- RMS  
- Maximum  
- Peak-to-peak amplitude  
- Skewness  
- Kurtosis  
- Crest factor  

**Spectral features:**
- Spectral centroid  
- Spectral power  
- Harmonic amplitudes (1x, 2x, 3x)  

---

### Method 2: AR Model with Durand-Kerner Algorithm

**Number of features:** 120  

**AR model order:** p=10  

**Features:**
- AIC (Akaike Information Criterion)  
- MAE (Mean Absolute Error)  
- Dominant frequency  
- Damping  
- RMS of residuals  
- Energy of IMF1  

---

### Method 3: FFT with Envelope Analysis

**Number of features:** 96  

**Process:** FFT → band-pass filtering → Hilbert transform → envelope FFT  

**Features per band:**
- Spectral energy  
- 3 dominant frequencies  
- Amplitudes  
- RMS  
- Peak value  
- Envelope energy  

---

### Method 4: Wavelet Transform

**Number of features:** 54  

**Wavelet:** db4 (Daubechies 4)  

**Decomposition levels:** 8 + trending  

**Features per level:**
- Energy  
- RMS  
- Kurtosis  
- Entropy  
- Skewness  
- Peak2RMS  

---

## Motor State Classification

The system recognizes **4 BLDC motor states**:

1. **Healthy**  
2. **Mechanical Damage** (Mech_Damage)  
3. **Electrical Damage** (Elec_Damage)  
4. **Combined Mechanical & Electrical Damage** (Mech_Elec_Damage)  

---

## Machine Learning Models

The system tests **33 models** using MATLAB Classification Learner:

### Decision Trees

- Fine Tree  
- Medium Tree  
- Coarse Tree  

### Discriminant Analysis

- Linear Discriminant  
- Quadratic Discriminant  

### Efficiently Trained Linear Classifiers

- Efficient Logistic Regression  
- Efficient Linear SVM  

### Naive Bayes Classifiers

- Gaussian Naive Bayes  
- Kernel Naive Bayes  

### Support Vector Machines

- Linear SVM  
- Quadratic SVM  
- Cubic SVM  
- Fine Gaussian SVM  
- Medium Gaussian SVM  
- Coarse Gaussian SVM  

### Nearest Neighbor Classifiers

- Fine KNN  
- Medium KNN  
- Coarse KNN  
- Cosine KNN  
- Cubic KNN  
- Weighted KNN  

### Ensemble Classifiers

- Boosted Trees  
- Bagged Trees  
- Subspace Discriminant  
- Subspace KNN  
- RUSBoosted Trees  

### Neural Network Classifiers

- Narrow Neural Network  
- Medium Neural Network  
- Wide Neural Network  
- Bilayered Neural Network  
- Trilayered Neural Network  

### Kernel Approximation Classifiers

- SVM Kernel  
- Logistic Regression Kernel  

---

## Output Folder Structure

```
FeatureTables/
├── Train/              # Training feature sets
├── Test/               # Test feature sets
├── Robustness/         # Robustness tests (7 scenarios)
└── Combined/           # Combined features of all methods

DFD_Method[1-4]/        # Data for Diagnostic Feature Designer
├── train/              # Training members
└── test/               # Test members

ML_Features_C_Exact/    # Features compatible with C implementation on ESP32

Analysis_Plots/         # Analytical plots

Robustness_Signals/     # Signals for robustness tests
```

---

## Usage Instructions

### 1. Run Main Script

```matlab
% Run main script
main
```

The script will interactively ask for:
- Selection of methods to run (1-4 or all)  
- Enabling training data augmentation  
- Generating robustness tests (7 scenarios)  
- Feature importance analysis  

---

### 2. Generate Data for Diagnostic Feature Designer

```matlab
% Configure ensemble datastore for selected method
ens_train = create_ens_for_method(1, 'train');
ens_test = create_ens_for_method(1, 'test');

% Open Diagnostic Feature Designer
diagnosticFeatureDesigner(ens_train);
```

---

### 3. Train Machine Learning Models

```matlab
% Load training data
load('FeatureTables/Train/M1_time_domain_train.mat');

% Launch Classification Learner
classificationLearner
```

---

### 4. Convert Decision Tree Model to C

```matlab
% Train decision tree in Classification Learner
% Export model as 'trainedModel' to workspace
% Run converter
tree_converter

% Script will generate tree_model.h and tree_model.c
% ready for implementation on ESP32S3
```

**Conversion Notes:**
- Feature mapping must follow the order in MotorFeatures15  
- Threshold values are Z-score normalized  
- Generated files contain full C implementation of the decision tree  
- Code includes debugging functions showing decision path  

---

### 5. Robustness Tests

Automatically generated by `main.m`  

Files available in `FeatureTables/Robustness/`  

**Test scenarios:**
- Frequency shift  
- Amplitude fluctuation  
- Noise  
- Combined  
- Extreme amplitude  
- Unknown distribution  

---

### 6. Generate Analytical Plots

```matlab
% Generate 4 analytical plots of ML results
generator_wykresow
```

---

## Technical Parameters

| Parameter | Value |
|----------|---------|
| Sampling frequency | 50 kHz |
| Samples per experiment | 32768 (2^15) |
| Training set | 176 signals |
| After augmentation | 3344 signals |
| Test set | 44 signals |
| Normalization | Z-score based on training set |
| Cross-validation | 5 folds (after augmentation 10) |

---

## Measurement Data

DUDU-BLDC dataset was used for system validation (https://explore.openaire.eu/search/result?pid=10.5281%2Fzenodo.15522163)  

## Included ML Models

- **Kernel_Naive_Bayes.mat** - best model of Method 3 after data augmentation  
- **fine_tree_model.mat** - one of the best models of Method 2 (implemented on microcontroller)  

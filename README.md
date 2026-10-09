# Drought Identification from GNSS Time Series (ISPRS 2026)

MATLAB code for the paper:

Esfandyari Kaloukan, M., Malihi, S., Iran-Pour, S., Shokri, D., Homayouni, S.
(2026). Drought Identification and Prediction from GNSS Time Series Using SSA
and Hybrid CNN-Transformer. ISPRS Annals of the Photogrammetry, Remote
Sensing and Spatial Information Sciences, XI-3-2026, 625-630.
https://doi.org/10.5194/isprs-annals-XI-3-2026-625-2026

The idea in one paragraph: the vertical displacement of a GNSS station is
sensitive to hydrological mass loading, so drought and wet years leave a
signature in it. The daily vertical series of station P242 (Santa Cruz
County, California) is cleaned, decomposed with singular spectrum analysis
into trend, annual and semi-annual components, and yearly features (mean,
energy, amplitude of each component) are extracted. The years are labeled
drought, normal or wet with the SPEI index, and a hybrid CNN plus Transformer
classifier is trained on the features. The model reaches 83.3 percent overall
accuracy with an F1 of 0.90 for the drought class, and its 2024-2025 labels
match the U.S. Drought Monitor.

## Files

- PreProcessing.m: gap filling, white plus flicker noise estimation (LS-VCE),
  A-mode offset detection, outlier screening and SSA decomposition of the
  P242 vertical series
- FeatureExtraction.m: yearly mean, energy and amplitude of the SSA trend,
  annual and semi-annual components (the features of Table 2)
- Labeling.m: merges the features with the yearly mean SPEI (Climate Engine
  gridMET export) and labels each year drought, normal or wet
- CNNTransformer.m: the main classifier of the paper, training, evaluation
  and prediction on new years
- DroughtClassifierNet.m: fully connected classifier, reports accuracy and
  the confusion matrix, and saves the trained network
- Oversampling.m: class balancing and a fully connected baseline network
- BaselineMLP_BiLSTM.m: a second baseline, MLP plus BiLSTM with k-fold
  evaluation
- SyntheticSignalsDemo.m: synthetic drought, rainfall and earthquake signals
  used for the concept figures
- lib/: shared routines (offset detection, LS-VCE, noise cofactor matrices,
  SSA smoothing and helpers)

## Data

The raw P242 series (cwu, IGS14 frame) can be downloaded from the
NOTA/UNAVCO archive. The feature table final_dataset_with_labels.csv is
produced by the preprocessing and feature extraction steps, with SPEI labels
added; the SPEI records are available from the global SPEI database. File
paths at the top of the scripts have to be adapted to your setup.

## Requirements

MATLAB with the Deep Learning Toolbox and the Statistics Toolbox. Two things
to know before running:

1. transformerLayer in CNNTransformer.m is not a built-in MATLAB layer. Use
   the custom implementation it was written for, or replace it with
   selfAttentionLayer (available since R2023a) with equivalent settings.
2. The train-test split in the scripts uses a random HoldOut partition. The
   evaluation reported in the paper uses a chronological 70/30 split, so for
   reproducing the paper numbers split the samples by year instead of
   randomly.

## Python version

The python folder contains a full Python port of the pipeline (preprocessing
with SSA, feature extraction, SPEI labeling and a PyTorch CNN-Transformer).
It needs no custom layers, uses a chronological 70/30 split as described in
the paper, and runs with the packages in python/requirements.txt. See
python/README.md for the run order.

## Credits

ssadow.m in lib/ is the SSA routine by F. J. Alonso Sanchez (University of
Extremadura), included with its original documentation. The LS-VCE and offset
detection routines follow Amiri-Simkooei (2007). The rest of the code is my
own work.

Motahareh Esfandyari-Kaloukan

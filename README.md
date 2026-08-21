## HyperLeaf2024, Pixel Set Deep Sets with Regional Attention Pooling

## Competition
HyperLeaf2024 is a Kaggle competition (link: https://www.kaggle.com/competitions/HyperLeaf2024) built on hyperspectral images of wheat flag leaves. 2410 images in total, 1590 for training (24 field plots) and 820 for testing (12 different plots, never seen in training). The goal is to predict 8 targets per image from the raw hyperspectral cube: 3 continuous physiological traits (GrainWeight, Gsw, PhiPS2), Fertilizer level (0.0, 0.5 or 1.0) and cultivar (one hot, 4 classes). The competition itself ran and closed in 2024. This work was done in 2026, after closing, so it was never a counted entry on the leaderboard.

## Approach

Each leaf is treated as an unordered set of pixel spectra, not as a resized image. Leaf size and shape vary a lot between images, and resizing to a fixed grid would distort or discard spatial information that a Deep Sets style model can use directly.

Segmentation: a pixel counts as leaf when the sum across all 204 bands is greater than zero, since the background comes out as exact zero in every band.

<img width="665" height="35" alt="image" src="https://github.com/user-attachments/assets/05c9ca39-000e-42bc-8329-4ad7d9489e63" />

Regions: the leaf is split into 5 bands along its tip to base axis (found by PCA on pixel coordinates). Each region gets its own multi head attention pooling layer, which summarizes a variable number of pixels into one fixed size vector.

<img width="589" height="70" alt="image" src="https://github.com/user-attachments/assets/e907a7ff-d8a5-4226-b98a-2cc052d88a24" />

Preprocessing: each pixel spectrum goes through a Savitzky Golay first derivative, removing additive and multiplicative baseline shifts between images while preserving absorption peak positions.

Heads: one linear regression head for the 3 continuous targets, two softmax heads for Fertilizer and cultivar. Predictions are decoded as the expected value of the softmax distribution, not rounded to a class, since the competition metric penalizes confident wrong rounding under MSE.

Training: number of epochs is chosen by 5 fold cross validation with early stopping. The final model is an ensemble of 5 seeds, averaged.

<img width="391" height="820" alt="image" src="https://github.com/user-attachments/assets/8000e1e8-0b25-4fbe-b851-e958ae105c12" />

## Results

<img width="367" height="80" alt="image" src="https://github.com/user-attachments/assets/5aaf71c7-4e57-44d2-aacc-b7cc21c025f4" />

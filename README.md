# Brain-Tumor-Segmentation-using-U-Net
Deep learning project for brain tumor segmentation in MRI scans using U-Net architecture. The model identifies tumor regions in brain MRI images from the LGG MRI Segmentation dataset.

Dataset
Source: LGG MRI Segmentation (Kaggle)
Details:

3,929 brain MRI slices with corresponding tumor masks
Image format: Grayscale .tif files
Resolution: 128x128 pixels (resized)
Patient folders: 110 patients
Each patient has multiple MRI slices with ground truth masks

Model Architecture
U-Net - Standard architecture for medical image segmentation
Structure:

Encoder: 4 convolutional blocks with max pooling
Bottleneck: 512 filters with dropout (0.5)
Decoder: 4 upsampling blocks with skip connections
Output: Single channel with sigmoid activation

Parameters:

Input shape: 128x128x1
Total parameters: ~7.8 million
Optimizer: Adam (lr=0.0001)
Loss: Weighted Binary Cross-Entropy + Dice Loss

Training Configuration

Batch size: 8
Epochs: 50
Image size: 128x128
Train/Test split: 80/20
Validation split: 20% of training data
Data augmentation: None (loaded on-the-fly)

Callbacks:

Early stopping (patience=10)
Learning rate reduction (factor=0.5, patience=5)

Loss Function
Combined loss to handle class imbalance:

Weighted Binary Cross-Entropy (10x weight on tumor pixels)
Dice Loss

Results

Training Dice Coefficient: 0.059
Validation Dice Coefficient: 0.011
Test IoU: 0.000

Note: Low scores due to extreme class imbalance (tumor pixels are very rare in images, typically <5% per image).
Challenges

Extreme Class Imbalance

Tumor regions occupy very small portions of images
Model tends to predict mostly background


Small Tumor Regions

Difficult to detect small features at 128x128 resolution


Limited Training Resources

Memory constraints required smaller image size
Shorter training time



Requirements
tensorflow>=2.10.0
numpy>=1.21.0
pandas>=1.3.0
matplotlib>=3.4.0
Pillow>=8.3.0
scikit-learn>=1.0.0
tqdm
Installation
bashgit clone https://github.com/yourusername/Brain-Tumor-Segmentation-UNet.git
cd Brain-Tumor-Segmentation-UNet
pip install -r requirements.txt
Dataset Setup

Download LGG MRI Segmentation dataset from Kaggle
Extract to: Brain Tumor/lgg-mri-segmentation/kaggle_3m/
Verify structure:

kaggle_3m/
├── TCGA_CS_4941/
│   ├── TCGA_CS_4941_1.tif
│   ├── TCGA_CS_4941_1_mask.tif
│   └── ...
├── TCGA_CS_4942/
└── ...
Usage
Training
Run the notebook cells sequentially:

Load data paths
Create data generator
Build U-Net model
Compile with weighted loss
Train model
Evaluate and visualize

Key Code
Data Generator:
pythondef data_generator(image_paths, mask_paths, batch_size=8, target_size=128):
    # Loads batches on-the-fly to save memory
    ...
Model:
pythonmodel = build_unet(input_shape=(128, 128, 1))
model.compile(optimizer=Adam(lr=0.0001), 
              loss=weighted_bce_dice_loss,
              metrics=[dice_coefficient])
Training:
pythonhistory = model.fit(train_gen, 
                   steps_per_epoch=steps_train,
                   epochs=50,
                   validation_data=val_gen)
File Structure
├── README.md
├── requirements.txt
├── brain_tumor_segmentation.ipynb
├── brain_tumor_unet_model.h5
└── segmentation_results.png
Evaluation Metrics
Dice Coefficient:

Measures overlap between prediction and ground truth
Range: 0 (no overlap) to 1 (perfect overlap)
Formula: 2 * |X ∩ Y| / (|X| + |Y|)

IoU (Intersection over Union):

Jaccard index measuring segmentation accuracy
Range: 0 to 1

Limitations

Model struggles with very small tumor regions
Performance limited by class imbalance
Requires more training data and epochs for better results
128x128 resolution may miss fine details
Not suitable for clinical use without further validation

Future Improvements

Use larger image resolution (256x256 or 512x512)
Implement advanced loss functions (Focal Loss, Tversky Loss)
Add data augmentation (rotation, flip, brightness)
Use attention mechanisms in U-Net
Ensemble multiple models
Train for more epochs with better hardware
Use transfer learning from pre-trained models


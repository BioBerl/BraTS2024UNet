# BraTS2024UNet

A complete 3D U-Net solution for the BraTS 2024 GLI (Glioma) challenge, implementing brain tumor segmentation on multi-modal MRI data.

##  Project Overview

This repository contains a comprehensive implementation for brain tumor segmentation using a 3D U-Net architecture. The solution processes multi-modal MRI images (T1CE, T2W, FLAIR) to perform 5-class semantic segmentation of brain tumor regions according to BraTS 2024 challenge specifications.

**Dataset Source**: [BraTS 2024 Challenge - Synapse](https://www.synapse.org/Synapse:syn59059776)

##  Repository Structure

This repository contains three essential Jupyter notebooks that implement the complete pipeline:

1. **`data_preprocessing.ipynb`** - Data preprocessing and formatting
2. **`model_training.ipynb`** - 3D U-Net training implementation
3. **`model_validation.ipynb`** - Model evaluation and visualization

##  Workflow Overview

### Stage 1: Data Preprocessing
- **Notebook**: `data_preprocessing.ipynb`
- Converts NIfTI files (.nii.gz) to compressed NumPy arrays (.npz)
- Combines 3 MRI modalities: T2F (FLAIR), T1CE, T2W
- Applies StandardScaler normalization
- Crops volumes to 142×178×142 voxels
- Filters volumes with >1% tumor content

### Stage 2: Model Training
- **Notebook**: `model_training.ipynb`
- Implements 3D U-Net with 5,648,789 parameters
- Uses combined Dice + Focal loss
- Trains for 60 epochs with early stopping
- Achieves 99.39% overall accuracy

### Stage 3: Model Validation
- **Notebook**: `model_validation.ipynb`
- Comprehensive performance evaluation
- Visualization tools and slice extraction
- Video generation for temporal analysis
- Per-class IoU metrics and confusion matrices

## 🛠 Installation

### Prerequisites
- Python 3.8+
- CUDA-compatible GPU (recommended)
- 16GB+ RAM
- 50GB+ free disk space

### Dependencies
```bash
pip install torch torchvision torchaudio
pip install nibabel numpy matplotlib scikit-learn
pip install tqdm split-folders
pip install torchmetrics torchinfo
pip install segmentation-models-pytorch-3d
pip install livelossplot
```

##  Dataset Setup

1. Download the BraTS 2024 training dataset from [Synapse](https://www.synapse.org/Synapse:syn59059776)
2. Extract the dataset to a folder named `training_data1_v2/`
3. Ensure the following structure:
   ```
   training_data1_v2/
   ├── BraTS-GLI-00001-100/
   │   ├── BraTS-GLI-00001-100-t1c.nii.gz
   │   ├── BraTS-GLI-00001-100-t1n.nii.gz
   │   ├── BraTS-GLI-00001-100-t2f.nii.gz
   │   ├── BraTS-GLI-00001-100-t2w.nii.gz
   │   └── BraTS-GLI-00001-100-seg.nii.gz
   └── ... (1324 total cases)
   ```

##  Usage

### Step 1: Data Preprocessing
```bash
jupyter notebook data_preprocessing.ipynb
```
- Run all cells to process the raw NIfTI data
- Creates `BraTS2024_Preprocessed/input_data_3channels_combined/` directory
- Outputs ~935 preprocessed .npz files

### Step 2: Model Training
```bash
jupyter notebook model_training.ipynb
```
- Splits data into train/test (85%/15%)
- Trains 3D U-Net for up to 100 epochs
- Saves checkpoints and best model
- Expected training time: 8-12 hours on modern GPU

### Step 3: Model Validation
```bash
jupyter notebook model_validation.ipynb
```
- Loads trained model checkpoint
- Evaluates on test set
- Generates visualizations and performance metrics

## Model Architecture

### 3D U-Net Specifications
- **Input**: 3-channel MRI volumes (142×178×142×3)
- **Output**: 5-class probability maps (142×178×142×5)
- **Parameters**: 5,648,789 trainable parameters
- **Architecture**: Encoder-decoder with skip connections
- **Loss Function**: Combined Dice Loss + Focal Loss (50%/50%)

### Class Definitions (BraTS 2024)
| Class | Label | Description |
|-------|--------|-------------|
| 0 | Background | Normal brain tissue |
| 1 | NETC | Non-Enhancing Tumor Core |
| 2 | SNFH | Surrounding Non-Enhancing FLAIR Hyperintensity |
| 3 | ET | Enhancing Tissue |
| 4 | RC | Resection Cavity |

## Performance Results

### Overall Metrics (Test Set, n=110)
- **Overall Accuracy**: 99.39% ± 0.41%
- **Training Epochs**: 60 (with early stopping)

### Per-Class Performance (IoU Scores)
| Class | IoU Score | F1-Score | Description |
|-------|-----------|----------|-------------|
| 0 (Background) | 99.55% | 99.77% | Normal tissue |
| 1 (NETC) | 34.97% | 44.91% | Non-enhancing core |
| 2 (SNFH) | 74.26% | 84.11% | FLAIR hyperintensity |
| 3 (ET) | 70.76% | 81.17% | Enhancing tissue |
| 4 (RC) | 29.59% | 38.22% | Resection cavity |

##  Visualization Features

The validation notebook includes advanced visualization tools:

- **Slice-by-slice Analysis**: Compare ground truth vs predictions
- **4-pane Visualizations**: Original, true mask, prediction, errors
- **Video Generation**: Create MP4 videos showing all slices
- **Error Mapping**: Highlight prediction differences
- **Statistical Plots**: IoU distributions and performance summaries

## Hardware Requirements

### Minimum Requirements
- **GPU**: 8GB VRAM (e.g., RTX 3070, GTX 1080Ti)
- **RAM**: 16GB system memory
- **Storage**: 50GB free space

### Recommended Setup
- **GPU**: 12GB+ VRAM (e.g., RTX 3080, A100)
- **RAM**: 32GB system memory
- **Storage**: 100GB+ SSD storage

## Technical Notes

- **Batch Size**: Optimized for 8GB GPU memory (batch_size=8)
- **Mixed Precision**: Automatic mixed precision training enabled
- **Gradient Accumulation**: 2-step accumulation for stability
- **Data Augmentation**: Standard scaling and cropping only
- **Optimization**: AdamW optimizer with cosine annealing

## Key Files Description

### `data_preprocessing.ipynb`
- **Purpose**: Complete data preprocessing pipeline
- **Key Functions**:
  - `extract_simple_name()`: Standardizes file naming
  - Multi-modal image stacking and normalization
  - Quality filtering (>1% tumor content)
- **Output**: Preprocessed .npz files ready for training

### `model_training.ipynb`
- **Purpose**: Full 3D U-Net training implementation
- **Key Components**:
  - Custom `BraTSDataset` class
  - 3D U-Net architecture definition
  - Combined loss function (Dice + Focal)
  - Training loop with validation
  - Checkpoint management and early stopping
- **Output**: Trained model checkpoint (`ckpt_epoch_60.tar`)

### `model_validation.ipynb`
- **Purpose**: Comprehensive model evaluation and visualization
- **Key Features**:
  - Model loading and inference pipeline
  - Performance metric calculation
  - Advanced visualization tools
  - Slice extraction and video generation
  - Statistical analysis and plotting
- **Output**: Validation results, visualizations, and performance reports

## Reproducibility

To reproduce the results:
1. Use the exact dataset from the provided Synapse link
2. Follow the notebook execution order (preprocessing → training → validation)
3. Use the same random seed (42) specified in the notebooks
4. Ensure consistent hardware setup (CUDA-enabled GPU recommended)

## Citation

If you use this implementation, please cite:

```bibtex
@misc{brats2024unet,
  title={BraTS2024UNet: 3D U-Net Implementation for Brain Tumor Segmentation},
  author={[Your Name]},
  year={2024},
  howpublished={\url{https://github.com/BioBerl/BraTS2024UNet}}
}
```

## Acknowledgments

- **BraTS 2024 Challenge** organizers for providing the dataset
- **Medical Image Computing and Computer Assisted Intervention (MICCAI)** community
- **Synapse** platform for data hosting
- Original U-Net paper: Ronneberger, O., Fischer, P., & Brox, T. (2015)

## License

This project is released under the MIT License. See LICENSE file for details.

---

**Note**: This implementation is for research and educational purposes. Clinical applications require additional validation and regulatory approval.
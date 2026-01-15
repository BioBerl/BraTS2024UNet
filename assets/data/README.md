# BraTS 2024 Segmentation Viewer - Data Directory

This directory contains brain tumor segmentation data for visualization in the viewer.

## Directory Structure

Each case should be placed in a separate folder following this structure:

```
assets/data/
├── cases.json                    # List of available cases
├── case001/
│   ├── groundtruth.nii.gz       # Ground truth segmentation mask
│   ├── prediction.nii.gz        # Model prediction mask
│   ├── error.nii.gz             # Error map (FP/FN analysis)
│   └── metadata.json            # Case metadata and metrics
├── case002/
│   ├── groundtruth.nii.gz
│   ├── prediction.nii.gz
│   ├── error.nii.gz
│   └── metadata.json
└── case003/
    ├── groundtruth.nii.gz
    ├── prediction.nii.gz
    ├── error.nii.gz
    └── metadata.json
```

## File Descriptions

### NIFTI Files (.nii.gz)

1. **groundtruth.nii.gz**: The ground truth segmentation mask
   - Binary or multi-class segmentation
   - Standard NIFTI format, gzip compressed

2. **prediction.nii.gz**: The model's prediction mask
   - Same dimensions and format as ground truth
   - Binary or multi-class segmentation

3. **error.nii.gz**: Error map showing false positives and false negatives
   - Values can represent:
     - 0: True Negative (correct background)
     - 1: True Positive (correct tumor)
     - 2: False Positive (predicted tumor, actually background)
     - 3: False Negative (predicted background, actually tumor)

### metadata.json

Contains case information and computed metrics:

```json
{
  "caseId": "case001",
  "patientId": "BraTS-GLI-00001",
  "description": "Case description",
  "metrics": {
    "dice": 0.8534,
    "iou": 0.7445,
    "sensitivity": 0.8912,
    "specificity": 0.9987,
    "precision": 0.8201,
    "hausdorff": 12.45
  },
  "errorPoints": [
    { "x": -10, "y": 15, "z": 20, "type": "FP" },
    { "x": 5, "y": -8, "z": 12, "type": "FN" }
  ]
}
```

**Metrics Explanation:**
- **dice**: Dice Similarity Coefficient (0-1, higher is better)
- **iou**: Intersection over Union / Jaccard Index (0-1, higher is better)
- **sensitivity**: True Positive Rate / Recall (0-1, higher is better)
- **specificity**: True Negative Rate (0-1, higher is better)
- **precision**: Positive Predictive Value (0-1, higher is better)
- **hausdorff**: Hausdorff Distance in mm (lower is better)

**Error Points:**
- List of 3D coordinates where errors occur
- `type`: "FP" for False Positive, "FN" for False Negative
- Used for 3D point cloud visualization

### cases.json

Simple list of available case folders:

```json
{
  "cases": [
    "case001",
    "case002",
    "case003"
  ]
}
```

## Adding New Cases

To add a new case:

1. Create a new folder: `assets/data/caseXXX/`
2. Add the three NIFTI files (groundtruth.nii.gz, prediction.nii.gz, error.nii.gz)
3. Create metadata.json with metrics
4. Update cases.json to include the new case ID

## Generating Error Maps

If you need to generate error maps from ground truth and predictions:

```python
import nibabel as nib
import numpy as np

# Load ground truth and prediction
gt = nib.load('groundtruth.nii.gz')
pred = nib.load('prediction.nii.gz')

gt_data = gt.get_fdata()
pred_data = pred.get_fdata()

# Create error map
# 0 = TN, 1 = TP, 2 = FP, 3 = FN
error_map = np.zeros_like(gt_data)
error_map[(gt_data == 0) & (pred_data == 0)] = 0  # TN
error_map[(gt_data == 1) & (pred_data == 1)] = 1  # TP
error_map[(gt_data == 0) & (pred_data == 1)] = 2  # FP
error_map[(gt_data == 1) & (pred_data == 0)] = 3  # FN

# Save error map
error_nifti = nib.Nifti1Image(error_map, gt.affine, gt.header)
nib.save(error_nifti, 'error.nii.gz')
```

## Computing Metrics

```python
from sklearn.metrics import jaccard_score
import numpy as np

def compute_dice(gt, pred):
    intersection = np.sum(gt * pred)
    return (2. * intersection) / (np.sum(gt) + np.sum(pred))

def compute_metrics(gt_data, pred_data):
    gt_flat = gt_data.flatten().astype(bool)
    pred_flat = pred_data.flatten().astype(bool)
    
    tp = np.sum(gt_flat & pred_flat)
    tn = np.sum(~gt_flat & ~pred_flat)
    fp = np.sum(~gt_flat & pred_flat)
    fn = np.sum(gt_flat & ~pred_flat)
    
    metrics = {
        'dice': compute_dice(gt_flat, pred_flat),
        'iou': jaccard_score(gt_flat, pred_flat),
        'sensitivity': tp / (tp + fn) if (tp + fn) > 0 else 0,
        'specificity': tn / (tn + fp) if (tn + fp) > 0 else 0,
        'precision': tp / (tp + fp) if (tp + fp) > 0 else 0
    }
    
    return metrics
```

## Notes

- The placeholder NIFTI files in the repository are empty and should be replaced with real data
- The viewer supports standard NIFTI format (.nii.gz)
- Ensure all three NIFTI files for a case have the same dimensions
- The viewer will automatically detect and load cases listed in cases.json

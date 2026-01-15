# BraTS2024UNet
A solution for the BraTS 2024 GLI challenge

## Brain Tumor Segmentation Viewer

An interactive 3-panel NIFTI visualization tool for brain tumor segmentation analysis.

### Features

- **3-Panel Synchronized View**: 
  - Left: Ground truth mask
  - Middle: Prediction mask
  - Right: Error map (False Positives/False Negatives)

- **Interactive Navigation**:
  - Synchronized slice navigation across all panels
  - Axial, Sagittal, and Coronal view modes
  - Real-time crosshair synchronization
  - Slider controls for each anatomical plane

- **Metrics Display**:
  - Dice Similarity Coefficient
  - IoU (Jaccard Index)
  - Sensitivity (Recall)
  - Specificity
  - Precision
  - Hausdorff Distance

- **3D Visualization** (Optional):
  - Three.js-powered 3D error point cloud
  - Color-coded false positives (red) and false negatives (blue)
  - Interactive rotation

- **Case Management**:
  - Dropdown selector for multiple cases
  - Dynamic case loading
  - Metadata display

### Technology Stack

- **Pure HTML/CSS/JavaScript** - No build step required
- **NiiVue.js** - High-performance NIFTI viewer library
- **Three.js** - 3D visualization (optional)
- **Modern CSS Grid** - Responsive layout

### Getting Started

#### 1. Open the Viewer

Simply open `index.html` in a modern web browser (Chrome, Firefox, Edge, Safari).

For local file access, you may need to run a local web server:

```bash
# Python 3
python -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000

# Node.js (if you have npx)
npx http-server

# PHP
php -S localhost:8000
```

Then navigate to `http://localhost:8000`

#### 2. Add Your Data

Place your NIFTI files in the `assets/data/` directory:

```
assets/data/
├── cases.json
├── case001/
│   ├── groundtruth.nii.gz
│   ├── prediction.nii.gz
│   ├── error.nii.gz
│   └── metadata.json
└── case002/
    └── ...
```

See `assets/data/README.md` for detailed instructions on data format and structure.

#### 3. Select a Case

Use the dropdown menu to select a case and view the segmentation results.

### Data Format

Each case folder should contain:

1. **groundtruth.nii.gz** - Ground truth segmentation mask
2. **prediction.nii.gz** - Model prediction mask
3. **error.nii.gz** - Error map (FP/FN analysis)
4. **metadata.json** - Case metadata and computed metrics

Example metadata.json:

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

### Usage

1. **Select a Case**: Choose a case from the dropdown menu
2. **Navigate Slices**: Use the sliders or mouse wheel to navigate through slices
3. **Change View**: Click the view buttons to switch between Axial, Sagittal, and Coronal views
4. **View Metrics**: Scroll down to see computed segmentation metrics
5. **3D Visualization**: If error points are available, view the 3D error point cloud at the bottom

### Controls

- **Mouse Wheel**: Navigate through slices
- **Click + Drag**: Pan the view
- **Sliders**: Manual slice selection for each anatomical plane
- **View Buttons**: Switch between anatomical views

### Browser Compatibility

- Chrome/Edge (recommended)
- Firefox
- Safari
- Modern mobile browsers

### No Build Required

This is a standalone HTML application with no build step or dependencies to install. All libraries are loaded via CDN.

### Credits

- Built for BraTS 2024 GLI Challenge
- Uses [NiiVue.js](https://github.com/niivue/niivue) for NIFTI visualization
- Uses [Three.js](https://threejs.org/) for 3D rendering

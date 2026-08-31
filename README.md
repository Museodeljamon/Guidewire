Author: Tim Chen

This project was developed by Tim Chen as proof of concept for AI-based 
guidewire segmentation and temporal tracking in fluoroscopy video.

# FluoroSAM + ConvLSTM Guidewire Tracking

A proof-of-concept deep learning pipeline for automatic guidewire segmentation and temporal tracking in fluoroscopy video.

The project combines a pretrained **FluoroSAM** image encoder with a **ConvLSTM** temporal network and a lightweight segmentation decoder. Instead of processing fluoroscopy frames independently, the model processes consecutive frames as a short temporal sequence to improve consistency when tracking thin guidewires.

## Pipeline

```text
Fluoroscopy frames
        ↓
Preprocessing
        ↓
FluoroSAM image encoder
        ↓
ConvLSTM temporal fusion
        ↓
Segmentation decoder
        ↓
Guidewire probability mask
        ↓
Thresholding + connected-component filtering
        ↓
Skeletonization
        ↓
Guidewire endpoint / tip estimation
        ↓
Temporal tip smoothing
        ↓
Tracking overlay and video export
```

## Features

* Official Guide3D dataset support
* FluoroSAM pretrained image encoder
* ConvLSTM temporal feature fusion
* Guidewire segmentation
* Guidewire tip estimation
* Temporal trajectory visualization
* Sequence-level train / validation / test splitting
* Checkpoint saving and resume support
* Inference-only demo mode
* Automatic export of:

  * original fluoroscopy video
  * AI segmentation / tracking overlay
  * side-by-side comparison video
  * combined comparison montage
  * JSON output manifest

## Visualization

The demo overlay uses:

* **Green** — predicted guidewire segmentation
* **Red** — estimated guidewire tip
* **Blue** — recent guidewire-tip trajectory

## Dataset

This project uses the official **Guide3D** dataset.

Guide3D contains fluoroscopy recordings of guidewires manipulated inside a vascular phantom. The images are acquired using fluoroscopic imaging equipment, but the dataset does **not** contain real-patient procedures.

For this reason, results from this repository should be interpreted as phantom-data proof-of-concept results rather than evidence of clinical performance.

## Model Architecture

The model contains three main components:

### 1. FluoroSAM Encoder

A pretrained FluoroSAM Swin-L image encoder extracts spatial features from each fluoroscopy frame.

### 2. ConvLSTM

Features from multiple consecutive frames are passed through a ConvLSTM network to incorporate temporal information and guidewire movement.

### 3. Lightweight Decoder

The fused temporal representation is decoded into a binary guidewire segmentation mask.

Post-processing is then applied to identify the largest connected component, skeletonize the guidewire prediction, estimate endpoints, and track the most temporally consistent guidewire tip.

## Demo Mode

If the model has already been trained, the demo-only script loads:

```text
best_fluorosam_convlstm.pt
```

and skips training, optimizer restoration, and full test-set evaluation.

Demo outputs are written to:

```text
Guidewire_POC/output/demo_videos/
```

Each selected test sequence can generate:

```text
<sequence>_original.mp4
<sequence>_ai_overlay.mp4
<sequence>_comparison.mp4
```

A combined comparison montage is also generated.

## Environment

The pipeline is designed primarily for Google Colab with an NVIDIA GPU.

The current implementation has been tested with:

```text
Python 3.12
PyTorch
CUDA
MMCV Lite
MMEngine
MMDetection
OpenCV
scikit-image
```

The script includes compatibility handling for FluoroSAM and MMDetection in current Colab environments.

## Limitations

This repository is currently a research proof of concept.

Important limitations include:

* Training data is based on vascular phantoms rather than real patients.
* The number of independent Guide3D sequences is limited.
* Clinical robustness has not been established.
* Guidewire appearance in real procedures may differ substantially because of anatomy, other devices, image acquisition settings, noise, motion, and contrast agents.
* Tip localization currently relies partly on geometric and temporal heuristics.
* Performance must be independently evaluated on real clinical fluoroscopy before any clinical use.

## Future Work

Potential next steps include:

* evaluation on real clinical fluoroscopy sequences
* domain adaptation from phantom to clinical fluoroscopy
* training with larger and more diverse datasets
* improved guidewire-tip localization
* uncertainty / confidence estimation
* real-time inference optimization
* integration with fluoroscopy workstation software

## Disclaimer

This project is intended for research and demonstration purposes only.
It is not a medical device and has not been validated for diagnosis, treatment, surgical navigation, or clinical decision-making.

# 🎯 Counter Object Detection & Recognition

> **EECE 490 / 690 — Final Project**  
> American University of Beirut

**Team:** Ola Sadek · Zora Banbouk · Mohamad Taha · Mohamad Dayeh

---

## 📌 Overview

This project investigates **adversarial attacks against three computer vision pipelines** commonly used in physical security and surveillance:

| Pipeline | Model | Attack |
|---|---|---|
| 👤 Face Detection | YOLOv8n (fine-tuned) | QR-coded hat occlusion |
| 🧠 Face Recognition | InsightFace (`buffalo_l`) | Landmark-guided hat overlay |
| 🚗 License Plate Detection | YOLOv8n (fine-tuned) | Color-matched 4-patch surround |
| 🚙 Car Detection | YOLOv8n (fine-tuned) | Dense checkerboard tattoo patch |

The goal is to assess the robustness of modern detectors under realistic adversarial conditions and highlight vulnerabilities in safety-critical systems.

---

## 📁 Repository Structure

```
.
├── facedetection.ipynb        # Face detection + hat occlusion attack
├── faceRecognition.ipynb      # InsightFace embeddings + LFW evaluation + hat adversarial
├── plates_detection.ipynb     # License plate detection + color-patch attack
├── cars_detection.ipynb       # Car detection + checkerboard tattoo attack
├── hat.png                    # Adversarial hat image (contains 2 QR codes)
└── README.md
```

---

## 🧪 Notebooks

### 1. `facedetection.ipynb` — Face Detection + Hat Occlusion
Trains a **YOLOv8n** face detector on the [Face Detection Dataset](https://www.kaggle.com/datasets/fareselmenshawii/face-detection-dataset), then evaluates a strategic occlusion attack using `hat.png`.

**Attack:** The hat (which contains **two embedded QR codes**) is scaled, alpha-composited, and placed over each ground-truth face region. The QR patterns introduce high-frequency texture that disrupts convolutional feature extraction in addition to the geometric occlusion.

**Metric:** Recall before vs. after occlusion on 100 validation images.

---

### 2. `faceRecognition.ipynb` — Face Recognition + Adversarial Hat
Uses **InsightFace** (`buffalo_l`) to extract 512-d normalized embeddings from the [LFW dataset](https://www.kaggle.com/datasets/atulanandjha/lfwpeople). Identity verification is done via cosine similarity with an optimized threshold.

**Attack:** In a real-time setting, the hat is placed using facial landmark guidance — positioned above the brow line, rotated to match face tilt, and scaled by inter-eye distance.

**Metrics:** Accuracy, Precision, Recall on the standard LFW pairs protocol.

---

### 3. `plates_detection.ipynb` — License Plate Detection + Color Patch Attack
Fine-tunes **YOLOv8n** on the [Car Plate Detection dataset](https://www.kaggle.com/datasets/andrewmvd/car-plate-detection) (Pascal VOC → YOLO format conversion included).

**Attack:** A second YOLOv8n (COCO-pretrained) detects the car and samples its dominant paint color. Four **color-matched noisy patches** are placed around the license plate to blur its spatial context without standing out visually.

**Metric:** Detection count and confidence drop before/after patching.

---

### 4. `cars_detection.ipynb` — Car Detection + Tattoo Patch Attack
Fine-tunes **YOLOv8n** on the [Cars Detection dataset](https://www.kaggle.com/datasets/abdallahwagih/cars-detection) for 50 epochs, then evaluates a center-placed visual patch on 126 test images.

**Attack:** A **dense checkerboard tattoo** (black/white grid + green/magenta circles) is resized to 25% of the car bounding box height and placed at the box center.

**Metric:** Recall rate and evasion rate (% of cars that evade detection post-attack).

---

## ⚙️ Setup

All notebooks are designed to run on **Google Colab** with GPU.

### Dependencies

```bash
pip install ultralytics insightface onnxruntime-gpu kagglehub \
            opencv-python numpy matplotlib scikit-learn tensorflow
```

### Kaggle API

Datasets are downloaded via `kagglehub`. Make sure your Kaggle credentials are configured:

```bash
pip install kaggle
# Place kaggle.json in ~/.kaggle/
```

### Adversarial Hat

Place your `hat.png` file (a hat image with **2 QR codes** on it) at `/content/hat.png` before running the face detection and recognition attack cells.

---

## 📊 Results Summary

| Pipeline | Metric | Clean | After Attack |
|---|---|---|---|
| Face Detection | Recall | — | Drops after hat occlusion |
| Face Recognition | Accuracy | Optimized on LFW pairs | Similarity degraded |
| License Plate | Detection Count | Baseline | Reduced post-patch |
| Car Detection | Recall | Baseline | Drops by evasion rate |

*Exact numbers are printed at the end of each notebook's evaluation cell.*

---

## 💡 Key Insights

- **Physical plausibility matters** — the color-matched patches and QR-coded hat are designed to evade human suspicion while fooling the model.
- **Context attacks work** — the license plate attack targets the *surrounding region*, not the plate itself, exploiting the model's reliance on spatial context.
- **Structured high-frequency patterns** (QR codes, checkerboards) are disproportionately effective against CNN-based detectors.

---

## 🛡️ Potential Defenses

- **Adversarial training** — augment training data with patched/occluded examples
- **Input smoothing / JPEG compression** — destroys high-frequency perturbations
- **Ensemble detection** — require agreement across multiple scales or models
- **Anomaly detection** — flag unusual textures in detected regions before classification

---

## 👥 Team
**Mohamad Dayeh** **Ola Sadek** **Zora Banbouk** **Mohamad Taha** 
---

## 📄 License

This project was developed for academic purposes as part of EECE 490/690 at the American University of Beirut.

# 🩺 Surgical Visual Co-Pilot: Real-Time 3D Tool Tracking & Geofencing

[![macOS Supported](https://img.shields.io/badge/macOS-Apple_Silicon-brightgreen)](https://developer.apple.com/)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-EE4C2C.svg)](https://pytorch.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.x-5C3EE8.svg)](https://opencv.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

An end-to-end, sub-15ms real-time computer vision and 3D spatial safety pipeline designed for laparoscopic surgical assistance. This system provides sub-millisecond tool trajectory estimation, dynamic 3D geofencing around vulnerable anatomical boundaries, and GPU-accelerated on-screen safety alerts.

---

## 📸 Demo & Visualization (TBD)

> *Insert a GIF or side-by-side rendering here showing real-time tool tracking, distance heatmaps, and OSD visual boundary alerts.*


---

## ⚡ System Architecture & Latency Budget

To achieve real-time clinical viability without dropping frames, the system implements a zero-copy memory architecture and asynchronous multi-stream execution:


.
├── .github/
│   └── workflows/          # CI/CD automated testing and linting pipelines
├── data/                   # Sample videos and test media (git-ignored)
├── docs/                   # System design diagrams and performance benchmarks
├── src/                    # Primary source code
│   ├── __init__.py
│   ├── capture/            # Video stream ingestion and zero-copy frame buffers[cite: 1]
│   ├── detection/          # Model inference and keypoint localization[cite: 1]
│   ├── tracking/           # 3D Extended Kalman Filter (EKF) and state estimation[cite: 1]
│   ├── geofencing/         # Point-to-mesh Euclidean spatial distance kernels[cite: 1]
│   └── rendering/          # Vulkan / OpenGL On-Screen Display (OSD) overlays[cite: 1]
├── tests/                  # Automated unit and integration test suites
├── .gitignore              # Excludes virtual environments, builds, and raw data
├── LICENSE                 # Project open-source license (MIT)
├── README.md               # Primary project documentation and architecture overview
├── main.py                 # Application entry point and pipeline orchestrator
└── requirements.txt        # Python dependency specifications



[Stereo Video Input] ──► [Zero-Copy Frame Buffer]
│
┌─────────────────────────┴────────────────────────┐
▼                                                  ▼
[YOLO Tool Tip Detection]                     [3D Stereo Triangulation]
(~3.0 ms latency)                      (~1.0 ms latency)
│                                                  │
└─────────────────────────┬────────────────────────┘
│
▼
[3D Extended Kalman Filter (EKF)]
(~0.5 ms latency)
│
▼
[Spatial Geofencing Engine]
Euclidean Point-to-Mesh Math
│
▼
[Vulkan/OpenGL On-Screen Display (OSD)]
(~1.5 ms latency)




### End-to-End Target Pipeline Latency: **~12.5 ms (60–90 FPS)**[cite: 1]

| Pipeline Stage | Technology Stack | Target Latency |
| :--- | :--- | :--- |
| **Frame Capture & Pre-Processing** | Hardware Resizing & NV12 to RGB | ~1.0 ms[cite: 1] |
| **Tool Tip Detection / Inference** | TensorRT-Optimized YOLO | ~3.0 ms[cite: 1] |
| **Tracking & State Estimation** | CUDA-Accelerated 3D EKF Kernel | ~0.5 ms[cite: 1] |
| **Safety Geofencing Logic** | GPU Spatial Point-to-Mesh Math Kernel | ~0.5 ms[cite: 1] |
| **Overlay Rendering & OSD Output** | Vulkan / OpenGL OSD Buffer | ~1.5 ms[cite: 1] |

---

## ✨ Key Technical Features

* **3D State Filtering & Occlusion Handling:** Pairs tracking algorithms (DeepSORT/ByteTrack) with a 3D Extended Kalman Filter (EKF) to estimate tool trajectories during temporal visual occlusions (e.g., tissue or blood obstruction)[cite: 1].
* **Real-Time Spatial Geofencing:** Computes frame-by-frame Euclidean point-to-mesh distances between 3D tool tip coordinates ($T_{xyz}$) and defined anatomical safety envelopes ($A_{xyz}$)[cite: 1].
* **Active Visual Safety Alerts:** Generates on-screen overlay warnings and alerts when surgical tool boundaries breach defined safety margins[cite: 1].
* **Hybrid Execution Architecture:** Uses Python for workflow orchestration, model training, and experimentation, combined with C++/CUDA execution engines for low-latency frame pipelines[cite: 1].

---

## 🛠️ Tech Stack

* **Languages:** Python 3.10+, C++17[cite: 1]
* **Computer Vision & Geometry:** OpenCV, Open3D, FilterPy[cite: 1, 2]
* **Deep Learning Frameworks:** PyTorch, TorchVision, Ultralytics (YOLO)[cite: 1, 2]
* **Inference Engine & Acceleration:** TensorRT / ONNX Runtime[cite: 1]

---

## 🚀 Getting Started

### Prerequisites

Ensure Python 3.10+ and Homebrew are installed on your Mac:

```bash
brew install cmake opencv
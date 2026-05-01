# Trajectory Simulator

A neural-network-based mouse trajectory simulator for generating human-like mouse movement paths from a target displacement.

This project collects real mouse movement trajectories, trains a lightweight PyTorch MLP model, and exports the trained model to ONNX for fast inference. Given a relative movement target `(dx, dy)`, the model predicts a sequence of trajectory points that approximate a natural mouse movement path.

> 使用神經網路預測擬人滑鼠軌跡。  
> 根據目標位移 `(dx, dy)`，模型會輸出一組接近人類滑鼠移動習慣的軌跡點。

---

## Overview

Trajectory Simulator is designed for experimenting with mouse movement modeling, trajectory prediction, and human-computer interaction control research.

Instead of generating a straight-line movement from point A to point B, the project learns trajectory patterns from collected mouse movement data. The trained model can then generate a short sequence of intermediate points for a given displacement.

Core idea:

```text
Input:  dx, dy
Output: 10 trajectory points
```

Example:

```json
{
  "relative_move": {
    "dx": 120,
    "dy": 80
  },
  "trajectory": [
    [0, 0],
    [8, 4],
    [19, 11],
    [34, 22],
    [52, 35],
    [71, 48],
    [89, 61],
    [103, 70],
    [114, 77],
    [120, 80]
  ]
}
```

---

## Features

- Interactive Pygame GUI for data collection, training, testing, and log viewing
- Mouse trajectory dataset stored in JSONL format
- PyTorch MLP model for trajectory prediction
- ONNX export for lightweight and fast inference
- ONNX Runtime inference support
- Bilingual interface support: English / Chinese

---

## Demo

The GUI provides a simple control panel for:

- Collecting mouse movement data
- Training the trajectory model
- Testing generated trajectories
- Viewing runtime logs
- Switching interface language

![Trajectory Simulator Demo](figure.png)

---

## How It Works

The project follows this pipeline:

```text
1. Collect real mouse movement data
2. Save each movement as a JSONL record
3. Train a neural network using PyTorch
4. Export the trained model to ONNX
5. Run fast inference with ONNX Runtime
6. Generate predicted trajectory points from dx/dy
```

The model does not predict movement based on time slices.

Instead, each trajectory is normalized into a fixed number of points according to the full path length. This makes the generated path easier to combine with external control algorithms such as PID controllers or custom movement systems.

---

## Model

The default model is a simple Multi-Layer Perceptron.

```text
Input:
  dx, dy

Output:
  10 trajectory points
  20 values total: x1, y1, x2, y2, ..., x10, y10

Architecture:
  Linear(2 -> 64)
  ReLU
  Linear(64 -> 128)
  ReLU
  Linear(128 -> 64)
  ReLU
  Linear(64 -> 20)
```

The trained model is exported as:

```text
mouse_traj.onnx
```

---

## Dataset Format

Training data is stored in JSONL format.

Each line represents one mouse movement sample:

```json
{"relative_move":{"dx":120,"dy":80},"trajectory":[[0,0],[8,4],[19,11],[34,22],[52,35],[71,48],[89,61],[103,70],[114,77],[120,80]]}
```

Field description:

| Field | Description |
| --- | --- |
| `relative_move.dx` | Target horizontal movement |
| `relative_move.dy` | Target vertical movement |
| `trajectory` | A list of trajectory points from start to target |

---

## Requirements

Install dependencies with:

```bash
pip install -r requirements.txt
```

Main dependencies:

```text
torch
onnxruntime
pygame
numpy
```

Recommended environment:

```text
Python 3.9+
Windows 10 / Windows 11
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/hoho087/trajectory-simulator.git
cd trajectory-simulator
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Usage

Start the GUI:

```bash
python main.py
```

The GUI includes:

```text
Collect Data
Train Model
Test Model
View Logs
Language Switch
Quit
```

---

## Collect Data

Use the GUI to collect mouse movement trajectories.

The collected data will be saved as:

```text
mouse_dataset.jsonl
```

Each sample records:

```text
1. The target relative movement: dx, dy
2. The actual mouse movement trajectory
```

---

## Train Model

After collecting enough samples, train the model from the GUI or run:

```bash
python train_model.py
```

By default, the training script reads:

```text
mouse_dataset.jsonl
```

and exports the trained model to:

```text
mouse_traj.onnx
```

---

## Test Model

You can test the exported ONNX model from the GUI, or run:

```bash
python test_model.py
```

The inference function accepts a target displacement:

```python
run_inference("mouse_traj.onnx", dx=120, dy=80)
```

and returns 10 predicted trajectory points:

```python
[
    [x1, y1],
    [x2, y2],
    ...
    [x10, y10]
]
```

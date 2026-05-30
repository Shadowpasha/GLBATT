# GLBATT: Global-Local Belief Attention

Welcome to the official repository for **GLBATT (Global-Local Belief Attention)**, a memory-efficient and highly stable architecture designed for solving long-horizon Partially Observable Markov Decision Processes (POMDPs) in Deep Reinforcement Learning.

This clean code directory has been prepared specifically for **publishing and reproducibility**. It contains only the core GLBATT library components and the critical training scripts to recreate all benchmarks.

---

## 📂 Repository Structure

The repository is structured as a standard modular package:

```
GLBATT_publishing/
├── glbatt/                      # Core neural network & utility library
│   ├── __init__.py
│   ├── utils.py                 # Flat replay buffer
│   ├── utils_transformer.py     # High-efficiency sequence replay buffer
│   ├── utils_T_TD3.py           # Baseline Sequence replay buffer for comparisons
│   ├── architectures/           # Model neural network definitions
│   │   ├── __init__.py
│   │   ├── CrossQ_GLBATT.py            # Main stabilized CrossQ + GLBATT model
│   │   ├── CrossQ_GLBATT_NoFusion.py   # Ablation: Without decision fusion
│   │   ├── CrossQ_GLBATT_NoStriding.py # Ablation: Contiguous sequence processing
│   │   ├── GLBATT_NoGlobal.py          # Ablation: Local-only attention window
│   │   └── GLBATT_Summary.py           # Standard TD3 + GLBATT model
│   └── gtrxl_torch/             # GTrXL Transformer backbone implementation
│       ├── __init__.py
│       └── gtrxl_torch.py
├── environments/                # Custom benchmarking environments
│   ├── __init__.py
│   ├── agnostic_pid_adapter.py  # PID controller wrapper for robotics tasks
│   ├── complex_envs.py          # Navigational blind corridors and trap envs
│   ├── differential_lidar_env.py # Differential drive robot simulator
│   ├── holonomic_lidar_env.py    # Standard holonomic navigation env
│   ├── holonomic_lidar_env_moving.py # Navigation with dynamic/moving obstacles
│   ├── holonomic_lidar_env_pid.py   # Navigation with PID smoothed controls
│   └── mujoco_pomdp_wrapper.py  # POMDP joint-masking wrapper for MuJoCo Locomotion
├── scripts/                     # Reproducibility training scripts
│   ├── train_gym_GLBATT.py
│   ├── train_gym_CrossQ_GLBATT.py
│   ├── train_mujoco_pomdp_GLBATT.py
│   ├── train_mujoco_pomdp_CrossQ_GLBATT.py
│   ├── train_mujoco_pomdp_CrossQ_GLBATT_NoFusion.py
│   ├── train_mujoco_pomdp_CrossQ_GLBATT_NoStriding.py
│   ├── train_GLBATT_pygame.py
│   └── train_CrossQ_GLBATT_pygame.py
└── README.md                    # Detailed documentation and guidelines
```

---

## ⚡ Quick Start & Installation

To install all dependencies required to train GLBATT, we recommend setting up a virtual environment running Python 3.10+:

```bash
# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate

# Install core deep learning packages
pip install torch numpy gymnasium tensorboard

# Install benchmark specific dependencies (Pygame & MuJoCo)
pip install pygame mujoco
```

---

## 🚀 Running Training Benchmarks

To ensure reproducibility, all scripts run with default configurations matched to the parameters reported in the paper. 

### 1. Classical Gymnasium Benchmarks (Continuous Control)
To train **CrossQ-GLBATT** or vanilla **GLBATT** on the classic `BipedalWalker-v3` environment:
```bash
# Train with CrossQ + GLBATT (highly stabilized off-policy learning)
python3 scripts/train_gym_CrossQ_GLBATT.py --env BipedalWalker-v3 --seed 1236

# Train with standard TD3 + GLBATT
python3 scripts/train_gym_GLBATT.py --env BipedalWalker-v3 --seed 1236
```

### 2. MuJoCo POMDP Benchmarks (Locomotion with Occluded Joint States)
To train on partially observable MuJoCo environments (e.g. `Hopper-v4` with distal foot joints masked out):
```bash
# Train standard CrossQ-GLBATT POMDP
python3 scripts/train_mujoco_pomdp_CrossQ_GLBATT.py --env Hopper-v4 --seed 1236

# Run Ablation: No-Fusion
python3 scripts/train_mujoco_pomdp_CrossQ_GLBATT_NoFusion.py --env Hopper-v4 --seed 1236

# Run Ablation: No-Striding
python3 scripts/train_mujoco_pomdp_CrossQ_GLBATT_NoStriding.py --env Hopper-v4 --seed 1236
```

### 3. PyGame 2D Navigation Benchmarks (LIDAR-based Occlusion & Planning)
To train in highly challenging custom 2D navigation environments with moving obstacles or blind corridors:
```bash
# Train on environment with dynamic moving obstacles
python3 scripts/train_CrossQ_GLBATT_pygame.py --env moving --seed 1236

# Train on blind corridor task
python3 scripts/train_CrossQ_GLBATT_pygame.py --env corridor --seed 1236
```

---

## 📊 Monitoring Progress

All scripts utilize PyTorch TensorBoard for high-frequency logging of training metrics, safety/auxiliary loss accuracy, Q-value estimations, and reward trends.

To visualize logs in real-time, run:
```bash
tensorboard --logdir=runs/
```
Then navigate to `http://localhost:6006` in your web browser.

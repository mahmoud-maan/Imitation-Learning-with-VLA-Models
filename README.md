# The SO-101 Robotic System — Imitation Learning with VLA Models

To further explore imitation learning, I've been working with the **SO-101 Kit**, an open-source project from HuggingFace. This setup is designed to test the limits of **Vision-Language-Action (VLA)** models in a controlled environment. In this project I fine-tuned two models to conduct a **pick-and-place task**: SmolVLA and ACT.

**My Collected Human Demonstritation Datasets:**
- 🗂️ Old setup data: [maanm/pick-place-00](https://huggingface.co/datasets/maanm/pick-place-00)
- 🗂️ New setup data: [maanm/pick-place-01](https://huggingface.co/datasets/maanm/pick-place-01)
- 📊 Visualize the dataset: [LeRobot Dataset Visualizer](https://huggingface.co/spaces/lerobot/visualize_dataset)
- 🤗 Models: [huggingface.co/maanm](https://huggingface.co/maanm)

---

## Hardware Setup

![SO-101 Robot Setup](/assets/robots-setup.png)

> *Open-source SO-101 Arm Kit from HuggingFace — annotated hardware components.*

| Component | Description |
|---|---|
| **Leader Arm** | Input device for human demonstrations |
| **Follower Arm** | Primary 5-DOF manipulator executing the task |
| **Top-Front Camera** | Provides global spatial context for scene understanding |
| **Wrist Camera** | Mounted on the end-effector to mitigate occlusions and provide precise local visual feedback |

The architecture uses a **1:1 teleoperation mapping**. By moving the leader arm, the operator generates the haptic-visual pairs necessary for the dataset. This leader-follower synergy ensures that the proprioceptive data from the follower is perfectly aligned with the visual demonstrations.

---

## Old Setup — Identified Problems

![ACT Old Setup Inference](assets/act-old.gif)

> *ACT inference on the old setup, the picking task fails.*

The old setup suffered from several issues that hindered model performance and capped the ACT model at a baseline of **3x inference speed**:

- **Bad lighting & inconsistent backgrounds** — introduced high-frequency noise, making feature extraction unstable
- **Camera angle variability** — slight shifts in the top camera resulted in coordinate mapping errors
- **Proprioceptive misalignment** — the operator was teleoperating by looking directly at the follower arm, creating a distribution shift since the operator's control decisions were decoupled from the camera's perspective — the only reality the model actually "sees"

These factors effectively broke the visual feedback loop, leading to spatial distribution shifts that the model could not generalize during autonomous inference.

---

## Teleoperation Methodology

![Teleoperation](assets/teleoperation.gif)

> *Teleoperation session, operating the leader arm while watching exclusively through the camera feeds on the monitor.*

To fix the old setup issues, I implemented a refined methodology:

- **Standardized the lighting** across all recording sessions
- **Viewed the workspace exclusively through the visual feed** (the monitor), ensuring that the demonstration data perfectly matched the model's inference environment

This creates the high-fidelity feedback loop required for successful imitation learning.

---

## Model Inference & Performance

### SmolVLA

![SmolVLA Inference](assets/SmolVLA-inference.gif)

> *SmolVLA inference, the robot successfully completes the pick-and-place task. Note: when the bowl is moved to a new position at the end, the robot fails, this is an out-of-distribution scenario not seen during training.*

---

### SmolVLA — Picking Purple Side

![SmolVLA Inference - Picking Purple Side](assets/SmolVLA-inference-purple.gif)

> *SmolVLA inference, the robot successfully picks the **purple side** of the cube, a color variant not present in the training data. This demonstrates SmolVLA's ability to ground linguistic instructions in visual space.*

---

### ACT Model 

![ACT Model Inference](assets/ACT.gif)

> *ACT model inference, the robot struggles with the pick but ultimately succeeds with minor human assistance.*

---

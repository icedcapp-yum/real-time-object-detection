# RealTime-Object-Detection
Autonomous human-following robot controlled by bare-hand gestures in crowded indoor environments.

> ✅ GitHub: https://github.com/Si-jeong/RealTime-Object-Detection  
> ✅ Demo video available below

---

## ✅ Overview

This project aims to **reduce physical workload for nurses** by automating the task of pulling medical carts using a mobile robot.  
The robot can **identify a specific human target among many people** and follow them, while the **user controls the robot with natural bare-hand gestures** (no devices, gloves, or markers required for control).

✔ Human-following in crowded hallways  
✔ Detect a person with a specific ArUco marker  
✔ If the target changes or is lost, automatically re-identify  
✔ Recognize the user’s hand pose using skeleton estimation + depth sensing  
✔ Toggle robot driving / stop mode with a single hand gesture

---

## ✅ System Demo

▶ **Human following + gesture control (demo video)**  
https://prod-files-secure.s3.us-west-2.amazonaws.com/3931cda4-21ba-40bd-b890-2cc466ab70e1/1406f034-daa8-46a0-a110-6bf0413f06cf/EBRL_%E1%84%83%E1%85%A6%E1%84%86%E1%85%A9.mp4

---

## ✅ Architecture

<p align="center">
  <img src="https://prod-files-secure.s3.us-west-2.amazonaws.com/3931cda4-21ba-40bd-b890-2cc466ab70e1/cb9b5f8e-23ac-418d-9e71-17027b915528/Untitled.png" width="600"/>
</p>

The robot:

1. Detects people + ArUco markers  
2. Identifies the target person (marker-based)  
3. Tracks the user with DeepSORT  
7. Estimates body & hand skeleton  
5. Uses depth to choose the correct hand (filtering interference from others)  
6. Interprets hand gesture (palm) to toggle robot driving  
7. Controls motion based on distance from user

---

## ✅ Algorithm Details

### 1. Person & Marker Detection
- Custom dataset for **person** + **ArUco markers**
- Trained **YOLOv4-tiny** (Darknet → TensorFlow)
- Optimized for real-time inference on Jetson (TensorRT)

### 2. User Identification with DeepSORT
- Track all people
- If a person’s bounding box contains the target marker → set as **user**
- Store their **track_id** as `target_person_id`
- If:
  - target changes (keyboard event), or
  - tracker loses the person  
  → restart detection and re-identification

### 3. Body Skeleton Localization
- Extract body keypoints with **trt_pose**
- Validate that the detected skeleton belongs to the current user by checking the neck keypoint inside their bounding box

### 4. Hand Region Extraction
- Use wrist & elbow keypoints to crop a square region
- Resize to 224×224 for hand gesture classifier (**trt_pose_hand**)

### 5. Distinguishing the User’s Hand (Depth-based Filtering)
When another person’s hand overlaps:

- Use RealSense depth input  
- Compare depth of detected hands
- Select the nearest hand as the user’s hand  
- Implemented using a min-heap priority queue for efficiency

### 6. Gesture-Based Motion Control
| Gesture | Robot Mode |
|---------|------------|
| ✋ Palm shown (4s hold) | Toggle driving / stopping |
| Other gestures | Keep current mode |

### 7. Driving Modes
- **Driving mode:** robot follows user and adjusts speed based on distance (RGB-D camera)
- **Non-driving mode:** robot stops safely

---

## ✅ Development Environment

### Training / Development Server
- Ubuntu 18.04
- CUDA 10.2 / cuDNN 7.6.5 / NVIDIA Driver 470
- Anaconda, Python 3.7.11, TensorFlow 2.3.1, OpenCV 4.2
- Jupyter Lab, VSCode

### Robot Platform
- **NVIDIA Jetson Xavier**, JetPack 4.4
- CUDA 10.2, cuDNN 8.0.0, ROS Melodic
- Python 3.6.9
- TensorRT optimization for YOLOv4-tiny

---

## ✅ Technologies

<p align="center"> 
  <img src="https://img.shields.io/badge/Tensorflow-FF6F00?style=flat-square&logo=Tensorflow&logoColor=white"/> &nbsp
  <img src="https://img.shields.io/badge/YOLOv4-00FFFF?style=flat-square&logo=YOLO&logoColor=white"/> &nbsp   
  <img src="https://img.shields.io/badge/OpenCV-5C3EE8?style=flat-square&logo=OpenCV&logoColor=white"/> &nbsp
  <img src="https://img.shields.io/badge/ROS-22314E?style=flat-square&logo=ROS&logoColor=white"/> &nbsp 
  <img src="https://img.shields.io/badge/Jetson_Xavier-76B900?style=flat-square&logo=NVIDIA&logoColor=white"/> &nbsp
</p>

---

## ✅ My Role

✔ Project Manager (PM)  
✔ Implemented user identification with DeepSORT  
✔ Implemented gesture-based driving control using body + hand skeletons  
✔ Data generation, model training & evaluation  
✔ Real-time robot integration

---

## ✅ Repository

Full code:  
https://github.com/Si-jeong/RealTime-Object-Detection

---

## ✅ Reference Models & Libraries
- YOLOv4-tiny (Darknet → TensorFlow)
- DeepSORT
- `trt_pose` (Body skeleton): https://github.com/NVIDIA-AI-IOT/trt_pose
- `trt_pose_hand` (Hand skeleton): https://github.com/NVIDIA-AI-IOT/trt_pose_hand

---

## ✅ Future Work
- Expand gesture vocabulary (forward, reverse, turn)
- Multi-user handling with priority switching
- Real-time SLAM + obstacle navigation

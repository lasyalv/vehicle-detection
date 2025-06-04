# Vehicle Tracking and Speed Estimation from Traffic Videos

## Introduction

This computer vision project implements automated vehicle detection, tracking, and speed estimation from traffic video feeds. The goal is to support traffic monitoring by identifying vehicles, tracking their trajectories across frames, and computing real-world speeds, which is crucial for traffic planning and law enforcement. It leverages deep learning for detection and classic tracking algorithms. Prior work has shown that OpenCV combined with deep learning can accurately perform vehicle speed estimation from video.


## Dataset

We used recorded traffic videos captured from a stationary road-side camera. Each video is 1920×1080 resolution at 30 FPS, covering a 100-meter stretch of road. No annotated labels are required since object detection will run on frames. However, for evaluation, we annotated a subset of frames (~500 images) with bounding boxes to calculate detection accuracy and tracking correctness. Distance calibration was done via known markers in the scene.

## Methodology

The project pipeline includes:

1. **Frame Extraction:** Using OpenCV, extract frames at 5 FPS for processing to balance accuracy and speed.  
2. **Object Detection:** Apply a pre-trained YOLOv3 model (via OpenCV’s DNN module) to detect vehicles in each frame. YOLO provides bounding boxes with class labels (car, truck, etc.).  
3. **Object Tracking:** Use a multi-object tracker (e.g., `SORT` algorithm or OpenCV’s built-in tracking) to assign consistent IDs to detected vehicles across frames. This enables us to build a trajectory for each vehicle.  
4. **Speed Estimation:** Calibrate pixel distance to real-world distance using known camera parameters. Compute  
We convert pixels/frame to meters/second by considering perspective.  
5. **Post-Processing:** Filter out false detections and smooth the speed values using a moving average to reduce noise.  
6. **Visualization:** Overlay bounding boxes and speed labels on video frames. We also log vehicle IDs and speeds to a CSV for analysis.


## Tools & Techniques

- **Programming:** Python 3.x with OpenCV (`cv2`) for image processing.  
- **Deep Learning:** YOLOv3 model for real-time vehicle detection (using pre-trained COCO weights).  
- **Tracking:** OpenCV trackers (e.g., `KCF` or `CSRT`) or `SORT` (Simple Online Realtime Tracking) to maintain identities.  
- **Video I/O:** `FFmpeg` or OpenCV `VideoCapture` to read and write video.  
- **Data:** `NumPy` for distance calculations, `pandas` for logging results.  
- **Performance:** Utilized GPU acceleration (`CUDA`) for running YOLO to achieve near real-time performance (~15 FPS detection).  
- **Testing:** Measured accuracy by comparing estimated speed vs. manual measurement for sample vehicles.

## Results & Insights

<div align='center'>
  <img src='assets/vehicle_detection.gif' alt='demo'>
 </div
    
- We achieved an average speed estimation error of ±5 km/h on test samples, within acceptable range for monitoring.  
- The system successfully tracked multiple vehicles simultaneously; trajectories showed realistic traffic flow patterns.  
- **Key insight:** Cars at the far end of the frame appeared slower due to perspective; we adjusted our calibration matrix accordingly.  
- **Example outcome:** A demo video clip was produced with bounding boxes and speed labels (e.g., “Car #7 – 52 km/h”) and an output CSV with 500 entries of vehicle speeds and timestamps.  
- These results enable downstream tasks like density estimation or violation detection.  


## Setup & Running Instructions

1. **Environment:** Python 3.7+ with OpenCV (4.x) and NumPy. Install via `pip install -r requirements.txt`.  
2. **Model Files:** Download the YOLOv3 weights and config (`yolov3.weights`, `yolov3.cfg`) and place in `models/`.  
3. **Running Detection:** Execute  
   ```bash
   python detect_track_speed.py --video input.mp4


## Team & Credits

Developed by: Lasya Lalpet Venkata, Harish Miriyala.

We thank OpenCV community tutorials for guidance on vehicle detection and the traffic surveillance lab for providing video data.

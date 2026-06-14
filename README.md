# Perception

⭐ **1. Introduction**

This project builds a perception pipeline for autonomous driving that converts raw camera data into actionable scene understanding. It estimates drivable road space, detects lane boundaries, and computes distances to surrounding objects.

The system combines semantic segmentation, depth-based 3D reconstruction, and classical computer vision techniques to make the environment interpretable for safe vehicle navigation.

<table>
  <tr>
    <td align="center">
      <b></b><br>
      <img src="Perception_GIF.gif" width="700"/>
    </td>
  </tr>
</table>

---

🧩 **2. Challenge**

This project tackles the problem of understanding complex road scenes from noisy and imperfect vision data in autonomous driving.

Key challenges include:

- Converting 2D image + depth data into reliable 3D road understanding.  
- Handling noisy and redundant object detections from vision models.  
- Robustly estimating the ground plane despite outliers in segmentation.  
- Extracting stable lane boundaries from fragmented segmentation outputs.  
- Estimating real-world distances to objects using camera geometry and depth.

---

🎯 **3. Objectives**

- Estimate drivable space using semantic segmentation output.
- Estimate the ground plane.
- Estimate the lane boundaries using semantic segmentation output.
- Compute minimum distance to impact using 2D object detection output.

---

🛠 **4. Tech Stack**

The project combines computer vision techniques with pretrained perception outputs to build a full autonomous driving scene understanding pipeline:

- Python – core implementation of the perception pipeline  
- NumPy – numerical computations for geometry, projections, and filtering  
- OpenCV – edge detection and lane line extraction (Canny + Hough Transform)  
- Matplotlib – visualization of segmentation, lanes, and detections  
- Semantic Segmentation (Pretrained CNN Output) – pixel-wise road scene labels (road, lanes, vehicles, pedestrians)  
- 2D Object Detection (Pretrained Detector Output) – bounding boxes and confidence scores for surrounding objects  
- Depth Maps – 3D reconstruction and distance estimation  
- Camera Geometry – pinhole projection model for lifting 2D pixels into 3D space
  
---



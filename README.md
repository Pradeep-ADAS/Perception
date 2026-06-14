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

🧩 **5. Basics**

---

🧠 **6. Simplified Pipeline Setup**

```mermaid
flowchart TD
    A([🚗 CARLA Simulator Dataset]) --> B[DatasetHandler]

    B --> B1[RGB Image & Depth Map]
    B --> B3[Segmentation Map]
    B --> B4[Object Detections]

    subgraph P1[" 📐 Part 1 — Drivable Space Estimation"]
        direction TB
        C1["3D Reconstruction
        Project depth to 3D space"]
        C2["Road Mask
        Isolate road pixels"]
        C3["Ground Plane Fitting
        RANSAC plane estimation"]
        C4["Drivable Space Mask
        Filter inlier ground pixels"]
        C1 --> C2 --> C3 --> C4
    end

    subgraph P2[" 🛣️ Part 2 — Lane Estimation"]
        direction TB
        D1["Lane Detection
        Edges and Hough lines"]
        D2["Line Merging
        Cluster similar lines"]
        D3["Lane Extrapolation
        Extend to road bounds"]
        D1 --> D2 --> D3
    end

    subgraph P3[" 🚙 Part 3 — Obstacle Distance Estimation"]
        direction TB
        E2["Detection Filtering
        Validate with segmentation"]
        E3["Distance Estimation
        Min 3D Distance per object"]
        E2 --> E3
    end

    B1 --> C1
    B3 --> C2
    B3 --> D1
    B4 --> E2
    B3 --> E2
    C1 --> E3

    C4 --> OUT1([ ✅ 3D Ground Plane & Drivable Space])
    D3 --> OUT2([ ✅ Lane Boundaries])
    E3 --> OUT3([ ✅ Min Distance of Impact from Obstacles])

    style P1 fill:#1e3a5f,stroke:#4a9edd,color:#fff
    style P2 fill:#1e4a2f,stroke:#4add7a,color:#fff
    style P3 fill:#4a1e1e,stroke:#dd4a4a,color:#fff
    style A fill:#2d2d2d,stroke:#aaa,color:#fff
    style OUT1 fill:#1e3a5f,stroke:#4a9edd,color:#fff
    style OUT2 fill:#1e4a2f,stroke:#4add7a,color:#fff
    style OUT3 fill:#4a1e1e,stroke:#dd4a4a,color:#fff
```
**Part 1**
  - **3D Reconstruction**: Converts depth image into a 3D point cloud (spatial map of the scene) using camera geometry.
  - **Road Mask**: Extracts road pixels from the segmentation map. These pixels are used as input for 3D road modeling.
  - **Ground Plane Fitting (RANSAC)** - Fits a flat road surface to the 3D road points from the road mask. RANSAC is a method that finds the best plane while ignoring noisy/outlier points (e.g., cars, pedestrians on the road).
  - **Drivable Space Mask** - Keeps points close to the estimated road plane, representing the physically drivable area.

**Part 2**
  - **Lane Detection**: Finds lane markings by detecting edges and line segments in segmentation output.
  - **Line Merging**: Groups and combines small, similar line segments into one clean lane line.
  - **Lane Extrapolation** → Extends lane lines across the road and picks the lane where the car is currently driving.

**Part 3**
  - **Detection Filtering**: Removes incorrect object detections using segmentation consistency (keeps only likely real objects).
  - **Distance Estimation**: Computes real-world distance by converting pixels inside each bounding box into 3D points and taking the closest one.

---

📈 **7. Results**

**Part 1: Drivable Space Estimation**

<table>
  <tr>
    <td align="center">
      <img src="Input_Image.png" width="100%"/>
      <p><b>Input image for perception</b></p>
    </td>
    <td align="center">
      <img src="Road_Plane.png" width="100%"/>
      <p><b>Identified Drivable Region — Pixel View</b></p>
    </td>
  </tr>
</table>

**Part 2: Lane Estimation**

<table>
  <tr>
    <td align="center">
      <img src="Lane_Boundary.png" width="100%"/>
      <p><b>Lane Boundary Proposals</b></p>
    </td>
    <td align="center">
      <img src="Filtered_Lane_Boundary.png" width="100%"/>
      <p><b>Filtered Lane Lines</b></p>
    </td>
    <td align="center">
      <img src="Current_Lane.png" width="100%"/>
      <p><b>Current Lane Identification</b></p>
    </td>
  </tr>
</table>

**Part 3: Obstacle Distance Estimation**

<table>
  <tr>
    <td align="center">
      <img src="Object_Detected.png" width="100%"/>
      <p><b>Filtered Object(s)</b></p>
    </td>
    <td align="center">
      <img src="Distance.png" width="100%"/>
      <p><b>Minimum Impact Distance</b></p>
    </td>
  </tr>
</table>


---

📈 **8. End-to-End Pipeline Test Case**

This section demonstrates the performance of the full perception pipeline on an unseen test frame. The system processes raw sensor inputs to estimate drivable space, detect lane boundaries, and compute distances to surrounding objects.

The results highlight the robustness of the geometric and vision-based methods under realistic and noisy scene conditions.

<table>
  <tr>
    <td align="center">
      <img src="Test_Input.png" width="100%"/>
      <p><b>Input Scenario</b></p>
    </td>
    <td align="center">
      <img src="Test_Road.png" width="100%"/>
      <p><b>Identified Drivable Region</b></p>
    </td>
  </tr>

  <tr>
    <td align="center">
      <img src="Test_Lane.png" width="100%"/>
      <p><b>Lane Identification</b></p>
    </td>
    <td align="center">
      <img src="Test_Distance.png" width="100%"/>
      <p><b>Distance to Impact</b></p>
    </td>
  </tr>
</table>

---

🧭 **9. Future Extensions**

Potential improvements to further enhance the robustness and scalability of the perception pipeline include:

- Extending evaluation to diverse driving scenes (urban, highway, adverse weather) to test generalization. 
- Explore the possibility of replacing rule-based lane detection with a learning-based lane segmentation model for improved robustness.

---

⚠️ **10. Data Note**

This project was developed as part of a self-driving car perception assignment using simulated data from the CARLA environment. All inputs used in this pipeline (semantic segmentation, depth maps, and 2D object detections) are pre-generated and provided as part of the dataset. 

The focus of this work is on perception logic, geometric reasoning, and classical computer vision techniques for scene understanding.

---

👨‍💻 **11. Skills Demonstrated**

This project demonstrates an end-to-end autonomous driving perception pipeline that transforms raw sensor inputs into structured scene understanding.

I demonstrate my ability to work with:

- Camera-based 3D geometry and point cloud reconstruction 
- Use semantic segmentation outputs for scene understanding
- RANSAC-based ground plane estimation for drivable space detection    
- Rule-based lane detection using classical computer vision (Canny + Hough Transform)  
- Object detection filtering using segmentation consistency  
- 3D distance estimation for obstacle proximity analysis  

---


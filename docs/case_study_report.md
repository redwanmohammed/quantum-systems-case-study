# Catch an UAV – Technical Case Study

## 1. Problem Understanding

Based on the provided sketches, the UAV is expected to pass through a known circular capture ring mounted on the gripper.  
A Full HD camera is integrated in the gripper and looks through the ring.

The objective is to:

- Detect and track the UAV in real time
- Align the robot gripper with the UAV trajectory
- Keep the UAV centered inside the capture ring
- Regulate distance until it reaches the grasp plane


No ready-made software stack is required, only the technical approach.

---

# 2. Proposed Main Approach: What kind of algorithm could be used to track (3D XYZ) and capture the drone?

## Image-Based Visual Servoing (IBVS)

A practical and robust solution is to use Image-Based Visual Servoing (IBVS) with Kalman state estimation.

Because the camera is mounted inside a known circular capture ring, full 3D pose reconstruction is not strictly necessary.  
Instead, robot motion can be controlled directly from image features. This reduces system complexity and improves real-time performance.

This is especially suitable because:

- The ring geometry is known
- The goal is alignment + capture, not full scene reconstruction

## System Pipeline

```text
Camera Image
   ↓
UAV Detection / Segmentation
   ↓
Feature Extraction (u, v, s)
   ↓
Kalman Prediction
   ↓
IBVS Controller
   ↓
Robot TCP Motion (X,Y,Z)
   ↓
Capture Decision

---

# 3. Tracking and Capture Algorithm (XYZ Control)

## Perception Pipeline (per camera frame)

Using the gripper camera:

### Step 1 – UAV Detection / Segmentation

Detect the UAV body using:

- Lightweight CNN detector (YOLO / MobileNet-based)
- OR classical motion / threshold segmentation

### Step 2 – Extract Image Features

For every frame compute:

- **Centroid:** `(u, v)` → UAV position in image
- **Apparent size:** `s` → bounding box size / blob area

The ring center `(u0, v0)` is fixed and known from camera calibration.

---

## Control Logic

## Control Logic(IBVS)

u-u0 -> move X  
v-v0 -> move Y  
s-s0 -> move Z

This continuously drives the UAV toward the center of the ring.

When errors are near zero:
close gripper.

---

# 4. Motion Prediction / State Estimation

Because the UAV is moving and vision has latency, tracking should include prediction.

## Recommended Solution: Kalman Filter

Track state:

```text
[u, v, s, u_dot, v_dot, s_dot]


## 5. What are the challenges of using a single camera, and how to resolve them?

- No depth
- Blur
- Latency
- FOV
- Occlusion
- Lighting

## 6. What other approaches (sensors, etc.) beyond a single camera would you use?

- Stereo Camera
- RGB-D / Depth Camera
- External Motion Capture System -> Examples: Vicon, OptiTrack
- Event camera
- A UWB (Ultra-Wideband) tag on the UAV
- Shared UAV Telemetry / IMU means the drone sends its internal flight data to the robot system in real time.

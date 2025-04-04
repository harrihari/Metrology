# Software Scope and Technical Specification for Subsea Jumper Metrology System (Version 1)

## 1. Introduction

### 1.1 Purpose
The Subsea Jumper Metrology System (Version 1) software is a critical component of a subsea measurement system designed to determine the distance, orientation (pitch, roll, yaw, heading), and vertical distance between multiple subsea assets (e.g., jumper hubs, pipelines, wellheads) at depths up to 5000m. Deployed via a Remotely Operated Vehicle (ROV), the system operates without external GPS, relying on dead-reckoning with the D90 IMU, error correction techniques (ZUPT, Position Updates, Kalman Filtering), and a pressure sensor for depth measurements. 

### 1.2 Scope
This document provides a detailed software scope and technical specification for Version 1, including:
- A comprehensive feature list with detailed explanations to aid implementation.
- Technical requirements, software architecture, and development tools.
- Considerations for real-time performance, fault tolerance, and subsea operational challenges.
- Version 1 excludes Version 2 enhancements (e.g., Camera System with Visual Odometry, Doppler Velocity Log).

### 1.3 Objectives
- Enable high-accuracy navigation and metrology at 5000m depth with ~0.1–0.2m position accuracy.
- Support multi-point surveys to map 3–5 subsea points in a single deployment.
- Integrate with hardware (D90 IMU, pressure sensor, ROV) for data acquisition and control.
- Ensure stability and reliability through error correction and fault-tolerant design.
- Provide real-time monitoring, data logging, and post-processing capabilities.

## 2. System Overview

### 2.1 Software Role
The software serves as the central control and processing unit for the Subsea Jumper Metrology System, managing:
- Data acquisition from the D90 IMU and pressure sensor.
- Navigation and error correction using dead-reckoning, ZUPT, Position Updates, and Kalman Filtering.
- Multi-point survey coordination, including path planning and data association.
- Real-time monitoring, user interaction, and post-mission data analysis.
- Operational workflows involving the detachable PEEK stab for stability.

### 2.2 Performance Goals
- **Position Accuracy**: ~0.1–0.2m after error correction and loop closure.
- **Orientation Accuracy**: Pitch/roll: ~0.03°, heading: ~0.05°.
- **Vertical Distance Accuracy**: ~0.5–0.6m at 5000m depth.
- **Real-Time Processing**: Process sensor data and update estimates at ≥10 Hz.
- **Fault Tolerance**: Maintain operation despite sensor or communication failures.

## 3. Feature List and Detailed Explanations

### 3.1 Data Acquisition
**Description**: Interface with hardware sensors to collect real-time data for navigation and metrology.

- **Sub-Features**:
  - **IMU Data Acquisition**: Retrieve orientation (pitch, roll, yaw, heading) and acceleration data from the D90 IMU.
  - **Pressure Sensor Data Acquisition**: Collect depth measurements from the pressure sensor.
  - **Timestamp Synchronization**: Ensure all data is timestamped for accurate integration.

- **Implementation Details**:
  - **IMU Interface**:
    - Use the RS-232/RS-422 protocol (as per D90 IMU specifications) to communicate with the IMU.
    - Configure communication settings: baud rate (e.g., 115200), 8 data bits, no parity, 1 stop bit.
    - Parse IMU data packets to extract angular rates ($$\(\omega_x, \omega_y, \omega_z\)$$) in rad/s, accelerations (\$$(a_x, a_y, a_z\)$$) in m/s², and orientation (pitch, roll, heading) in degrees.
  - **Pressure Sensor Interface**:
    - Use RS-232/RS-422 to interface with the pressure sensor (e.g., Paroscientific Digiquartz).
    - Parse pressure data (in bar) and convert to depth:
      
     $$\text{Depth} = \frac{\text{Pressure}}{\rho \cdot g}$$
     
      where $$\(\rho = 1025 \, \text{kg/m}^3\) (seawater density), \(g = 9.81 \, \text{m/s}^2\)$$.
  - **Timestamping**:
    - Use a high-resolution system clock (e.g., nanosecond precision) to timestamp each data packet.
    - Synchronize timestamps across sensors to ensure temporal alignment for fusion.

- **Considerations**:
  - Implement error handling for communication failures (e.g., timeouts, CRC errors) with retry mechanisms.
  - Use a circular buffer to store incoming data, preventing loss during high-frequency sampling (e.g., 100 Hz for IMU).

---

### 3.2 Alignment Algorithms
**Description**: Perform initial alignment to establish the IMU’s orientation before navigation.

- **Sub-Features**:
  - **Coarse Alignment (Leveling)**: Calculate pitch and roll using accelerometer data.
  - **Fine Alignment (Gyro-Compassing)**: Determine heading using gyroscope data and Earth’s rotation rate.

- **Implementation Details**:
  - **Coarse Alignment**:
    - Collect accelerometer data while the device is stationary (seated via the PEEK stab).
    - Compute pitch and roll:
      
      $$\text{Pitch} = \arcsin\left(\frac{a_x}{g}\right), \quad \text{Roll} = \arcsin\left(\frac{a_y}{g}\right)$$
    
      where $$\(a_x, a_y\)$$ are accelerometer outputs, and $$\(g = 9.81 \, \text{m/s}^2\)$$.
    - Apply a moving average filter (e.g., over 10 seconds) to reduce noise.
    - Duration: Configurable, default 10–30 seconds.
  - **Fine Alignment**:
    - Collect gyroscope data while stationary.
    - Compute heading using Earth’s rotation rate $$(\(\omega_e = 15^\circ/\text{hr} = 7.292 \times 10^{-5} \, \text{rad/s}\))$$:
     
      $$\text{Heading} = \arctan\left(\frac{-\omega_y}{\omega_x \cos(\phi) + \omega_z \sin(\phi)}\right)$$
      
      where $$\(\omega_x, \omega_y, \omega_z\)$$ are gyro outputs, and $$\(\phi\)$$ is the latitude (user input or approximated).
    - Duration: Configurable, default 1–5 minutes, longer at higher latitudes.
  - **Validation**:
    - Check alignment results against expected ranges (e.g., pitch/roll within ±90°, heading 0–360°).
    - Flag alignment failures if results deviate significantly.

- **Considerations**:
  - Account for environmental noise (e.g., currents) mitigated by the PEEK stab.
  - Provide user feedback on alignment progress and success via the interface.

---

### 3.3 Dead-Reckoning
**Description**: Estimate the system’s position and velocity by integrating IMU data over time.

- **Sub-Features**:
  - **Orientation Tracking**: Maintain orientation using gyroscope data.
  - **Velocity and Position Estimation**: Compute velocity and position by integrating acceleration.

- **Implementation Details**:
  - **Orientation Tracking**:
    - Integrate angular rates to update orientation:
      
      $$\theta(t) = \theta(t-1) + \omega \cdot \Delta t$$
    
      Use quaternions to avoid gimbal lock:
    
     Use quaternions to avoid gimbal lock:
    
$$q(t) = q(t - 1) \otimes \Delta q,\quad \Delta q = \begin{bmatrix} \cos\left(\frac{|\omega|\Delta t}{2}\right) \[8pt] \dfrac{\omega}{|\omega|}\sin\left(\frac{|\omega|\Delta t}{2}\right) \end{bmatrix}$$
    
  - **Velocity and Position**:
    - Transform acceleration from body frame to navigation frame using the rotation matrix derived from orientation.
    - Integrate acceleration to compute velocity and position:
     
      $$v(t) = v(t-1) + a(t) \cdot \Delta t, \quad p(t) = p(t-1) + v(t) \cdot \Delta t$$
      
      Use trapezoidal integration for better accuracy:
      
      $$v(t) = v(t-1) + \frac{a(t) + a(t-1)}{2} \cdot \Delta t$$
      
  - **Initialization**:
    - Set initial position and velocity at a known point (e.g., Hub A coordinates).

- **Considerations**:
  - Mitigate drift using error correction (ZUPT, Position Updates).
  - Optimize $$\(\Delta t\)$$ (e.g., 0.01s for 100 Hz) for real-time performance.

---

### 3.4 Error Correction
**Description**: Reduce navigation errors using ZUPT and Position Updates.

- **Sub-Features**:
  - **Zero Velocity Update (ZUPT)**: Correct velocity errors during stationary periods.
  - **Position Updates**: Reset position estimates at known locations.

- **Implementation Details**:
  - **ZUPT**:
    - Detect stationary periods when the PEEK stab is seated (e.g., velocity < 0.01 m/s for 10 seconds).
    - Set velocity to zero and feed this as a measurement to the Kalman Filter.
    - Duration: Configurable, default 10–30 seconds per ZUPT.
  - **Position Updates**:
    - Use known coordinates (e.g., Hub A at (x, y, z)) provided via user input.
    - Reset the position estimate and update the Kalman Filter with the known position as a measurement.
  - **Trigger Mechanism**:
    - Automatically trigger ZUPT and Position Updates at waypoints, with manual override option.

- **Considerations**:
  - Ensure accurate stationary detection to avoid false ZUPTs.
  - Validate known coordinates for consistency with the local reference frame.

---

### 3.5 Kalman Filter
**Description**: Fuse sensor data and correction updates to optimize state estimates.

- **Sub-Features**:
  - **State Estimation**: Track position, velocity, orientation, and sensor biases.
  - **Prediction and Update**: Predict state forward and correct with measurements.

- **Implementation Details**:
  - **State Vector**:
    
    $$\mathbf{x} = [x, y, z, v_x, v_y, v_z, \theta, \phi, \psi, b_{accel_x}, b_{accel_y}, b_{accel_z}, b_{gyro_x}, b_{gyro_y}, b_{gyro_z}]$$
 
    where $$\(b_{accel}, b_{gyro}\)$$ are bias terms.
  - **Prediction Step**:
    - Use IMU data to propagate the state:
    
      $$\mathbf{x}(t) = f(\mathbf{x}(t-1), \mathbf{u}(t)), \quad \mathbf{u} = [a_x, a_y, a_z, \omega_x, \omega_y, \omega_z]$$
     
    - Compute the state transition matrix and update the covariance.
  - **Update Step**:
    - Incorporate ZUPT (velocity = 0) and Position Updates as measurements.
    - Use an Extended Kalman Filter (EKF) due to nonlinear dynamics:
      - Linearize the system model around the current state.
      - Apply the Kalman gain to update the state and covariance.
  - **Tuning**:
    - Set process noise $$\(Q\)$$ based on IMU noise characteristics (e.g., gyro bias stability: 0.3°/hr).
    - Set measurement noise $$\(R\)$$ based on ZUPT and position update accuracy.

- **Considerations**:
  - Optimize for real-time performance (e.g., <10 ms per update cycle).
  - Monitor filter divergence and implement reset mechanisms if needed.

---

### 3.6 Multi-Point Survey Management
**Description**: Coordinate ROV navigation and data collection across multiple subsea points.

- **Sub-Features**:
  - **Path Planning**: Define and execute a sequence of waypoints.
  - **Waypoint Measurement**: Trigger measurements at each point.
  - **Data Association**: Link measurements to specific assets.

- **Implementation Details**:
  - **Path Planning**:
    - Allow users to input a survey path (e.g., Hub A → Hub B → Hub C → Hub A) via the user interface.
    - Store waypoints as a list of coordinates and associated actions (e.g., ZUPT, Position Update).
  - **Waypoint Measurement**:
    - At each waypoint, trigger ZUPT and/or Position Update based on configuration.
    - Record IMU and pressure sensor data during the pause.
  - **Data Association**:
    - Tag measurements with waypoint IDs and timestamps.
    - Store data in a structured format for post-processing.

- **Considerations**:
  - Support dynamic path adjustments during operation.
  - Ensure ROV feedback (e.g., confirmation of stab seating) before proceeding.

---

### 3.7 Data Logging and Storage
**Description**: Record all data for real-time use and post-mission analysis.

- **Sub-Features**:
  - **Real-Time Logging**: Capture sensor data, state estimates, and system events.
  - **Storage Management**: Organize and store data efficiently.

- **Implementation Details**:
  - Log data in a binary format for efficiency, with fields:
    - Timestamp (ns precision).
    - IMU data (orientation, acceleration, angular rates).
    - Pressure sensor data (depth).
    - State estimates (position, velocity, orientation).
    - System events (e.g., ZUPT, Position Update).
  - Use a 128GB solid-state drive for storage, with a file system supporting large datasets (e.g., ext4).
  - Implement a rolling log to manage storage (e.g., overwrite oldest data if full).

- **Considerations**:
  - Ensure data integrity with checksums or CRC.
  - Support data export to CSV/JSON for post-processing.

---

### 3.8 Real-Time Monitoring and Control
**Description**: Provide operators with live system status and control capabilities.

- **Sub-Features**:
  - **Status Display**: Show sensor data, alignment status, and position estimates.
  - **Control Inputs**: Enable operational commands (e.g., start/stop alignment).

- **Implementation Details**:
  - Develop a real-time dashboard with:
    - Graphs for orientation, velocity, and position.
    - Indicators for system health (e.g., sensor status, alignment progress).
  - Implement control commands:
    - Start/stop alignment.
    - Initiate/pause survey.
    - Trigger manual ZUPT or Position Update.
  - Use a client-server model to transmit data to the surface via the ROV umbilical.

- **Considerations**:
  - Minimize latency (<100 ms) for real-time updates.
  - Include alerts for critical events (e.g., alignment failure).

---

### 3.9 User Interface
**Description**: Provide an intuitive interface for operators to interact with the system.

- **Sub-Features**:
  - **Graphical Display**: Visualize data and survey progress.
  - **Configuration**: Allow parameter adjustments.

- **Implementation Details**:
  - Use Qt (C++) for a cross-platform GUI.
  - Display components:
    - Real-time plots for orientation, position, and depth.
    - Survey map showing waypoints and ROV path.
    - System status (e.g., “Alignment in Progress”, “Survey Active”).
  - Configuration options:
    - Alignment duration (coarse: 10–30s, fine: 1–5 min).
    - ZUPT duration and threshold.
    - Survey path input (waypoint coordinates).

- **Considerations**:
  - Design for usability in high-stress environments.
  - Include tooltips and help documentation.

---

### 3.10 Post-Processing
**Description**: Refine data and generate a subsea asset map after the survey.

- **Sub-Features**:
  - **Loop Closure**: Adjust estimates using redundant measurements.
  - **Map Generation**: Create a cohesive asset layout.

- **Implementation Details**:
  - **Loop Closure**:
    - Use least-squares optimization to minimize errors across revisited points (e.g., Hub A).
    - Adjust position estimates to ensure consistency in loop closure.
  - **Map Generation**:
    - Generate a 3D map with asset positions, orientations, and vertical distances.
    - Export in formats like CSV (for coordinates) and JSON (for metadata).
  - Provide visualization tools (e.g., Python with Matplotlib) for post-mission analysis.

- **Considerations**:
  - Optimize for large datasets (e.g., hours of survey data).
  - Validate map accuracy against known references.

---

## 4. Additional Requirements

- **Fault Tolerance**:
  - Detect sensor failures (e.g., IMU data timeout) and switch to a fallback mode (e.g., last known state).
  - Log errors and notify operators via the UI.
- **Calibration**:
  - Support pre-deployment calibration of IMU and pressure sensor (e.g., bias estimation).
- **Time Synchronization**:
  - Synchronize with UTC via the ROV system before deployment.
- **Security**:
  - Encrypt logged data using AES-256.
  - Restrict software access with user authentication.
- **Performance**:
  - Ensure real-time processing at ≥10 Hz (100 ms cycle time).
  - Optimize memory usage for embedded deployment.

---

## 5. Software Architecture

The software will follow a modular, layered architecture to ensure maintainability and scalability:

- **Hardware Abstraction Layer (HAL)**:
  - Interfaces with IMU, pressure sensor, and ROV systems.
  - Provides abstracted data access to higher layers.
- **Navigation Core**:
  - Alignment Module: Handles coarse and fine alignment.
  - Dead-Reckoning Module: Computes position and velocity.
  - Error Correction Module: Implements ZUPT and Position Updates.
  - Kalman Filter Module: Fuses data for state estimation.
- **Survey Management**:
  - Path Planning Module: Manages waypoints and survey execution.
  - Data Association Module: Links measurements to assets.
- **Data Management**:
  - Logging Module: Handles real-time data storage.
  - Post-Processing Module: Refines data and generates maps.
- **User Interface Layer**:
  - Real-Time Monitoring Module: Displays live data.
  - Control Module: Processes operator commands.
- **Communication Layer**:
  - Manages data transmission to the surface via ROV umbilical.

---

## 6. Development Tools and Technologies

- **Programming Language**:
  - C++ for real-time performance and embedded deployment.
  - Python for post-processing scripts and prototyping.
- **Libraries**:
  - Eigen for linear algebra (Kalman Filter, matrix operations).
  - Qt for GUI development.
  - Boost for threading and serialization.
- **Operating System**:
  - Embedded Linux (e.g., on an ROV control unit) for real-time operation.
- **Version Control**:
  - Git with GitLab/GitHub for source code management.
- **Testing**:
  - Unit tests: Google Test for C++.
  - Integration tests: Simulate sensor data and ROV interactions.
  - Simulation: Use synthetic IMU data to validate navigation algorithms.
- **Documentation**:
  - Doxygen for code documentation.
  - Markdown for user manuals and guides.

---

## 7. Implementation Considerations

- **Real-Time Constraints**:
  - Optimize critical paths (e.g., Kalman Filter updates) to meet 10 Hz requirement.
  - Use multi-threading to separate data acquisition, processing, and UI updates.
- **Fault Tolerance**:
  - Implement watchdog timers to detect and recover from software hangs.
  - Use redundant data storage (e.g., duplicate logs) to prevent data loss.
- **Subsea Challenges**:
  - Account for communication delays between ROV and surface (e.g., buffer UI updates).
  - Design for low-power operation to minimize ROV power draw.
- **Scalability**:
  - Design modules to support future enhancements (e.g., Version 2 features).
  - Use configuration files for tunable parameters (e.g., Kalman Filter covariances).

---


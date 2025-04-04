# Subsea Jumper Metrology System: Product Development Scope Document (Version 1)

## 1. Introduction

### 1.1 Purpose
The Subsea Jumper Metrology System (Version 1) is designed to perform high-accuracy measurements of distance, orientation (pitch, roll, yaw, heading), and vertical distance between multiple subsea assets (e.g., jumper hubs, pipelines, wellheads) at depths up to 5000m. The system enables multi-point surveys in a single deployment, providing a comprehensive map of relative positions for subsea infrastructure. It is deployed via a Remotely Operated Vehicle (ROV) and operates without external GPS, relying on dead-reckoning and advanced error correction techniques. A stab fixed at the bottom of the device ensures stability during stationary periods, enhancing measurement accuracy.

### 1.2 Scope
This document details the product development scope for Version 1 of the Subsea Jumper Metrology System, including:
- Core features: Multi-point surveys, high-accuracy navigation, 5000m depth rating, and a stab for stability.
- Technical specifications, operational workflow, and development plan.
- Challenges, mitigations, and next steps for product development.
- Version 1 excludes Version 2 enhancements (Camera System with Visual Odometry and Doppler Velocity Log).

### 1.3 Target Applications
- Subsea jumper metrology for oil and gas infrastructure.
- Mapping of subsea fields, including pipelines, manifolds, and wellheads.
- Inspection and maintenance planning for deepwater assets.

## 2. System Overview

### 2.1 Key Features
- **Multi-Point Surveys**: Measures and maps multiple subsea points in a single deployment, creating a cohesive map of relative positions.
- **High-Accuracy Navigation**: Uses the D90 IMU from Advanced Navigation for dead-reckoning, with error correction via Zero Velocity Updates (ZUPT), Position Updates, and Kalman Filtering.
- **5000m Depth Rating**: Operates at depths up to 5000m (~500 bar pressure).
- **ROV Deployment with Stab**: Deployed via a 5000m-rated ROV, with a stab fixed at the bottom of the device to enable secure seating on subsea assets during stationary periods.

### 2.2 Performance Goals
- **Distance Accuracy**: ~0.1–0.2m after error correction and multi-point loop closure.
- **Orientation Accuracy**: Pitch/roll: ~0.03°, heading: ~0.05°.
- **Vertical Distance Accuracy**: ~0.5–0.6m at 5000m depth.
- **Survey Capability**: Map multiple points (e.g., 3–5 hubs) in a single deployment with cohesive data integration.

## 3. Technical Specifications

### 3.1 System Components
#### Metrology Device
- **Core Sensor**: D90 IMU (Advanced Navigation)
  - Heading accuracy: ~0.05° (without external aiding).
  - Pitch/roll accuracy: ~0.03°.
  - Gyro bias stability: ~0.3°/hr.
  - Accelerometer bias stability: ~30 µg.
  - Power consumption: ~5W.
  - Size/weight: Compact, <1.5 kg (excluding stab).
- **Additional Sensors**:
  - **Pressure Sensor**: 5000m-rated, 0.01% full-scale accuracy (e.g., Paroscientific Digiquartz), depth accuracy ~0.5–0.6m at 5000m.
    - Range: 0–600 bar (to provide margin for 5000m depth).
    - Power consumption: ~2W.
- **Housing**:
  - Material: Titanium Grade 5 (yield strength ~900 MPa).
  - Wall thickness: 3–5mm, rated for 5000m (~500 bar) with a safety margin to 6000m.
    - Wall thickness calculation:
     
      $$t = \frac{P \cdot r}{\sigma - 0.6P}$$
     
      where $$\(P = 50 \, \text{MPa}\), \(r = 0.05 \, \text{m}\) (100mm diameter housing), \(\sigma = 900 \, \text{MPa}\)$$:
     
      $$t = \frac{50 \cdot 0.05}{900 - 0.6 \cdot 50} = \frac{2.5}{870} \approx 0.00287 \, \text{m} = 2.87 \, \text{mm}$$
    
      A 3–5mm thickness provides a safety margin.
  - Sealing: Redundant O-ring seals (e.g., Viton), pressure-tested to 600 bar.
  - Connectors: 5000m-rated electrical connectors (e.g., SubConn) for power and data.
- **Stab**:
  - **Location**: Fixed at the bottom of the device.
  - **Purpose**: Enables the device to sit securely on subsea assets (e.g., jumper hubs) during stationary periods for alignment, ZUPT, and Position Updates.
  - **Design**:
    - Material: Titanium Grade 5 (same as housing) for corrosion resistance and strength.
    - Shape: Conical or cylindrical with a tapered end to facilitate alignment and seating.
    - Dimensions: ~10–15 cm length, ~2–3 cm diameter at the base, tapering to a point.
    - Weight: ~0.5 kg (to minimize impact on overall device weight).
    - Interface: Mechanically attached to the housing base, ensuring stability under load (e.g., ROV weight, currents).
  - **Compatibility**: Designed to interface with standard subsea hub receptacles or flat surfaces, ensuring secure contact without slippage.
- **Data Logger**: Onboard storage for IMU and pressure sensor data (e.g., 128GB solid-state storage).
  - Power consumption: ~1W.
- **Interface with ROV**: RS-232 or RS-422, power input ~24V DC.
- **Total Power Consumption**: ~8W (D90 IMU: 5W, pressure sensor: 2W, data logger: 1W).
- **Total Weight**: ~2 kg (including stab).

#### ROV
- Depth rating: 5000m.
- Power supply: Provides ~24V DC, ~10W for the metrology device.
- Data transmission: Umbilical connection to surface vessel, supporting real-time data transfer.
- Navigation aids: Cameras and lights for visual identification of subsea assets during multi-point surveys.
- **Stab Deployment**: The ROV positions the device to seat the stab on subsea assets during stationary periods, ensuring stability.

#### Subsea Assets
- Multiple fixed points (e.g., Hub A, Hub B, Hub C) at 5000m depth.
- At least one point (e.g., Hub A) has known coordinates in a local subsea reference frame for Position Updates.
- **Stab Interface**: Assets (e.g., jumper hubs) may have receptacles or flat surfaces compatible with the stab for secure seating.

### 3.2 Environmental Specifications
- **Depth Rating**: 5000m (~500 bar).
- **Temperature Range**: 2–4°C (typical at 5000m depth).
- **Corrosion Resistance**: Titanium housing and stab ensure long-term durability in seawater.

### 3.3 Performance Specifications
- **Orientation**:
  - Pitch/roll: ~0.03° accuracy after coarse alignment.
  - Heading: ~0.05° accuracy after gyro-compassing.
- **Distance**:
  - Dead-reckoning with D90 IMU: ~0.3–0.7m drift per 100m loop.
  - With ZUPT, Position Updates, and Kalman Filter: ~0.1–0.2m accuracy after 3–5 loops.
- **Vertical Distance**:
  - Depth accuracy: ~0.5–0.6m at 5000m (pressure sensor).
- **Multi-Point Survey**:
  - Capable of mapping 3–5 points in a single deployment, with loop closure to ensure consistency.
- **Stability with Stab**:
  - Ensures minimal movement during stationary periods, improving alignment and ZUPT accuracy.

## 4. Operational Workflow

### 4.1 Step 1: Coarse Alignment (Leveling)
- **Objective**: Determine initial pitch and roll to align the D90 IMU with the local gravity vector.
- **Process**:
  - The ROV positions the metrology device at Hub A (5000m depth) and lowers it to seat the stab securely on the hub or a flat surface.
  - The stab ensures the device remains stable, minimizing vibrations or drift due to currents.
  - The D90 IMU uses its accelerometers to measure the gravity vector and calculate pitch and roll:
   
    $$\text{Pitch} = \arcsin\left(\frac{a_x}{g}\right), \quad \text{Roll} = \arcsin\left(\frac{a_y}{g}\right)$$
   
    where $$\(a_x, a_y\)$$ are accelerometer outputs, and $$\(g \approx 9.81 \, \text{m/s}^2\)$$.
  - The stab’s stability enhances the accuracy of this measurement by reducing noise.
- **Duration**: 10–30 seconds.
- **Output**: Pitch and roll accuracy of ~0.03°.

### 4.2 Step 2: Fine Alignment (Gyro-Compassing)
- **Objective**: Determine true heading by sensing the Earth’s rotation rate.
- **Process**:
  - The ROV keeps the device seated on Hub A via the stab.
  - The stab ensures the device remains stationary, minimizing ROV-induced vibrations or movement due to currents.
  - The D90 IMU’s gyroscopes measure the Earth’s rotation rate (\(\omega_e \approx 15°/\text{hr}\)) to estimate heading:
    
    $$\text{Heading} = \arctan\left(\frac{-\omega_y}{\omega_x \cos(\phi) + \omega_z \sin(\phi)}\right)$$

    where $$\(\omega_x, \omega_y, \omega_z\) are gyro outputs, and \(\phi\)$ is the latitude (assumed known or approximated).
  - The D90 IMU’s low gyro bias stability (0.3°/hr) ensures high accuracy.
- **Duration**: 1–5 minutes, depending on latitude and noise levels.
- **Output**: Heading accuracy of ~0.05°.

### 4.3 Step 3: Multi-Point Survey Execution
- **Objective**: Map multiple subsea points in a single deployment, creating a cohesive map of relative positions.
- **Process**:
  - The ROV follows a pre-planned path to visit multiple subsea points (e.g., Hub A → Hub B → Hub C → Hub A).
  - At each point:
    - **Seating with Stab**: The ROV lowers the device to seat the stab on the hub or a flat surface, ensuring stability.
    - **ZUPT**: The ROV pauses (10–30 seconds) for Zero Velocity Updates to correct velocity errors. The stab ensures a true zero-velocity state, improving ZUPT effectiveness.
    - **Position Update**: At known points (e.g., Hub A), the system resets position errors using the known coordinates in the local subsea reference frame.
  - The D90 IMU continuously tracks orientation and estimates position via dead-reckoning by double-integrating acceleration data:
    
    $$\text{Position} = \iint \text{Acceleration} \, dt^2$$
    
  - The pressure sensor measures depth at each point to calculate vertical distances:
   
    $$\text{Depth} = \frac{\text{Pressure}}{\rho \cdot g}$$
   
    where $$\(\rho \approx 1025 \, \text{kg/m}^3\), and \(g \approx 9.81 \, \text{m/s}^2\)$$.
  - Data from all points is logged for post-processing to create a cohesive map.
- **Stab Advantage**: The stab ensures the device remains stationary during pauses, reducing noise in IMU data and improving measurement accuracy.

### 4.4 Step 4: Multiple Loops for Accuracy
- **Objective**: Enhance measurement accuracy through repeated measurements and loop closure.
- **Process**:
  - The ROV performs 3–5 loops across the survey path (e.g., Hub A → Hub B → Hub C → Hub A).
  - At each point, the stab is seated to ensure stability during pauses.
  - Loop closure techniques (e.g., least-squares optimization) are applied during post-processing to refine position estimates.
- **Output**: Position accuracy of ~0.1–0.2m after loop closure.

### 4.5 Step 5: Kalman Filter for Optimal Error Estimation
- **Objective**: Optimize position, velocity, and orientation estimates by fusing sensor data and error corrections.
- **Process**:
  - A Kalman Filter estimates the system’s state (position, velocity, orientation) and error states (gyro bias, accelerometer bias).
  - **State Vector**: Includes position, velocity, orientation, and error terms.
  - **Measurement Updates**:
    - ZUPT: Corrects velocity and bias errors during stationary periods, enhanced by the stab’s stability.
    - Position Update: Corrects position errors at known points (e.g., Hub A).
  - **Prediction Step**: Propagates the state using D90 IMU acceleration and angular rate data between updates.
- **Output**: Position accuracy of ~0.1–0.2m after 3–5 loops.

## 5. Product Development Plan

### 5.1 Phase 1: Concept and Design (3–6 Months)
- **Objectives**:
  - Finalize system architecture and component selection for Version 1.
  - Design the 5000m-rated housing, stab, and ROV interface.
- **Tasks**:
  - Procure the D90 IMU (Advanced Navigation) and a 5000m-rated pressure sensor (e.g., Paroscientific Digiquartz).
  - Design the titanium housing with 3–5mm wall thickness, redundant O-ring seals, and 5000m-rated connectors (e.g., SubConn).
  - Design the stab:
    - Material: Titanium Grade 5.
    - Dimensions: ~10–15 cm length, ~2–3 cm diameter at the base, tapering to a point.
    - Attachment: Mechanically fixed to the housing base, ensuring stability under load.
  - Develop initial software for coarse alignment, gyro-compassing, and multi-point survey path planning.
  - Define the local subsea reference frame for Position Updates.
- **Deliverables**:
  - System architecture document.
  - Housing and stab design specifications.
  - Initial software framework for navigation and error correction.

### 5.2 Phase 2: Prototype Development (6–9 Months)
- **Objectives**:
  - Build and integrate a Version 1 prototype with core components (D90 IMU, pressure sensor, data logger, stab).
  - Implement navigation and error correction algorithms.
- **Tasks**:
  - Assemble the metrology device with the D90 IMU, pressure sensor, data logger, and stab in the titanium housing.
  - Develop software for:
    - Coarse alignment (leveling) and fine alignment (gyro-compassing).
    - ZUPT and Position Updates for error correction, leveraging the stab for stability.
    - Kalman Filter for sensor fusion and optimal error estimation.
    - Path planning and data stitching for multi-point surveys.
  - Design the ROV mounting system and interface (power, data), ensuring the stab can be seated on subsea assets.
- **Deliverables**:
  - Functional Version 1 prototype with stab.
  - Software for navigation, error correction, and multi-point surveys.
  - ROV integration design.

### 5.3 Phase 3: Testing and Validation (6–9 Months)
- **Objectives**:
  - Validate system performance in controlled and real-world conditions.
  - Ensure 5000m depth rating and operational reliability with the stab.
- **Tasks**:
  - **Surface Testing**:
    - Test navigation, multi-point surveys, and error correction in a controlled environment (e.g., test tank).
    - Validate the stab’s ability to ensure stability during coarse alignment, gyro-compassing, ZUPT, and Position Updates.
    - Measure position accuracy (~0.1–0.2m target) over a simulated multi-point survey path.
  - **Pressure Testing**:
    - Test the housing and stab at 600 bar in a hyperbaric chamber to confirm 5000m depth rating with a safety margin.
  - **Subsea Testing**:
    - Deploy the system at 5000m depth to validate performance in real-world conditions.
    - Perform multi-point surveys (e.g., 3–5 points) using the stab to seat the device at each point.
    - Confirm position accuracy, orientation accuracy, and vertical distance measurements.
- **Deliverables**:
  - Test reports for surface, pressure, and subsea testing.
  - Validation of performance goals (position accuracy, depth rating, stab stability).

### 5.4 Phase 4: Production and Deployment (3–6 Months)
- **Objectives**:
  - Finalize production design and prepare for market launch.
  - Train operators and provide documentation.
- **Tasks**:
  - Optimize the design for manufacturing (e.g., standardize housing and stab components, streamline assembly).
  - Produce an initial batch of Version 1 systems for customer trials.
  - Develop user manuals, training
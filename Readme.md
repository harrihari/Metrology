
# Subsea Jumper Metrology System: Technical and Product Development Document

## 1. Introduction

### 1.1 Purpose
The Subsea Jumper Metrology System is designed to perform high-accuracy measurements of distance, orientation (pitch, roll, yaw, heading), and vertical distance between multiple subsea assets (e.g., jumper hubs, pipelines, wellheads) at depths up to 5000m. The system enables multi-point surveys in a single deployment, providing a comprehensive map of relative positions for subsea infrastructure. It is deployed via a Remotely Operated Vehicle (ROV) and operates without external GPS, relying on advanced navigation and error correction techniques.

### 1.2 Scope
This document details the technical specifications, operational workflow, and product development plan for the Subsea Jumper Metrology System. It includes:
- Main features: Multi-point surveys, high-accuracy navigation, and 5000m depth rating.
- Version 2 enhancements: Camera System with Visual Odometry and Doppler Velocity Log (DVL).
- Development phases, challenges, and next steps for bringing the system to market.

### 1.3 Target Applications
- Subsea jumper metrology for oil and gas infrastructure.
- Mapping of subsea fields, including pipelines, manifolds, and wellheads.
- Inspection and maintenance planning for deepwater assets.

## 2. System Overview

### 2.1 Key Features
- **Multi-Point Surveys**: Measures and maps multiple subsea points in a single deployment, creating a cohesive map of relative positions.
- **High-Accuracy Navigation**: Uses the D90 IMU from Advanced Navigation for dead-reckoning, with error correction via ZUPT, Position Updates, and Kalman Filtering.
- **5000m Depth Rating**: Operates at depths up to 5000m (~500 bar pressure).
- **ROV Deployment**: Deployed via a 5000m-rated ROV for power, data transmission, and movement.

### 2.2 Version 2 Enhancements
- **Camera System with Visual Odometry**: Provides an independent motion estimate using visual data, enhancing position accuracy.
- **Doppler Velocity Log (DVL)**: Measures velocity relative to the seafloor, reducing drift in dead-reckoning.

### 2.3 Performance Goals
- **Distance Accuracy**: ~0.05–0.1m after error correction and multi-point loop closure.
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
- **Additional Sensors**:
  - **Pressure Sensor**: 5000m-rated, 0.01% full-scale accuracy (e.g., Paroscientific Digiquartz), depth accuracy ~0.5–0.6m at 5000m.
  - **Camera System (Version 2)**: 5000m-rated stereo camera with high-intensity lights, resolution ~2MP, field of view ~60°.
  - **Doppler Velocity Log (DVL, Version 2)**: 5000m-rated, velocity accuracy ~0.1% of speed (e.g., Teledyne RDI Workhorse Navigator).
- **Housing**:
  - Material: Titanium Grade 5 (yield strength ~900 MPa).
  - Wall thickness: 3–5mm, rated for 5000m (~500 bar) with a safety margin to 6000m.
  - Sealing: Redundant O-ring seals (e.g., Viton), pressure-tested to 600 bar.
  - Connectors: 5000m-rated electrical connectors (e.g., SubConn) for power and data.
- **Data Logger**: Onboard storage for IMU, pressure, camera, and DVL data (e.g., 128GB solid-state storage).
- **Interface with ROV**: RS-232 or RS-422, power input ~24V DC.

#### ROV
- Depth rating: 5000m.
- Power supply: Provides ~24V DC, ~50W for the metrology device (including version 2 sensors).
- Data transmission: Umbilical connection to surface vessel, supporting real-time data transfer.
- Navigation aids: Cameras and lights for visual identification of subsea assets.

#### Subsea Assets
- Multiple fixed points (e.g., Hub A, Hub B, Hub C) at 5000m depth.
- At least one point (e.g., Hub A) has known coordinates in a local subsea reference frame for Position Updates.

### 3.2 Environmental Specifications
- **Depth Rating**: 5000m (~500 bar).
- **Temperature Range**: 2–4°C (typical at 5000m depth).
- **Corrosion Resistance**: Titanium housing ensures long-term durability in seawater.

### 3.3 Performance Specifications
- **Orientation**:
  - Pitch/roll: ~0.03° accuracy after coarse alignment.
  - Heading: ~0.05° accuracy after gyro-compassing.
- **Distance**:
  - Dead-reckoning with D90 IMU: ~0.3–0.7m drift per 100m loop.
  - With ZUPT, Position Updates, and Kalman Filter: ~0.1–0.2m accuracy.
  - With version 2 features (DVL, visual odometry): ~0.05–0.1m accuracy.
- **Vertical Distance**:
  - Depth accuracy: ~0.5–0.6m at 5000m (pressure sensor).
- **Multi-Point Survey**:
  - Capable of mapping 3–5 points in a single deployment, with loop closure to ensure consistency.

## 4. Operational Workflow

### 4.1 Step 1: Coarse Alignment (Leveling)
- **Objective**: Determine initial pitch and roll.
- **Process**:
  - The ROV holds a stationary position at Hub A (5000m depth).
  - The D90 IMU uses accelerometers to measure the gravity vector:
  
     $$\text{Pitch} = \arcsin\left(\frac{a_x}{g}\right), \quad \text{Roll} = \arcsin\left(\frac{a_y}{g}\right)$$
   
    where $$\(a_x, a_y\) are accelerometer outputs, and \(g \approx 9.81 \, \text{m/s}^2\)$$.
- **Duration**: 10–30 seconds.

### 4.2 Step 2: Fine Alignment (Gyro-Compassing)
- **Objective**: Determine true heading using Earth’s rotation.
- **Process**:
  - The ROV remains stationary at Hub A.
  - The D90 IMU’s gyroscopes measure Earth’s rotation rate $(\(\omega_e \approx 15°/\text{hr}\))$$ to estimate heading:
    
    $$\text{Heading} = \arctan\left(\frac{-\omega_y}{\omega_x \cos(\phi) + \omega_z \sin(\phi)}\right)$$
    
    where $$\(\omega_x, \omega_y, \omega_z\) are gyro outputs, and \(\phi\)$$ is the latitude.
- **Duration**: 1–5 minutes.

### 4.3 Step 3: Multi-Point Survey Execution
- **Objective**: Map multiple subsea points in a single deployment.
- **Process**:
  - The ROV follows a pre-planned path (e.g., Hub A → Hub B → Hub C → Hub A).
  - At each point:
    - **ZUPT**: The ROV pauses (10–30 seconds) for Zero Velocity Updates to correct velocity errors.
    - **Position Update**: At known points (e.g., Hub A), the system resets position errors using known coordinates.
  - **Version 2 Enhancements**:
    - **Camera System with Visual Odometry**: Captures images and estimates ROV motion by tracking features across frames.
    - **DVL**: Measures velocity relative to the seafloor with ~0.1% accuracy.
  - The D90 IMU continuously tracks orientation and estimates position via dead-reckoning.

### 4.4 Step 4: Multiple Loops for Accuracy
- **Objective**: Enhance accuracy through repeated measurements.
- **Process**:
  - The ROV performs 3–5 loops across the survey path.
  - Loop closure techniques (e.g., least-squares optimization) refine position estimates.

### 4.5 Step 5: Kalman Filter Sensor Fusion
- **Objective**: Optimize position, velocity, and orientation estimates.
- **Process**:
  - Fuses D90 IMU, DVL (version 2), visual odometry (version 2), ZUPT, and Position Update data.
  - State vector includes position, velocity, orientation, and error terms (e.g., gyro bias, accelerometer bias).
  - Achieves ~0.05–0.1m position accuracy with version 2 features.

## 5. Version 2 Features
### 5.1 Camera System with Visual Odometry
- **Description**: A 5000m-rated stereo camera with high-intensity lights captures images of the seafloor or subsea assets. Visual odometry algorithms estimate ROV motion by tracking features across frames.
- **Specifications**:
  - Camera: Stereo, ~2MP resolution, 60° field of view.
  - Lights: High-intensity LED, 5000m-rated.
  - Power: ~10W.
- **Benefits**:
  - Provides an independent motion estimate, reducing reliance on IMU-based dead-reckoning.
  - Enhances accuracy near feature-rich areas (e.g., hubs).
- **Integration**: Visual odometry data is fused with D90 IMU and DVL data in the Kalman Filter.

### 5.2 Doppler Velocity Log (DVL)
- **Description**: A 5000m-rated DVL measures ROV velocity relative to the seafloor using acoustic beams.
- **Specifications**:
  - Velocity accuracy: ~0.1% of speed.
  - Range: Up to 50m from seafloor.
  - Power: ~15W.
- **Benefits**:
  - Reduces velocity drift, improving dead-reckoning accuracy.
  - Enhances navigation over longer multi-point survey paths.
- **Integration**: DVL velocity data is incorporated into the Kalman Filter, reducing position drift to ~0.05–0.1m.

## 6. Product Development Plan

### 6.1 Phase 1: Concept and Design (3–6 Months)
- **Objectives**:
  - Finalize system architecture and component selection.
  - Design the 5000m-rated housing and ROV interface.
- **Tasks**:
  - Select 5000m-rated camera and DVL hardware (e.g., Teledyne RDI DVL, Subsea Tech stereo camera).
  - Design titanium housing with 3–5mm wall thickness, redundant seals, and 5000m-rated connectors.
  - Develop initial software for coarse alignment, gyro-compassing, and multi-point survey path planning.

### 6.2 Phase 2: Prototype Development (6–9 Months)
- **Objectives**:
  - Build and integrate a prototype with all components (D90 IMU, pressure sensor, camera, DVL).
  - Implement navigation and error correction algorithms.
- **Tasks**:
  - Assemble the metrology device with D90 IMU, pressure sensor, camera, and DVL in the titanium housing.
  - Develop software for ZUPT, Position Updates, and Kalman Filter sensor fusion.
  - Implement visual odometry algorithms for the camera system.
  - Design ROV mounting system and interface (power, data).

### 6.3 Phase 3: Testing and Validation (6–9 Months)
- **Objectives**:
  - Validate system performance in controlled and real-world conditions.
  - Ensure 5000m depth rating and operational reliability.
- **Tasks**:
  - **Surface Testing**: Test navigation, multi-point surveys, and version 2 features in a test tank.
  - **Pressure Testing**: Test the housing at 600 bar in a hyperbaric chamber.
  - **Subsea Testing**: Deploy the system at 5000m depth to validate performance, accuracy, and multi-point survey capability.

### 6.4 Phase 4: Production and Deployment (3–6 Months)
- **Objectives**:
  - Finalize production design and prepare for market launch.
  - Train operators and provide documentation.
- **Tasks**:
  - Optimize design for manufacturing (e.g., standardize housing components).
  - Produce initial batch of systems for customer trials.
  - Develop user manuals, training materials, and support infrastructure.

## 7. Challenges and Mitigations
- **Multi-Point Surveys**:
  - **Challenge**: Cumulative errors over longer paths.
  - **Mitigation**: Use Position Updates at known points and loop closure to reset errors.
- **Camera System**:
  - **Challenge**: Reduced visibility due to turbidity.
  - **Mitigation**: Use high-intensity lights and image enhancement algorithms.
- **DVL**:
  - **Challenge**: Loss of seafloor lock in uneven terrain.
  - **Mitigation**: Optimize DVL mounting and use multi-beam models.
- **Power and Data**:
  - **Challenge**: Increased demand from version 2 sensors.
  - **Mitigation**: Ensure ROV supports additional power (~30W total) and bandwidth.
- **Gyro-Compassing at Depth**:
  - **Challenge**: Environmental noise affecting Earth’s rotation measurement.
  - **Mitigation**: Minimize ROV vibrations during alignment and leverage D90 IMU’s low gyro bias (0.3°/hr).

## 8. Next Steps
- **Component Procurement**: Source 5000m-rated camera and DVL hardware.
- **Software Development**:
  - Finalize path-planning and data stitching for multi-point surveys.
  - Develop visual odometry algorithms and enhance Kalman Filter for sensor fusion.
- **Prototype Testing**:
  - Conduct surface tests in a tank to validate navigation and version 2 features.
  - Perform pressure tests at 600 bar to confirm 5000m depth rating.
- **Field Trials**:
  - Deploy the system at 5000m depth to test multi-point surveys and overall performance.
- **Documentation**:
  - Prepare technical specifications, user manuals, and training materials.

## 9. Conclusion
The Subsea Jumper Metrology System is a high-accuracy, 5000m-rated solution for subsea metrology, with multi-point surveys as a core feature. Version 2 enhancements (Camera System with Visual Odometry and DVL) improve accuracy to ~0.05–0.1m, making the system versatile for mapping complex subsea fields. The development plan ensures a structured path to market, addressing challenges through rigorous testing and validation. This system is poised to meet the needs of deepwater oil and gas operators, offering efficiency, precision, and reliability.

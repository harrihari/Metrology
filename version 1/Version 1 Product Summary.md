# Subsea Jumper Metrology System: Product Development Scope Document (Version 1)

## 1. Introduction

### 1.1 Purpose
The Subsea Jumper Metrology System (Version 1) is a high-precision measurement tool designed to determine the distance, orientation (pitch, roll, yaw, heading), and vertical distance between multiple subsea assets (e.g., jumper hubs, pipelines, wellheads) at depths up to 5000m. The system supports multi-point surveys in a single deployment, enabling the creation of a cohesive map of relative positions for subsea infrastructure. Deployed via a Remotely Operated Vehicle (ROV), it operates without external GPS, relying on dead-reckoning and advanced error correction techniques. 

### 1.2 Scope
This document outlines the product development scope for Version 1 of the Subsea Jumper Metrology System, including:
- Core features: Multi-point surveys, high-accuracy navigation, 5000m depth rating, and a detachable stab.
- Technical specifications, operational workflow, and development plan.
- Challenges, mitigations, and next steps for product development.
- Version 2 enhancements (e.g., Camera System with Visual Odometry, Doppler Velocity Log).

### 1.3 Target Applications
- Subsea jumper metrology for oil and gas infrastructure.
- Mapping of subsea fields, including pipelines, manifolds, and wellheads.
- Inspection and maintenance planning for deepwater assets.

### 1.4 Objectives
- Develop a reliable, cost-effective system for subsea metrology at 5000m depth.
- Achieve position accuracy of ~0.1–0.2m through dead-reckoning and error correction.
- Enable multi-point surveys to map 3–5 subsea points in a single deployment.
- Ensure stability during measurements with a detachable PEEK stab.

## 2. System Overview

### 2.1 Key Features
- **Multi-Point Surveys**: Measures and maps multiple subsea points in a single deployment, creating a cohesive map of relative positions.
- **High-Accuracy Navigation**: Utilizes the D90 IMU from Advanced Navigation for dead-reckoning, with error correction via Zero Velocity Updates (ZUPT), Position Updates, and Kalman Filtering.
- **5000m Depth Rating**: Operates at depths up to 5000m (~500 bar pressure).
- **ROV Deployment with Detachable Stab**: Deployed via a 5000m-rated ROV, with a detachable PEEK stab for stability during stationary periods.

### 2.2 Performance Goals
- **Distance Accuracy**: ~0.1–0.2m after error correction and multi-point loop closure.
- **Orientation Accuracy**: Pitch/roll: ~0.03°, heading: ~0.05°.
- **Vertical Distance Accuracy**: ~0.5–0.6m at 5000m depth.
- **Survey Capability**: Map 3–5 points in a single deployment with cohesive data integration.
- **Stability**: Enhanced by the detachable stab during stationary periods.

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
  - **Stab Attachment Point**: Threaded interface at the bottom of the housing for secure attachment of the detachable stab.
- **Detachable Stab**:
  - **Material**: PEEK (Polyetheretherketone), selected for its high strength (~100 MPa tensile strength), pressure resistance, and corrosion resistance in seawater.
  - **Design**:
    - Shape: Tapered conical, ~10–15 cm long, ~2–3 cm diameter at the base, tapering to a point for easy alignment and seating.
    - Weight: ~0.2–0.3 kg, lightweight to minimize impact on device buoyancy and facilitate ROV handling.
    - Attachment Mechanism: Threaded interface with ROV-friendly grips (e.g., ridges or handles) for easy attachment/detachment by the ROV’s manipulator.
  - **Purpose**: Provides stability by seating the device on subsea assets (e.g., jumper hubs) during stationary periods for alignment, ZUPT, and Position Updates.
  - **Compatibility**: Designed to interface with standard subsea hub receptacles or flat surfaces, ensuring secure contact without slippage.
- **Data Logger**: Onboard storage for IMU and pressure sensor data (e.g., 128GB solid-state storage).
  - Power consumption: ~1W.
- **Interface with ROV**: RS-232 or RS-422, power input ~24V DC.
- **Total Power Consumption**: ~8W (D90 IMU: 5W, pressure sensor: 2W, data logger: 1W).

#### ROV
- Depth rating: 5000m.
- Power supply: Provides ~24V DC, ~10W for the metrology device.
- Data transmission: Umbilical connection to surface vessel, supporting real-time data transfer.
- Navigation aids: Cameras and lights for visual identification of subsea assets during multi-point surveys.
- **Stab Handling**: The ROV’s manipulator attaches and detaches the PEEK stab before and after operations, ensuring secure seating during measurements.

#### Subsea Assets
- Multiple fixed points (e.g., Hub A, Hub B, Hub C) at 5000m depth.
- At least one point (e.g., Hub A) has known coordinates in a local subsea reference frame for Position Updates.
- **Stab Interface**: Assets (e.g., jumper hubs) may have receptacles or flat surfaces compatible with the stab for secure seating.

### 3.2 Environmental Specifications
- **Depth Rating**: 5000m (~500 bar).
- **Temperature Range**: 2–4°C (typical at 5000m depth).
- **Corrosion Resistance**: Titanium housing and PEEK stab ensure long-term durability in seawater.

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

### 4.1 Pre-Operations: Attach Stab
- **Objective**: Secure the detachable PEEK stab to the metrology device before deployment.
- **Process**:
  - The ROV’s manipulator screws the PEEK stab into the threaded attachment point at the bottom of the device’s housing.
  - The stab’s ROV-friendly grips (e.g., ridges) allow the manipulator to securely grasp and rotate it.
  - The operator verifies the attachment using the ROV’s cameras, ensuring the stab is firmly in place.
- **Duration**: ~1–2 minutes.

### 4.2 Step 1: Coarse Alignment (Leveling)
- **Objective**: Determine initial pitch and roll to align the D90 IMU with the local gravity vector.
- **Process**:
  - The ROV positions the metrology device at Hub A (5000m depth) and lowers it to seat the PEEK stab securely on the hub or a flat surface.
  - The stab ensures the device remains stable, minimizing vibrations or drift due to currents.
  - The D90 IMU uses its accelerometers to measure the gravity vector and calculate pitch and roll:
    
    $$\text{Pitch} = \arcsin\left(\frac{a_x}{g}\right), \quad \text{Roll} = \arcsin\left(\frac{a_y}{g}\right)$$
    
    where $$\(a_x, a_y\)$$ are accelerometer outputs, and $$\(g \approx 9.81 \, \text{m/s}^2\)$$.
  - The stab’s stability enhances the accuracy of this measurement by reducing noise.
- **Duration**: 10–30 seconds.
- **Output**: Pitch and roll accuracy of ~0.03°.

### 4.3 Step 2: Fine Alignment (Gyro-Compassing)
- **Objective**: Determine true heading by sensing the Earth’s rotation rate.
- **Process**:
  - The ROV keeps the device seated on Hub A via the PEEK stab.
  - The stab ensures the device remains stationary, minimizing ROV-induced vibrations or movement due to currents.
  - The D90 IMU’s gyroscopes measure the Earth’s rotation rate (\(\omega_e \approx 15°/\text{hr}\)) to estimate heading:
   
    $$\text{Heading} = \arctan\left(\frac{-\omega_y}{\omega_x \cos(\phi) + \omega_z \sin(\phi)}\right)$$
   
    where $$\(\omega_x, \omega_y, \omega_z\)$$ are gyro outputs, and $$\(\phi\)$$ is the latitude (assumed known or approximated).
  - The D90 IMU’s low gyro bias stability (0.3°/hr) ensures high accuracy.
- **Duration**: 1–5 minutes, depending on latitude and noise levels.
- **Output**: Heading accuracy of ~0.05°.

### 4.4 Step 3: Multi-Point Survey Execution
- **Objective**: Map multiple subsea points in a single deployment, creating a cohesive map of relative positions.
- **Process**:
  - The ROV follows a pre-planned path to visit multiple subsea points (e.g., Hub A → Hub B → Hub C → Hub A).
  - At each point:
    - **Seating with Stab**: The ROV lowers the device to seat the PEEK stab on the hub or a flat surface, ensuring stability.
    - **ZUPT**: The ROV pauses (10–30 seconds) for Zero Velocity Updates to correct velocity errors. The stab ensures a true zero-velocity state, improving ZUPT effectiveness.
    - **Position Update**: At known points (e.g., Hub A), the system resets position errors using the known coordinates in the local subsea reference frame.
  - The D90 IMU continuously tracks orientation and estimates position via dead-reckoning by double-integrating acceleration data:
    
    $$\text{Position} = \iint \text{Acceleration} \, dt^2$$
  
  - The pressure sensor measures depth at each point to calculate vertical distances:
   
    $$\text{Depth} = \frac{\text{Pressure}}{\rho \cdot g}$$
    
    where $$\(\rho \approx 1025 \, \text{kg/m}^3\), and \(g \approx 9.81 \, \text{m/s}^2\)$$.
  - Data from all points is logged for post-processing to create a cohesive map.
- **Stab Advantage**: The PEEK stab ensures the device remains stationary during pauses, reducing noise in IMU data and improving measurement accuracy.

### 4.5 Step 4: Multiple Loops for Accuracy
- **Objective**: Enhance measurement accuracy through repeated measurements and loop closure.
- **Process**:
  - The ROV performs 3–5 loops across the survey path (e.g., Hub A → Hub B → Hub C → Hub A).
  - At each point, the PEEK stab is seated to ensure stability during pauses.
  - Loop closure techniques (e.g., least-squares optimization) are applied during post-processing to refine position estimates.
- **Output**: Position accuracy of ~0.1–0.2m after loop closure.

### 4.6 Step 5: Kalman Filter for Optimal Error Estimation
- **Objective**: Optimize position, velocity, and orientation estimates by fusing sensor data and error corrections.
- **Process**:
  - A Kalman Filter estimates the system’s state (position, velocity, orientation) and error states (gyro bias, accelerometer bias).
  - **State Vector**: Includes position, velocity, orientation, and error terms.
  - **Measurement Updates**:
    - ZUPT: Corrects velocity and bias errors during stationary periods, enhanced by the stab’s stability.
    - Position Update: Corrects position errors at known points (e.g., Hub A).
  - **Prediction Step**: Propagates the state using D90 IMU acceleration and angular rate data between updates.
- **Output**: Position accuracy of ~0.1–0.2m after 3–5 loops.

### 4.7 Post-Operations: Detach Stab
- **Objective**: Remove the PEEK stab after operations for storage or replacement.
- **Process**:
  - The ROV’s manipulator unscrews the stab from the device’s housing.
  - The stab is stored on the ROV or in a designated container for future use.
- **Duration**: ~1–2 minutes.

## 5. Product Development Plan

### 5.1 Phase 1: Concept and Design (3–6 Months)
- **Objectives**:
  - Finalize system architecture and component selection for Version 1.
  - Design the 5000m-rated housing, detachable PEEK stab, and ROV interface.
- **Tasks**:
  - Procure the D90 IMU (Advanced Navigation) and a 5000m-rated pressure sensor (e.g., Paroscientific Digiquartz).
  - Design the titanium housing with 3–5mm wall thickness, redundant O-ring seals, and 5000m-rated connectors (e.g., SubConn).
  - Design the detachable PEEK stab:
    - Material: PEEK (Polyetheretherketone).
    - Dimensions: ~10–15 cm length, ~2–3 cm base diameter, tapering to a point.
    - Attachment: Threaded interface with ROV-friendly grips for easy attachment/detachment.
  - Design the housing’s stab attachment point (threaded interface) to ensure secure connection.
  - Develop initial software for coarse alignment, gyro-compassing, and multi-point survey path planning.
  - Define the local subsea reference frame for Position Updates.
- **Deliverables**:
  - System architecture document.
  - Housing and PEEK stab design specifications.
  - Initial software framework for navigation and error correction.

### 5.2 Phase 2: Prototype Development (6–9 Months)
- **Objectives**:
  - Build and integrate a Version 1 prototype with core components (D90 IMU, pressure sensor, data logger, detachable stab).
  - Implement navigation and error correction algorithms.
- **Tasks**:
  - Assemble the metrology device with the D90 IMU, pressure sensor, and data logger in the titanium housing.
  - Manufacture the detachable PEEK stab and integrate it with the housing’s threaded attachment point.
  - Develop software for:
    - Coarse alignment (leveling) and fine alignment (gyro-compassing).
    - ZUPT and Position Updates for error correction, leveraging the stab for stability.
    - Kalman Filter for sensor fusion and optimal error estimation.
    - Path planning and data stitching for multi-point surveys.
  - Design the ROV mounting system and interface (power, data), ensuring the stab can be seated on subsea assets.
- **Deliverables**:
  - Functional Version 1 prototype with detachable stab.
  - Software for navigation, error correction, and multi-point surveys.
  - ROV integration design.

### 5.3 Phase 3: Testing and Validation (6–9 Months)
- **Objectives**:
  - Validate system performance in controlled and real-world conditions.
  - Ensure 5000m depth rating and operational reliability with the detachable stab.
- **Tasks**:
  - **Surface Testing**:
    - Test navigation, multi-point surveys, and error correction in a controlled environment (e.g., test tank).
    - Validate the PEEK stab’s attachment/detachment process using an ROV manipulator.
    - Confirm the stab’s ability to ensure stability during coarse alignment, gyro-compassing, ZUPT, and Position Updates.
    - Measure position accuracy (~0.1–0.2m target) over a simulated multi-point survey path.
  - **Pressure Testing**:
    - Test the housing and attached PEEK stab at 600 bar in a hyperbaric chamber to confirm 5000m depth rating with a safety margin.
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
  - Optimize the design for manufacturing (e.g., standardize housing and PEEK stab components, streamline assembly).
  - Produce an initial batch of Version 1 systems for customer trials.
  - Develop user manuals, training materials, and support infrastructure, including instructions for stab attachment/detachment.
  - Conduct customer trials with subsea operators to gather feedback and refine the system.
- **Deliverables**:
  - Production-ready Version 1 system.
  - User manuals, training materials, and support documentation.
  - Customer trial feedback report.

## 6. Challenges and Mitigations

### 6.1 Navigation Accuracy
- **Challenge**: Cumulative errors in dead-reckoning over longer multi-point survey paths (~0.3–0.7m drift per 100m loop).
- **Mitigation**:
  - Use ZUPT and Position Updates at known points to reset errors.
  - Perform 3–5 loops and apply loop closure techniques to achieve ~0.1–0.2m accuracy.
  - Leverage the D90 IMU’s low bias stability (0.3°/hr gyro, 30 µg accelerometer).

### 6.2 Gyro-Compassing at 5000m Depth
- **Challenge**: Environmental noise (e.g., currents) may affect gyro-compassing.
- **Mitigation**:
  - Use the PEEK stab to ensure the device remains stationary, minimizing vibrations.
  - Extend gyro-compassing duration if needed to improve accuracy.

### 6.3 ZUPT Effectiveness
- **Challenge**: Minor ROV movement during ZUPT due to currents.
- **Mitigation**:
  - The PEEK stab ensures a true zero-velocity state by securely seating the device.
  - Apply a velocity threshold (< 0.01 m/s) for ZUPT.

### 6.4 Stab Attachment/Detachment
- **Challenge**: Ensuring secure attachment and easy detachment by the ROV.
- **Mitigation**:
  - Use a threaded interface with a locking mechanism to prevent loosening.
  - Design ROV-friendly grips on the stab for easy handling.
  - Test the attachment/detachment process in a controlled environment.

### 6.5 Stab Durability
- **Challenge**: Potential wear or damage to the PEEK stab during repeated use.
- **Mitigation**:
  - PEEK’s high strength and durability ensure longevity.
  - The detachable design allows for easy replacement if needed.

### 6.6 5000m Depth Rating
- **Challenge**: Housing and stab must withstand ~500 bar pressure.
- **Mitigation**:
  - Titanium housing with 3–5mm wall thickness, rated for 5000m.
  - PEEK stab, proven for subsea applications at 5000m, pressure-tested to 600 bar.

## 7. Conclusion
The Subsea Jumper Metrology System (Version 1) is a reliable, cost-effective solution for subsea metrology at 5000m depth, with multi-point surveys and a detachable PEEK stab as core features. It achieves ~0.1–0.2m position accuracy through advanced navigation and error correction, meeting the needs of deepwater oil and gas operators. The development plan ensures a structured path to market, with rigorous testing to address challenges and validate performance.


# Software User Story: Subsea Jumper Metrology System (Version 1)

## Title
**As a subsea operator, I want a software system to accurately measure and map the positions of multiple subsea assets in a single deployment, with a general idea of the results before completion, so that I can efficiently plan jumper installations and maintenance at 5000m depth.**

---

## User Story Overview

### Persona
- **Name**: Harry Tackey-Otoo
- **Role**: Senior Survey Engineer
- **Background**: Harry is a seasoned subsea operator with 10 years of experience in deepwater oil and gas operations. He works for an offshore energy company and is responsible for overseeing jumper installations and subsea asset maintenance. Harry relies on precise metrology data to ensure jumpers fit correctly between hubs, minimizing costly errors.
- **Needs**: Harry needs a reliable, user-friendly system that provides accurate measurements, real-time feedback, and a preliminary view of results during the survey. This allows him to make informed decisions on the fly, such as adjusting the survey path or addressing potential issues before completion.

### Additional Stakeholders
- **ROV Pilot (Benedict)**: Operates the ROV to deploy the metrology device and navigate between subsea assets. Needs clear instructions and real-time system status.
- **Data Analyst (Elvis)**: Processes post-mission data to generate detailed maps and reports for engineering teams. Needs structured, reliable data exports.

---

## User Story Details

### As a subsea operator, I want to:
- **Measure distances, orientations, and vertical distances** between multiple subsea assets (e.g., jumper hubs) with high accuracy (~0.1–0.2m for position, ~0.03° for pitch/roll, ~0.05° for heading, ~0.5–0.6m for depth).
- **Perform multi-point surveys** in a single deployment to map 3–5 assets, reducing operational time and costs.
- **Get a general idea of the results before survey completion**, so I can assess progress and make adjustments if needed.
- **Monitor the system in real-time** to confirm it’s functioning correctly and to make adjustments during the survey.
- **Log all data** for post-mission analysis and generate a comprehensive map of asset positions.
- **Operate the system at 5000m depth** without external GPS, relying on dead-reckoning and error correction.

### So that I can:
- Plan jumper installations accurately, ensuring proper fit and alignment.
- Make informed decisions during the survey, such as adjusting the path or addressing issues early.
- Minimize operational downtime by completing surveys efficiently.
- Provide engineering teams with reliable data for maintenance and future planning.
- Ensure safety and reliability in the harsh subsea environment.

---

## Scenarios

### Scenario 1: Pre-Deployment Setup
- **Given** I am preparing for a subsea survey at 5000m depth,
- **When** I configure the system before deployment,
- **Then** I can:
  - Input the survey path (e.g., Hub A → Hub B → Hub C → Hub A) with known coordinates for Hub A.
  - Set alignment durations (e.g., 30s for coarse, 3 min for fine).
  - Define thresholds for error correction (e.g., ZUPT velocity threshold).

### Scenario 2: Initial Alignment at Hub A
- **Given** the ROV has deployed the device to Hub A,
- **When** I initiate the alignment process,
- **Then** the system:
  - Performs coarse alignment to determine pitch and roll (~0.03° accuracy).
  - Performs fine alignment (gyro-compassing) to determine heading (~0.05° accuracy).
  - Displays alignment progress and results in real-time, with alerts for failures (e.g., excessive noise).

### Scenario 3: Multi-Point Survey Execution
- **Given** the system is aligned and ready,
- **When** the ROV navigates to each waypoint (e.g., Hub B, Hub C),
- **Then** the system:
  - Tracks position using dead-reckoning with the D90 IMU.
  - Triggers ZUPT when the ROV pauses at each waypoint, correcting velocity errors.
  - Applies Position Updates at known points (e.g., Hub A) to reset position errors.
  - Logs all data (IMU, pressure, position estimates) with timestamps.

### Scenario 4: Jumper Metrology Between Two Hubs
- **Given** I need to measure the distance and orientation between Hub A and Hub B for jumper installation,
- **When** the ROV completes the path from Hub A to Hub B,
- **Then** the system:
  - Calculates the relative distance, orientation (pitch, roll, heading), and vertical distance between Hub A and Hub B.
  - Provides a preliminary estimate of these measurements in real-time (e.g., after the first loop), with an accuracy of ~0.3–0.7m (before final corrections).
  - Updates the estimate as more loops are completed, achieving ~0.1–0.2m accuracy after 3–5 loops.
  - Displays the preliminary results in the UI, allowing me to assess if the jumper will fit or if adjustments are needed.

### Scenario 5: Preliminary Results During Survey
- **Given** the survey is in progress and the ROV has visited Hub A, Hub B, and Hub C,
- **When** I check the system’s progress,
- **Then** the system:
  - Provides a general idea of the results, including preliminary positions and orientations of the visited hubs.
  - Displays a provisional map of the subsea assets with estimated positions (e.g., ~0.3–0.7m accuracy before loop closure).
  - Updates the provisional map as more data is collected, improving accuracy with each loop.

### Scenario 6: Real-Time Monitoring
- **Given** the survey is in progress,
- **When** I monitor the system via the user interface,
- **Then** I can:
  - See real-time position, orientation, and depth data.
  - View preliminary results (e.g., provisional map, estimated distances) to assess progress.
  - Confirm the system’s status (e.g., “ZUPT in Progress”, “Survey Active”).
  - Receive alerts if issues arise (e.g., sensor failure).

### Scenario 7: Post-Mission Analysis
- **Given** the survey is complete,
- **When** I retrieve the logged data,
- **Then** the system:
  - Performs loop closure to refine position estimates to ~0.1–0.2m accuracy.
  - Generates a final 3D map of asset positions, orientations, and vertical distances.
  - Exports the map in CSV/JSON formats for engineering analysis.

---

## Acceptance Criteria

### General Requirements
- **Given** I am using the system at 5000m depth,
- **When** I deploy the system,
- **Then**:
  - The software operates reliably without external GPS, using dead-reckoning and error correction.
  - All components (software, hardware) are rated for 500 bar pressure and 2–4°C temperatures.

### Data Acquisition
- **Given** the ROV has deployed the device,
- **When** the system starts,
- **Then**:
  - The software collects IMU data (orientation, acceleration, angular rates) at ≥100 Hz.
  - The software collects pressure sensor data (depth) at ≥1 Hz.
  - All data is timestamped with nanosecond precision and synchronized across sensors.

### Alignment
- **Given** the device is at Hub A,
- **When** I initiate alignment,
- **Then**:
  - Coarse alignment completes within 10–30 seconds, achieving pitch/roll accuracy of ~0.03°.
  - Fine alignment (gyro-compassing) completes within 1–5 minutes, achieving heading accuracy of ~0.05°.
  - The UI displays alignment progress and results, with alerts for failures (e.g., excessive noise).

### Dead-Reckoning
- **Given** the system is aligned,
- **When** the ROV moves between waypoints,
- **Then**:
  - The software tracks position and velocity using dead-reckoning, updating at ≥10 Hz.
  - Initial drift is ~0.3–0.7m per 100m loop, before error correction.

### Error Correction
- **Given** the ROV pauses at a waypoint,
- **When** a ZUPT is triggered,
- **Then**:
  - ZUPT corrects velocity errors within 10–30 seconds.
  - Position Updates apply at known points (e.g., Hub A), resetting position errors.
  - Final position accuracy is ~0.1–0.2m after corrections.

### Kalman Filter
- **Given** the system is collecting data,
- **When** the Kalman Filter processes data,
- **Then**:
  - The filter fuses IMU data, ZUPT, and Position Updates to optimize state estimates.
  - Updates occur at ≥10 Hz, with position accuracy of ~0.1–0.2m after 3–5 loops.

### Multi-Point Survey Management
- **Given** I have defined a survey path,
- **When** the ROV executes the survey,
- **Then**:
  - The system navigates to each waypoint (3–5 points), performing measurements at each.
  - Measurements are tagged with waypoint IDs and timestamps.
  - The survey completes 3–5 loops for accuracy, with loop closure applied.

### Preliminary Results
- **Given** the survey is in progress,
- **When** the ROV has visited at least two waypoints,
- **Then**:
  - The system provides a general idea of the results, including preliminary positions, orientations, and distances between visited assets.
  - Preliminary accuracy is ~0.3–0.7m (before loop closure).
  - The UI displays a provisional map of the subsea assets, updating with each new waypoint visited.
  - Estimated distances and orientations are shown with a confidence indicator (e.g., “Preliminary: ±0.5m”).

### Jumper Metrology
- **Given** I am measuring between Hub A and Hub B for jumper installation,
- **When** the ROV completes the first loop (Hub A → Hub B → Hub A),
- **Then**:
  - The system provides a preliminary estimate of the distance, orientation, and vertical distance between Hub A and Hub B (e.g., ~0.3–0.7m accuracy).
  - After 3–5 loops, the final accuracy is ~0.1–0.2m for position and ~0.5–0.6m for vertical distance.
  - The UI displays these results in real-time, allowing me to assess jumper fitment early.

### Data Logging
- **Given** the survey is in progress,
- **When** data is collected,
- **Then**:
  - All data (IMU, pressure, position estimates) is logged in a binary format with timestamps.
  - Storage supports extended missions (e.g., 128GB capacity).
  - Data is recoverable after power loss.

### Real-Time Monitoring and Control
- **Given** I am monitoring the system,
- **When** the survey is active,
- **Then**:
  - The UI displays real-time position, orientation, and depth data with <100 ms latency.
  - I can view preliminary results (e.g., provisional map, estimated distances) to assess progress.
  - I can initiate/pause the survey, trigger manual ZUPT, or adjust settings.
  - Alerts notify me of issues (e.g., sensor failure, alignment issues).

### User Interface
- **Given** I am interacting with the system,
- **When** I use the UI,
- **Then**:
  - I can input the survey path and configure parameters (e.g., alignment duration).
  - I see a graphical display of the survey map (provisional and final), system status, and data plots.
  - The interface is intuitive, with tooltips and error messages.

### Post-Processing
- **Given** the survey is complete,
- **When** I process the data,
- **Then**:
  - The software applies loop closure to achieve ~0.1–0.2m position accuracy.
  - A final 3D map of asset positions, orientations, and vertical distances is generated.
  - Data is exported in CSV/JSON formats for engineering analysis.

---

## Non-Functional Requirements

- **Performance**:
  - Real-time processing at ≥10 Hz.
  - UI updates with <100 ms latency.
- **Reliability**:
  - Operates without failure for missions up to 24 hours.
  - Recovers from sensor failures by using last known state.
- **Security**:
  - Encrypts logged data with AES-256.
  - Requires user authentication for access.
- **Usability**:
  - UI is intuitive for operators with minimal training.
  - Provides clear error messages and recovery options.

---

## Assumptions

- The ROV provides stable power (24V DC, ~10W) and data connectivity via its umbilical.
- Known coordinates (e.g., Hub A) are provided in a local subsea reference frame.
- The ROV pilot ensures the device is stationary during ZUPT and alignment phases.

---

## Risks and Mitigations

- **Risk**: Inaccurate preliminary results mislead the operator.
  - **Mitigation**: Display a confidence indicator (e.g., “Preliminary: ±0.5m”) and update results as more data is collected.
- **Risk**: Sensor failure during operation.
  - **Mitigation**: Implement fault tolerance (e.g., fallback to last known state) and alert operators.
- **Risk**: Data loss during long missions.
  - **Mitigation**: Use redundant storage and checksums to ensure data integrity.


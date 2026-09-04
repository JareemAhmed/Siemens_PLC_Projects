# Weight-Based Box Sorting System

## Network 1 — Factory I/O Communication

Network 1 provides the interface between the S7-1200 PLC program and the Factory I/O simulation through the FC9000 function block.

The function block handles the exchange of input and output signals between the PLC and the virtual sorting system.

### Purpose

- Interface the PLC with Factory I/O
- Synchronize virtual sensors and actuators
- Enable real-time simulation of the sorting process
<img width="790" height="196" alt="NETWORK 1" src="https://github.com/user-attachments/assets/403dc11c-4310-4d58-beef-29da724233b8" />

---

## Network 2 — Conveyor Start & Scale Control

Network 2 controls the startup of the conveyor system.

When the START button is activated, the main conveyor and the three sorting conveyors are enabled.

The network also uses TOF timers and an N_TRIG edge-detection instruction to temporarily stop the scale conveyor while the box weight is being measured.

After the measurement period, the conveyor resumes operation.

### Purpose

- Start the conveyor system
- Control the weighing section
- Temporarily stop the box for accurate measurement
- Restart the conveyor after weighing
- Use timer and edge-triggered control
<img width="621" height="630" alt="NETWORK 2" src="https://github.com/user-attachments/assets/e13cddf7-168f-4798-be78-b9589f70dbd0" />

---

## Network 3 — Weight Signal Scaling

Network 3 processes the analog signal received from the weight sensor.

The raw analog value from ID30 is multiplied by 2.0 to convert the sensor reading into the corresponding weight value in kilograms.

The resulting value is stored in a REAL/float variable.

### Purpose

- Process the analog weight-sensor signal
- Convert the sensor reading into kilograms
- Store the measured weight as a REAL value
- Prepare the measurement for the classification logic

This is particularly useful from an engineering perspective because you're demonstrating analog signal processing, rather than only digital ON/OFF PLC logic.

<img width="856" height="301" alt="NETWORK 3" src="https://github.com/user-attachments/assets/5910b025-ec17-4c2a-ab83-3d1f77f2e97e" />

---

## Network 4 — Weight Classification

Network 4 compares the measured weight against predefined ranges.

The system identifies three categories:

| Category | Weight | Code |
|----------|--------|------|
| Small | 8.0–8.2 kg | 1 |
| Medium | 10.0–10.2 kg | 2 |
| Large | >15 kg | 3 |

The corresponding category code is stored in a temporary data register.

### Purpose

- Compare measured weight against predefined ranges
- Classify boxes automatically
- Store the classification result
- Provide the sorting logic with the required category
<img width="928" height="615" alt="NETWORK 4" src="https://github.com/user-attachments/assets/88329c72-17cf-4c69-bb0b-0cc63f7a00d5" />

---

## Network 5 — 8 kg Box Sorting

When the category register contains Category 1, the system identifies the box as an 8 kg box.

The PLC activates the required sorting outputs to direct the box toward the left exit lane.

The sorting sequence uses the forward conveyor together with the left-sort mechanism.

### Purpose

- Detect Category 1
- Activate the forward conveyor
- Activate the left sorting mechanism
- Route 8 kg boxes to the left exit
<img width="826" height="337" alt="NETWORK 5" src="https://github.com/user-attachments/assets/93315630-8515-419f-89b7-2b9c02bfa0f2" />

---

## Network 6 — Reset After Left Sorting

Network 6 clears the sorting state after the 8 kg box has successfully reached the left exit.

The left exit sensor provides confirmation that the box has passed through the sorting area.

The corresponding sorting outputs and category information are then reset.

### Purpose

- Detect completion of left-side sorting
- Reset left sorting outputs
- Clear the processed category
- Prepare the system for the next box
<img width="834" height="523" alt="NETWORK 6" src="https://github.com/user-attachments/assets/9f118deb-0c48-4cff-84d4-3563f967adf3" />

---

## Network 7 — 10 kg Box Sorting

When the category register contains Category 2, the box is identified as a 10 kg box.

The PLC keeps the forward conveyor active, allowing the box to continue straight through the system toward the middle exit.

### Purpose

- Detect Category 2
- Keep the forward path active
- Route 10 kg boxes to the middle/forward exit
<img width="808" height="279" alt="NETWORK 7" src="https://github.com/user-attachments/assets/39b0b299-450c-4671-b661-bf0e7fe569c7" />

---

## Network 8 — Reset After Middle Sorting

Network 8 monitors the middle exit sensor.

Once the 10 kg box reaches the middle exit, the corresponding sorting outputs are reset and the system becomes ready to process the next box.

### Purpose

- Confirm that the box reached the middle exit
- Reset the forward sorting state
- Clear the completed sorting operation
<img width="843" height="409" alt="NETWORK 8" src="https://github.com/user-attachments/assets/99f0d8a5-316d-4537-ad1b-6b1e471060f8" />

---

## Network 9 — 15 kg Box Sorting

When the category register contains Category 3, the box is identified as a 15 kg box.

The PLC activates the forward conveyor and the right sorting mechanism, directing the box toward the right exit lane.

### Purpose

- Detect Category 3
- Activate the forward conveyor
- Activate the right sorting mechanism
- Route 15 kg boxes to the right exit
<img width="826" height="330" alt="NETWORK 9" src="https://github.com/user-attachments/assets/a67869fb-1ac4-4e5d-b1bd-889acf3e1efc" />

---

## Network 10 — Reset After Right Sorting

Network 10 monitors the right exit sensor.

When the 15 kg box reaches the right exit, the PLC resets the corresponding sorting outputs and clears the completed sorting state.

### Purpose

- Confirm successful right-side sorting
- Reset right sorting outputs
- Clear the completed category
- Prepare for the next box
<img width="814" height="535" alt="NETWORK 10" src="https://github.com/user-attachments/assets/848bd784-7c25-48aa-8607-01dca144421a" />

---

## Network 11 — Master Reset

Network 11 provides a master reset function.

When the Reset button is activated, the PLC clears the relevant outputs, memory bits, and category data using the RESET_BF (Reset Bit Field) instruction.

This provides a convenient way to return the sorting system to its initial state.

### Purpose

- Reset the entire sorting sequence
- Clear memory bits
- Clear sorting outputs
- Reset the category register
- Return the system to its initial condition
<img width="841" height="421" alt="NETWORK 11" src="https://github.com/user-attachments/assets/f9e86be0-e957-4042-9472-b192e7fab6ba" />

---

## Network 12 — Box Counters

Network 12 implements three independent CTU (Count Up) counters.

Each counter tracks the number of boxes sorted into its respective category:

- 8 kg boxes
- 10 kg boxes
- 15 kg boxes

Each counter has a preset maximum value of 100.

### Purpose

- Count sorted boxes
- Maintain separate production counts
- Track each weight category
- Provide production data for the HMI

This is a nice feature to highlight in your CV because it moves the project beyond basic machine control into production monitoring.
<img width="489" height="504" alt="NETWORK 12" src="https://github.com/user-attachments/assets/333c7de6-95de-4f75-a80a-02e21de18f62" />

---

## Network 13 — HMI Indicators

Network 13 provides the PLC-side logic for the HMI status indicators.

When the Start button is activated, the START INDICATOR bit is set and the STOP INDICATOR is reset.

When the Reset/Stop button is activated, the opposite state is produced.

This allows the HMI to visually indicate the current operating state of the system.
<img width="957" height="561" alt="NETWORK 13" src="https://github.com/user-attachments/assets/6f6cb4f2-5fae-4911-89ae-d9e8a5765cae" />

---

# HMI Interface

Your HMI screen provides a simple operator interface for monitoring and controlling the sorting system.

It includes:

### System Control

- START button
- STOP button
- Start status indicator
- Stop status indicator

### Production Counters

The HMI displays separate counts for:

- 8 kg boxes
- 10 kg boxes
- 15 kg boxes

The displayed counters are linked to the corresponding PLC CTU counters.

This gives the operator a simple production-monitoring interface without needing to inspect the PLC program itself.
<img width="734" height="596" alt="HMI SCREEN" src="https://github.com/user-attachments/assets/7a98df5b-58f9-46a8-91d0-94f07f911e2e" />

---

# Factory I/O

The project also includes the Factory I/O driver configuration used to connect the virtual sorting system with the Siemens S7-1200 PLC.
<img width="1600" height="906" alt="FACTORYIO DRIVERS" src="https://github.com/user-attachments/assets/a19110d2-54a4-4d2a-8821-df69635d2d7a" />

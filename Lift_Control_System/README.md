# Lift Control System — 3-Floor Elevator

## Project Overview

This project is a PLC-based control system for a **3-floor elevator**, developed using **Siemens TIA Portal V19** and an **S7-1200 PLC**. The elevator operation is simulated using **Factory I/O**.

The system is designed to simulate realistic elevator behavior, including floor selection, floor request memory, target-floor management, automatic direction control, timing delays, and safety interlocking.

The PLC program consists of **10 ladder logic networks**, with each network responsible for a specific part of the elevator control sequence.

---

## System Features

- 3-floor elevator control
- Cabin floor selection
- Floor call buttons
- Multiple floor request handling
- Current floor detection
- Target floor selection
- Automatic UP/DOWN direction control
- UP/DOWN interlocking
- Start/Stop control
- Timer-based floor sequencing
- Floor arrival detection
- Request memory and reset logic
- Factory I/O simulation
- S7-1200 PLC programming

---

## Technologies & Software

- **Siemens TIA Portal V19**
- **Siemens S7-1200 PLC**
- **Ladder Logic (LAD)**
- **S7-PLCSIM V19**
- **Factory I/O**

---

# PLC Program — Network Explanation

## Network 1 — Factory I/O Communication

Network 1 provides the communication interface between the Siemens PLC program and the Factory I/O elevator simulation using the `FC9000` function block.

The function block synchronizes the PLC input and output signals with the virtual elevator hardware, allowing the PLC program to receive sensor and button inputs and control the simulated elevator.

### Purpose

- Connect the PLC program with Factory I/O
- Synchronize virtual inputs and outputs
- Enable real-time elevator simulation
<img width="742" height="199" alt="NETWORK 1" src="https://github.com/user-attachments/assets/9991e4fd-15c4-4609-a320-a7fc0caf5c9a" />

---

## Network 2 — System START Logic

Network 2 implements the system START function.

When the START push button is activated, the `SYSTEM_ON` bit is set. This bit acts as the main enable signal for the elevator control system.

Once the system is enabled, the elevator control logic is allowed to operate.

### Purpose

- Start the elevator control system
- Set the `SYSTEM_ON` status bit
- Enable the elevator control logic
<img width="745" height="192" alt="NETWORK 2" src="https://github.com/user-attachments/assets/c47c9f37-867e-4cd1-9b53-36840f8e85f7" />


---

## Network 3 — System STOP / Safety Shutdown

Network 3 implements the system STOP function.

When the STOP push button is activated, the system performs a complete shutdown by resetting the main system and elevator movement signals.

The following signals are reset:

- `SYSTEM_ON`
- `ELEVATOR_UP`
- `ELEVATOR_DOWN`
- `ELEVATOR_SLOW`

This ensures that elevator movement is stopped when the system is shut down.

### Purpose

- Stop the elevator
- Disable the control system
- Reset movement outputs
- Provide a system shutdown function
<img width="717" height="421" alt="NETWORK 3" src="https://github.com/user-attachments/assets/00e434d1-52d3-424d-97a1-b1dc830fc343" />


---

## Network 4 — Floor Request Memory

Network 4 handles floor requests from the elevator cabin and floor call buttons.

Instead of directly controlling the elevator when a button is pressed, the requested floor is stored in memory. This allows the system to retain multiple requests while the elevator is already moving.

For example, if a passenger requests Floor 3 while the elevator is travelling to another floor, the request can remain stored and be processed afterward.

### Purpose

- Capture floor requests
- Store requests in memory
- Support multiple floor requests
- Prevent requests from being lost while the elevator is moving
<img width="453" height="520" alt="NETWORK 4" src="https://github.com/user-attachments/assets/0401727c-6356-4ac7-b3e3-6dc192edc901" />


---

## Network 5 — Target Floor Selection

Network 5 determines when a stored floor request becomes the active target floor.

The requested floor is transferred to the `TARGET FLOOR` memory word (`MW0`) only when the elevator is not currently moving and the required timing condition has been completed.

This prevents the target floor from being changed while the elevator is still travelling.

### Purpose

- Select the next floor to be serviced
- Transfer the selected request to `TARGET FLOOR`
- Prevent target-floor changes during movement
- Coordinate multiple floor requests
<img width="817" height="631" alt="NETWORK 5" src="https://github.com/user-attachments/assets/347f8f78-2b8e-4cac-9627-b48be3989037" />


---

## Network 6 — Floor Request Reset

Network 6 resets the corresponding floor-request memory after the elevator reaches the requested floor.

The floor sensor confirms that the elevator has arrived at the requested floor. Once the arrival condition is satisfied, the associated request memory is cleared.

This prevents the same request from being processed repeatedly.

### Purpose

- Detect completion of a floor request
- Reset the completed request
- Prevent repeated processing
- Prepare the system for the next request
<img width="613" height="567" alt="NETWORK 6" src="https://github.com/user-attachments/assets/ba4c67d9-1a83-478c-aeac-ccd240c1ff06" />


---

## Network 7 — Floor Arrival Timing

Network 7 manages the timing delay associated with reaching a floor.

The floor sensor condition activates the required timer, ensuring that the elevator remains at the detected floor for the required period before the next request is processed.

This creates controlled sequencing between consecutive elevator stops.

### Purpose

- Confirm floor arrival
- Provide a controlled delay between stops
- Prevent immediate transition to the next request
- Improve multi-floor request sequencing
<img width="682" height="511" alt="NETWORK 7" src="https://github.com/user-attachments/assets/4bf2c285-58cd-4f74-87cb-de626a1fa1d6" />


---

## Network 8 — Current Floor Detection

Network 8 determines the elevator's current floor using the floor sensors.

Depending on which floor sensor is active, the corresponding floor number is stored in the `CURRENT FLOOR` memory word (`MW2`).

This provides the PLC with a numerical representation of the elevator's current position.

### Purpose

- Detect the current floor
- Process floor sensor signals
- Store the current floor in `MW2`
- Provide position information for direction control
<img width="688" height="664" alt="NETWORK 8" src="https://github.com/user-attachments/assets/fd76638a-5f60-4c2f-bdff-0a349ffadb28" />


---

## Network 9 — Elevator UP Control

Network 9 contains the control logic for upward elevator movement.

The PLC compares the target floor with the current floor:

`TARGET FLOOR > CURRENT FLOOR`

If the target floor is higher than the current floor, the `ELEVATOR_UP` command is activated.

Interlocking conditions prevent the UP command from being activated if the elevator is already travelling downward or if the system is switched off.

### Purpose

- Determine when upward movement is required
- Compare target and current floor
- Activate the elevator UP command
- Prevent conflicting UP/DOWN commands
- Ensure the system is enabled before movement
<img width="695" height="268" alt="NETWORK 9" src="https://github.com/user-attachments/assets/2c3fda6c-5440-475a-a11c-0a7b30b37367" />
---

## Network 10 — Elevator DOWN Control

Network 10 contains the control logic for downward elevator movement.

The PLC compares:

`TARGET FLOOR < CURRENT FLOOR`

If the target floor is lower than the current floor, the `ELEVATOR_DOWN` command is activated.

Like the UP control, the DOWN logic includes interlocking conditions to prevent conflicting movement commands.

### Purpose

- Determine when downward movement is required
- Compare target and current floor
- Activate the elevator DOWN command
- Prevent simultaneous UP/DOWN operation
- Ensure safe directional control
<img width="695" height="254" alt="NETWORK 10" src="https://github.com/user-attachments/assets/d3198ec4-5f63-4bc9-91d1-8247f1462125" />


---

# Elevator Control Sequence

The overall operation of the elevator can be summarized as follows:

```text
START SYSTEM
     ↓
SYSTEM_ON
     ↓
Floor Request
     ↓
Request Stored in Memory
     ↓
Target Floor Selected
     ↓
Compare Target Floor
with Current Floor
     ↓
 ┌───────────────┐
 │               │
Target > Current   Target < Current
 │               │
 ↓               ↓
ELEVATOR UP      ELEVATOR DOWN
 │               │
 └───────┬───────┘
         ↓
   Floor Sensor
         ↓
   Floor Reached
         ↓
 Request Cleared
         ↓
 Timing Delay
         ↓
 Next Floor Request

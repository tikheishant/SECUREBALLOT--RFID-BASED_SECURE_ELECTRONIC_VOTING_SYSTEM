# 🗳️ SecureBallot --- RFID-Based Electronic Voting System

**An embedded voting-system prototype built with ARM7 LPC2148 and
Embedded C.**

RFID authentication • Password protection • EEPROM data storage •
RTC-based election timing

------------------------------------------------------------------------

## 📌 Project Overview

**SecureBallot** is an RFID-based electronic voting system prototype
developed using the **ARM7 LPC2148 microcontroller** and **Embedded C**.
It provides separate interfaces for election officers and voters,
helping manage voter authentication, vote recording, and result display
in one embedded system.

The officer can manage voter records, configure election timings, view
results, reset election data, and update the officer password. A
registered voter authenticates using an RFID card and password, then
selects a party/candidate through the keypad. Voter status and
party-wise vote counts are stored in external I²C EEPROM.

> **Note:** SecureBallot is an academic/embedded-systems prototype, not
> a certified or production-ready election system. Real elections
> require independent security assessment, tamper resistance,
> auditability, accessibility, and compliance with applicable
> regulations.

## ✨ Features

### Officer Interface

-   Officer RFID-card verification
-   Password-based officer authentication
-   Add, remove, and reactivate voter records
-   Configure election start and stop times
-   View party-wise vote counts and results
-   Detect ties between parties with the highest vote count
-   Reset vote counts and voter voting-status flags
-   Change the officer password

### Voter Interface

-   RFID-based voter identification
-   Check whether a voter record is active
-   Check whether the voter has already voted
-   Password authentication
-   Select from up to **8 parties/candidates**
-   Confirm a vote before recording it
-   Change the voter password
-   Prevent a second vote by updating the voter's voting-status flag

### Embedded Features

-   UART-based RFID data reception
-   I²C EEPROM read/write operations
-   LCD-based menus and status messages
-   4×4 matrix keypad input
-   RTC-based time handling
-   Party-wise vote-count storage
-   UART0 event logging

## 🧰 Hardware Components

  Component                      Purpose
  ------------------------------ ------------------------------------
  LPC2148 ARM7 microcontroller   Main controller
  RFID reader and cards          Officer/voter identification
  LCD                            Menus, prompts, and results
  4×4 matrix keypad              Password and menu input
  AT24C256 I²C EEPROM            Persistent voter and election data
  RTC module                     Election time handling
  Supporting circuitry           Power and module interfacing

*Confirm the exact LCD size, RFID reader model, and RTC part number
against your final hardware before publishing.*

## 💻 Software and Tools

-   **Language:** Embedded C
-   **Microcontroller:** NXP LPC2148 (ARM7TDMI-S)
-   **IDE/Compiler:** Keil µVision
-   **Simulation:** Proteus, if used
-   **Interfaces:** UART, I²C, GPIO
-   **Peripherals:** LCD, keypad, RFID reader, RTC, external EEPROM

## 🧭 System Workflow

### Officer workflow

``` text
Officer RFID Card
       |
       v
Receive RFID Data (UART1)
       |
       v
Verify Officer RFID
       |
       v
Enter Officer Password
       |
       v
Officer Menu
  |       |        |         |         |
  v       v        v         v         v
Manage  Set Time  View     Reset    Change
Voters            Result   Election Password
```

### Voter workflow

``` text
Voter RFID Card
       |
       v
Receive and Identify Card
       |
       v
Find Voter Record in EEPROM
       |
       v
Check Active Status
       |
       v
Check Voting Status
       |
       v
Enter Voter Password
       |
       v
Voter Menu
       |
       v
Select Party/Candidate
       |
       v
Confirm Vote
       |
       v
Increment Party Count in EEPROM
       |
       v
Mark Voter as Already Voted
```

## 🗃️ Data Storage

The external EEPROM stores officer credentials, voter records, voter
status flags, election timing data, and party-wise vote counts.

The voter record is designed around a **20-byte record layout**:

  Offset          Size Stored information
  --------- ---------- ---------------------
  `+0x00`     10 bytes Voter ID field
  `+0x0A`      4 bytes Voter password
  `+0x0E`       1 byte Voting-status flag
  `+0x0F`       1 byte Active/removed flag
  `+0x10`      4 bytes Reserved

The exact byte representation depends on the implementation. Review
EEPROM address definitions and read/write routines together when
changing this layout.

## 🧩 Main Modules

Module names may differ depending on the final source-tree organization.

  Module / Function           Responsibility
  --------------------------- -----------------------------------------------
  `Officer_interface.c`       Officer menu and administrative operations
  `Voter_interface.c`         Voter authentication and voter menu
  `UART1.c`                   RFID data reception
  `UART.c`                    UART0 communication and logging
  `I2C.c`                     I²C communication
  `i2c_eeprom.c`              EEPROM read/write operations
  `lcd.c`                     LCD commands and display routines
  `KPM.c`                     Keypad scanning
  `Password.c`                Password entry and password-related functions
  `data_location_defines.h`   EEPROM address and flag definitions

## 🚀 Getting Started

### 1. Clone the repository

``` bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

Replace the URL and directory with your actual GitHub repository
details.

### 2. Open the project

1.  Open the project in **Keil µVision**.
2.  Confirm the selected target is **LPC2148**.
3.  Verify the startup file, device settings, include paths, and
    source-file list.
4.  Review pin assignments for the LCD, keypad, RFID UART, I²C EEPROM,
    and RTC.
5.  Build the project and resolve toolchain-specific warnings.

### 3. Connect the hardware

Connect each module according to the pin assignments in your source code
and circuit diagram. Confirm voltage levels, power requirements, UART
settings, I²C addressing, and common ground before powering the circuit.

### 4. Program and test

Flash the firmware to the LPC2148 and test in stages: 1. LCD and keypad
operation 2. RFID reception and card matching 3. Officer authentication
4. Add, remove, and reactivate voter records 5. Voter authentication and
password change 6. Vote confirmation and one-vote-per-voter behavior 7.
EEPROM persistence after power cycling 8. Election timing, result
display, tie handling, and reset

**Important:** EEPROM initialization routines should not run
automatically on every startup. They may overwrite credentials, voter
records, or vote counts. Run initialization only when intentionally
preparing a fresh test setup.

## 🧪 Suggested Test Cases

-   Valid and invalid officer RFID cards
-   Correct and incorrect officer passwords
-   Add a voter and attempt to add the same ID again
-   Remove a voter and attempt authentication with that card
-   Reactivate a previously removed voter
-   Incorrect voter password
-   Cast a vote and attempt to vote again
-   Verify the selected party count increases by one
-   Change a voter or officer password
-   Test all party selections
-   Test a tie and an election with no votes cast
-   Reset an election and inspect the resulting EEPROM values
-   Check election start/stop behavior around configured times

## 🔐 Security and Reliability Considerations

This project demonstrates embedded authentication and state management,
but these mechanisms alone do **not** establish election-grade security.

Before real-world deployment, the design would need a thorough review
covering: - Secure storage and protection of credentials - Protection
against EEPROM modification and physical tampering - Reliable, atomic
vote updates during power loss - Independent verification and audit
records - Firmware integrity and controlled updates - Recovery behavior
for interrupted writes - Privacy, accessibility, and applicable legal
requirements

Do not use real voter credentials or conduct a real election with this
prototype.

## 📷 Project Images

Add your own circuit, Proteus simulation, hardware setup, or LCD
screenshots. Example Markdown:

``` markdown
![SecureBallot hardware setup](images/hardware-setup.jpg)
![SecureBallot circuit simulation](images/proteus-simulation.png)
```

Create an `images/` folder and replace these example paths with your
actual image filenames.

## 🔮 Future Improvements

-   Strengthen credential protection
-   Add integrity checks for stored records
-   Improve recovery from power loss during EEPROM writes
-   Add a detailed audit log
-   Add a dedicated election-ended state to prevent unintended restarts
-   Improve result reporting and export
-   Document the complete circuit diagram and pin mapping
-   Add automated tests for EEPROM and voting logic

## 🎓 Learning Outcomes

-   ARM7 LPC2148 peripheral interfacing
-   Embedded C and bare-metal programming
-   UART interrupt handling
-   I²C communication and EEPROM storage
-   GPIO, LCD, and matrix keypad interfacing
-   RTC-based time management
-   State flags and record-based data management
-   Modular firmware development and debugging

## 👨‍💻 Author

**Ishant Tikhe**

Embedded Systems \| ARM7 \| Embedded C \| Microcontroller Interfacing

-   GitHub: `https://github.com/<your-username>`

## 📄 License

Add a `LICENSE` file if you intend to permit reuse of this project.
Without a license, others do not automatically receive permission to
reuse, modify, or distribute the code.

------------------------------------------------------------------------

**SecureBallot --- Learn, Build, Test, and Improve Embedded Systems.**


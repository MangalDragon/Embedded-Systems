# Module-1 Embedded Systems — FINAL COMPLETE Q&A
### ECE23601 | Exam Ready | All Topics Covered

---

> **✅ All numerical answers verified. All definitions cross-checked against source notes.**

---

## Q1. Comparison of Embedded System and General Computing System

| Parameter | General Purpose Computing System | Embedded System |
|---|---|---|
| Definition | Generic hardware + GPOS for running variety of applications | Special-purpose hardware + embedded OS for a specific application |
| OS | Always has a General Purpose OS (Windows, Linux, etc.) | May or may not have an OS |
| Firmware/Application | User can install, remove, or modify applications freely | Firmware is pre-programmed and **non-alterable** by the end user |
| Performance focus | "Faster is better" — performance is the key deciding factor | Application-specific requirements (power, memory, response) are the key factors |
| Power management | Not tailored for power saving | Highly tailored to use hardware/OS power saving modes |
| Response time | Not time-critical | Can be time-critical (especially mission-critical systems) |
| Determinism | Not necessarily deterministic | Deterministic for **Hard Real Time** systems |

**Key reasoning:**
1. A general computing system is generic; an embedded system is domain-specific.
2. The firmware of an embedded system is locked — the end user cannot change it.
3. Power, response time, and determinism matter far more in embedded systems.

---

## Q2. Characteristics of an Embedded System

1. **Application and Domain Specific**
   Designed for a specific function only. A microwave oven controller cannot replace an air conditioner controller.

2. **Reactive and Real Time**
   Constantly interact with the real world via sensors. Events trigger a designed response — hence *reactive systems*. Real Time means timing behaviour is deterministic; no deadlines should be missed. (Not all need to be real time — e.g., electronic toys.)

3. **Operates in Harsh Environments**
   Environments may involve dust, high temperature, vibration, shock, power fluctuations, corrosion, and component ageing.

4. **Distributed**
   May be part of a larger system where multiple embedded units work together.
   - ATM: card reader unit + transaction unit + currency counter + printer unit.
   - SCADA: physically distributed control units connected to a supervisory module.

5. **Small Weight and Size**
   Product aesthetics and portability demand compact, lightweight designs. *"Small is beautiful."*

6. **Power Concerns**
   Critical — especially for battery-operated devices. Select low power components (low dropout regulators, processors with power-saving modes). Minimise heat dissipation.

**Key reasoning:** These 6 characteristics distinguish embedded systems from general computers — driven by application needs, real-world interaction, physical constraints, and deployment conditions.

---

## Q3. Memory Storage of 32-bit data `3D7A2390H` — Little Endian & Big Endian

**Starting address:** 0x30000
**Data bytes (MSB → LSB):** `3D`, `7A`, `23`, `90`

### Little-Endian (Least Significant Byte at lowest address)

| Address | Data Byte | Label |
|---------|-----------|-------|
| 0x30000 | 90 | Byte 0 (LSB) |
| 0x30001 | 23 | Byte 1 |
| 0x30002 | 7A | Byte 2 |
| 0x30003 | 3D | Byte 3 (MSB) |

### Big-Endian (Most Significant Byte at lowest address)

| Address | Data Byte | Label |
|---------|-----------|-------|
| 0x30000 | 3D | Byte 3 (MSB) |
| 0x30001 | 7A | Byte 2 |
| 0x30002 | 23 | Byte 1 |
| 0x30003 | 90 | Byte 0 (LSB) |

```
            LITTLE-ENDIAN              BIG-ENDIAN
Address         Data                       Data
0x30000    ┌──────────┐               ┌──────────┐
           │    90    │  (Byte 0)     │    3D    │  (Byte 3)
0x30001    ├──────────┤               ├──────────┤
           │    23    │  (Byte 1)     │    7A    │  (Byte 2)
0x30002    ├──────────┤               ├──────────┤
           │    7A    │  (Byte 2)     │    23    │  (Byte 1)
0x30003    ├──────────┤               ├──────────┤
           │    3D    │  (Byte 3)     │    90    │  (Byte 0)
           └──────────┘               └──────────┘
```

**Key reasoning:**
1. Split 32-bit value into 4 bytes: `3D`, `7A`, `23`, `90` (MSB to LSB).
2. Little-Endian → LSB (`90`) at lowest address. Big-Endian → MSB (`3D`) at lowest address.
3. Intel x86 = Little-Endian; Motorola 68000 = Big-Endian.

---

## Q4. SPI (Serial Peripheral Interface) Communication Protocol

- **Type:** Synchronous, bi-directional, **full duplex**, **four-wire** serial bus.
- **Introduced by:** Motorola.
- **Architecture:** Single master, multi-slave (only one master active at a time).

### Four Signal Lines

| Signal | Full Form | Direction | Function |
|--------|-----------|-----------|----------|
| MOSI | Master Out Slave In | Master → Slave | Data from master to slave |
| MISO | Master In Slave Out | Slave → Master | Data from slave to master |
| SCLK | Serial Clock | Master → Slave | Clock generated by master |
| SS | Slave Select | Master → Slave | **Active LOW** — selects which slave is active |

### SPI Bus Diagram

```
        ┌──────────────┐
        │    Master    │
        │  MOSI ───────┼──────────────────────────► Slave 1   Slave 2
        │  MISO ◄──────┼──────────────────────────── Slave 1   Slave 2
        │  SCLK ───────┼──────────────────────────► Slave 1   Slave 2
        │  SS1  ───────┼──────────────────────────► Slave 1
        │  SS2  ───────┼────────────────────────────────────► Slave 2
        └──────────────┘
```

### Operation
- Master generates SCLK and selects slave by asserting its SS line **LOW**.
- Unselected slaves keep their MISO lines at **high impedance**.
- Data flows simultaneously on MOSI and MISO (full duplex).

### SPI Device Registers
- **Control Register:** Holds master/slave selection, baud rate, clock control settings.
- **Status Register:** Holds transmission and reception status conditions.

**Key reasoning:**
1. SS (active LOW) selects one slave at a time — each slave has its own SS line.
2. Full duplex = data flows both ways simultaneously.
3. Clock always from master; slaves cannot initiate communication.

---

## Q5. Super Loop Based Embedded Firmware Approach

- Adopted for applications that are **not time-critical** and where response time is not critical.
- Executes tasks **serially** in a fixed, infinite loop — hence called "Super Loop."

### Equivalent C Code

```c
void main()
{
    Configurations();
    Initializations();
    while(1)
    {
        Task_1();
        Task_2();
        :
        Task_n();
    }
}
```

- The only exit is a **hardware reset** (returns to main loop) or an **interrupt** (suspends current task → ISR executes → resumes).

### Advantages
- Does not require an operating system.
- No scheduling or priority assignment needed.
- Execution order is fixed and predictable.

### Applications
- Low-cost products where response time is not critical.
- Inherently sequential tasks (e.g., card reader: check card → authenticate → read/write).
- Example: electronic video game toy (reads keypad, updates display).

### Drawbacks
- Failure in any single task affects the entire system. WDTs can help but add cost.
- **Lack of real timeliness:** As tasks increase, loop iteration time increases — probability of missing events rises.

---

## Q6. Availability Calculation (MTBF = 4 months, MTTR = 2 weeks)

**Formula:**
$$A_i = \frac{MTBF}{MTBF + MTTR}$$

**Given:** MTBF = 4 months = **120 days** | MTTR = 2 weeks = **14 days**

$$A_i = \frac{120}{120 + 14} = \frac{120}{134}$$

$$\boxed{A_i = 0.8955 \approx 89.55\%}$$

**Key reasoning:**
1. Convert all units to the same unit (days): 4 months = 4×30 = 120 days; 2 weeks = 14 days.
2. Higher MTBF (less frequent failures) + lower MTTR (faster repair) = higher availability.

---

## Q7. I²C (Inter-Integrated Circuit) Communication Protocol

- **Type:** Synchronous, bi-directional, **half duplex**, **two-wire** serial bus.
- **Developed by:** Philips Semiconductors (early 1980s, originally for TV peripheral chips).

### Two Bus Lines

| Line | Full Form | Function |
|------|-----------|----------|
| SCL | Serial Clock Line | Synchronisation clock pulses (always generated by master) |
| SDA | Serial Data Line | Transmits serial data |

### I²C Bus Diagram

```
VCC
 │     Pull-up          Pull-up
 ├────┤ ├──────────────┤ ├────── VCC
 │    │                 │
SCL ──┼─────────────────┼──── SCL
SDA ──┼─────────────────┼──── SDA
    Master            Slave 1   Slave 2 ...
```
*Pull-up resistors are required on both SCL and SDA lines.*

### Key Features
- Shared bus — many devices connect to the same two wires.
- Slaves identified by **7-bit or 10-bit address**.
- Supports **multi-master** on same bus.

### I²C Communication Sequence
1. Master pulls SDA **LOW** while SCL is HIGH → **Start condition**.
2. Master sends 7/10-bit slave address MSB first; clock pulses on SCL.
3. Master sends **Read (1)** or **Write (0)** bit.
4. Master waits for **Acknowledge** from addressed slave.
5. Addressed slave drives SDA LOW → Acknowledgement.
6. **Write:** Master sends 8-bit data; slave acknowledges.  
   **Read:** Slave sends data; master acknowledges.
7. Master pulls SDA **HIGH** while SCL is HIGH → **Stop condition**.

**Key reasoning:**
1. Only 2 wires needed regardless of how many devices are on the bus.
2. Start/Stop conditions are distinguished by the state of SDA relative to SCL.
3. Every byte is followed by an acknowledgement bit.

---

## Q8. MTTR Calculation (Availability = 90%, MTBF = 30 days)

**Formula:**
$$A_i = \frac{MTBF}{MTBF + MTTR}$$

**Given:** $A_i$ = 90% = 0.9 | MTBF = 30 days

**Rearranging:**
$$MTTR = \frac{MTBF}{A_i} - MTBF = \frac{30}{0.9} - 30 = 33.33 - 30$$

$$\boxed{MTTR = 3.33 \text{ days} = 80 \text{ hours}}$$

*(3.33 days × 24 = 80 hours)*

---

## Q9. Operational Quality Attributes of Embedded System

Operational quality attributes are non-functional requirements related to the system **when it is in operational/online mode**. There are **6 attributes:**

### 1. Response
- Measure of the **quickness** of the system — how fast it tracks changes in input.
- Flight control systems require real-time response; delay creates safety hazards.
- Not all embedded systems need real time (e.g., electronic toy).

### 2. Throughput
- Measures the **efficiency** of the system — rate of production/operation over a time period.
- Example: how many transactions a card reader performs per minute/hour.
- Measured using **Benchmarks** (reference standards for comparison).

### 3. Reliability
- Measure of how much you can rely on the system functioning properly.
- Defined by:
  - **MTBF (Mean Time Between Failures):** Frequency of failures (in hours/weeks/months).
  - **MTTR (Mean Time To Repair):** How long the system is allowed to be out of order. For critical systems: order of minutes.

### 4. Maintainability
- Deals with support and maintenance for technical issues, failures, or routine check-ups.
- **Preventive (Scheduled):** Replacing printer cartridge after *n* printouts.
- **Corrective:** Repairing a printer when the paper feed fails.
- Indicates **Availability:**

$$A_i = \frac{MTBF}{MTBF + MTTR}$$

### 5. Security
Three major measures:
- **Confidentiality:** Protecting data from unauthorised **disclosure**.
- **Integrity:** Protecting data from unauthorised **modification**.
- **Availability:** Protecting data from **unauthorised access**.

Example: Shared-lab PDA uses username/password (Availability), admin vs user roles (Confidentiality), read-only for all users (Integrity).

### 6. Safety
- Deals with possible damage to **operators, public, and environment** due to hardware/firmware failure or hazardous emissions.
- Safety analysis is mandatory in product engineering.
- Some threats are sudden (product breakdown); others are gradual (hazardous emissions).

---

## Q10. Wi-Fi Communication Interface

- Follows **IEEE 802.11** standard.
- Supports **IP-based communication** — each device has a unique IP address.
- Requires a **Wi-Fi router / Wireless Access Point** to manage communication and assign IP addresses.
- Operates at **2.4 GHz or 5 GHz** (co-exists with Bluetooth and ISM-band devices).

### Security: WEP (Wired Equivalency Privacy), WPA (Wireless Protected Access)

### Data Rate and Range

| Attribute | Value |
|-----------|-------|
| Data Rate | 1 Mbps to 1.73 Gbps (varies: 802.11a/b/g/n/ac) |
| Range | 100 to 1000 feet |

### Wi-Fi Network Diagram

```
Device A ─── Wi-Fi Radio ──┐
Device B ─── Wi-Fi Radio ──┼──► Wi-Fi Router / Access Point ──► Internet
Device C ─── Wi-Fi Radio ──┘
```

### Connection Process
1. Device turns on Wi-Fi radio → searches available networks → lists SSIDs.
2. User selects SSID → enters password → connected → assigned IP address.

**Key reasoning:**
1. Router is the central point for all communication and internet access.
2. 2.4 GHz = longer range, slower speed; 5 GHz = shorter range, faster speed.

---

## Q11. Memory Storage of 32-bit data `56AB98FCH` — Little Endian & Big Endian

**Starting address:** 0x20000
**Data bytes (MSB → LSB):** `56`, `AB`, `98`, `FC`

### Little-Endian

| Address | Data Byte | Label |
|---------|-----------|-------|
| 0x20000 | FC | Byte 0 (LSB) |
| 0x20001 | 98 | Byte 1 |
| 0x20002 | AB | Byte 2 |
| 0x20003 | 56 | Byte 3 (MSB) |

### Big-Endian

| Address | Data Byte | Label |
|---------|-----------|-------|
| 0x20000 | 56 | Byte 3 (MSB) |
| 0x20001 | AB | Byte 2 |
| 0x20002 | 98 | Byte 1 |
| 0x20003 | FC | Byte 0 (LSB) |

```
            LITTLE-ENDIAN              BIG-ENDIAN
Address         Data                       Data
0x20000    ┌──────────┐               ┌──────────┐
           │    FC    │  (Byte 0)     │    56    │  (Byte 3)
0x20001    ├──────────┤               ├──────────┤
           │    98    │  (Byte 1)     │    AB    │  (Byte 2)
0x20002    ├──────────┤               ├──────────┤
           │    AB    │  (Byte 2)     │    98    │  (Byte 1)
0x20003    ├──────────┤               ├──────────┤
           │    56    │  (Byte 3)     │    FC    │  (Byte 0)
           └──────────┘               └──────────┘
```

---

## Q12. Core of an Embedded System (with Diagram)

The core is the **master brain** — a single chip controller/processor supported by memory, communication interfaces, I/O ports, and other subsystems.

### The Controller Can Be:
- **Microprocessor** | **Microcontroller** | **FPGA** | **DSP** | **ASIC/ASSP**

### Embedded System Block Diagram

```
                ┌────────────────────────────────────────┐
                │            Embedded System             │
                │                                        │
Sensors ───────►│      ┌─────────────────────┐           │──────► Actuators
(Input ports)   │      │     System Core      │           │       (Output ports)
                │      │  (Microprocessor /   │           │
                │      │   Microcontroller /  │           │
                │      │   FPGA / DSP / ASIC) │           │
                │      └──────────┬───────────┘           │
                │                 │                       │
                │    ┌────────────┼────────────┐          │
                │    │            │             │          │
                │  Memory  Communication    Other ICs      │
                │ (ROM/RAM)  Interface    & Subsystems     │
                │           (I²C, SPI,                    │
                │           UART, Wi-Fi)                  │
                │        Embedded Firmware                │
                └────────────────────────────────────────┘
```

### Components of the Core

| Component | Role |
|-----------|------|
| Processor / Controller | Executes firmware; controls all peripherals |
| Memory (ROM) | Stores firmware (OTP, PROM, EEPROM, FLASH) |
| Memory (RAM) | Working memory for temporary data (SRAM, DRAM, NVRAM) |
| Input Ports (Sensors) | Capture real-world events |
| Output Ports (Actuators) | Produce physical actions in response |
| Communication Interfaces | Onboard: I²C, SPI, UART; External: Bluetooth, Wi-Fi, USB |
| Embedded Firmware | Software that imparts intelligence to the system |

---

## Q13. Non-Operational Quality Attributes of Embedded System

Non-operational quality attributes are addressed on factors **other than operational aspects**. There are **5 attributes:**

### 1. Testability & Debug-ability
- **Testability:** How easily one can test the design — applies to both hardware and firmware.
- **Debug-ability:** Means of debugging to find unexpected behaviour.
  - **Hardware level:** Figuring out hardware problems.
  - **Firmware level:** Figuring out errors from firmware flaws.

### 2. Evolvability
- The ease with which the embedded product (firmware + hardware) can be modified to take advantage of new technologies.

### 3. Portability
- Measure of **system independence** — the product functions in various environments, target processors, and embedded OSes.
- **Porting:** Migration of firmware from one target processor (e.g., Intel x86) to another (e.g., Hitachi SH3).
- Firmware written in 'C' is easy to port; firmware in Assembly Language has **poor portability**.

### 4. Time-to-Prototype and Market
- **Time-to-market:** Time between product conceptualisation and availability for sale.
- Highly competitive market — a competitor may release first, or technology may be superseded.
- **Prototyping** reduces time-to-market by developing important features first using off-the-shelf components.

### 5. Per Unit Cost and Revenue
- Budget and total system cost must be balanced for marginal profit.
- **Product Life Cycle Phases:**
  1. **Design & Development** — Investment only, no returns.
  2. **Product Introduction** — Low sales, low competition.
  3. **Growth** — High market share.
  4. **Maturity** — Revenue reaches peak.
  5. **Retirement/Decline** — Drop in sales due to competition or technology changes.

---

## Q14. Memory Types in Embedded Systems

### Program Storage Memory (ROM) — Non-Volatile

```
               Code Memory (ROM)
              /     |      |      \     \
           MROM   PROM   EPROM  EEPROM  FLASH
           (OTP)
```

| Type | Key Characteristics |
|------|---------------------|
| **MROM (Masked ROM)** | Factory programmed; cannot be modified; low cost for high volume production |
| **PROM / OTP** | End-user programmable using a programmer that burns fuses; once written, cannot be reprogrammed |
| **EPROM** | Erasable by UV light through quartz window (20–30 min); must be removed from board |
| **EEPROM** | Electrically erasable at byte level in-circuit; limited to a few kilobytes |
| **FLASH** | Latest & most popular; erased at sector/page level electrically; high capacity; thousands of erase cycles |

> USB drives, thumb drives, and memory cards all use **FLASH** memory.

### Working Memory (RAM) — Volatile

```
         Read/Write Memory (RAM)
        /           |           \
      SRAM         DRAM         NVRAM
```

| Feature | SRAM | DRAM |
|---------|------|------|
| Storage mechanism | Voltage (flip-flops) | Charge (capacitor + MOSFET) |
| Cell structure | 6 CMOS transistors | 1 MOSFET + 1 capacitor |
| Refresh required? | **No** | **Yes** (every few ms) |
| Speed | Fast (~10 ns access) | Slow (~60 ns access) |
| Density | Low (less dense) | High (highly dense) |
| Cost | More expensive | Less expensive |

**NVRAM (Non-Volatile RAM):** SRAM with battery backup. Life span ~10 years. Example: DS1644 (32 KB).

---

## Q15. RISC vs CISC Architecture

| **RISC** | **CISC** |
|---|---|
| Fewer instructions (e.g., AVR — only 32 instructions) | Large instruction set (e.g., 8051 — 255 instructions) |
| Instruction pipelining supported | Generally no pipelining |
| Orthogonal instruction set | Non-orthogonal (instruction-specific) |
| Operations on registers only; Load/Store for memory | Operations on registers or memory |
| Large number of general-purpose registers | Limited general-purpose registers |
| More lines of code needed | Single instruction can replace multiple RISC instructions |
| Fixed-length instructions | Variable-length instructions |
| Less silicon; lower pin count | More silicon (additional decoder logic) |
| Harvard Architecture | Can be Harvard or Von-Neumann |

---

## Q16. Harvard vs Von-Neumann Architecture

| **Harvard Architecture** | **Von-Neumann Architecture** |
|---|---|
| **Separate** buses for instruction and data | **Single shared** bus for instruction and data |
| Fetches instruction and data simultaneously — faster | Fetches instruction first, then data — two separate fetches |
| Easier to pipeline; high performance | Lower performance |
| Comparatively higher cost | Cheaper |
| Program and data memory physically separate — no accidental corruption | Shared memory — risk of accidental program memory corruption |
| — | Allows self-modifying code |

> Von-Neumann is also called **Princeton Architecture**.

---

## Q17. Bluetooth Communication Interface

- Low cost, low power, **short range wireless** technology for data and voice.
- First proposed by **Ericsson in 1994**.
- Operates at **2.4 GHz** using **Frequency Hopping Spread Spectrum (FHSS)**.

### Data Rates by Version

| Version | Data Rate |
|---------|-----------|
| v1.2 | Up to 1 Mbps |
| v2.0 + EDR | Up to 3 Mbps |
| v3.0 + HS / v4.0 | Up to 24 Mbps |

- **Range:** ~30 to 100 feet.
- Each Bluetooth device has a **48-bit unique identification number**.
- Supports point-to-point and **point-to-multipoint** communication.
- A network of one master + multiple slaves = **Piconet** (max **7 slave devices**).

---

## Q18. ZigBee Communication Interface

- Low power, low cost wireless protocol based on **IEEE 802.15.4-2006**.
- Targeted for **Wireless Personal Area Networking (WPAN)**.
- Supports robust **mesh network** — messages travel through multiple paths.
- Operating frequency: **2.4 GHz**, 902–928 MHz, or 868 MHz bands.
- Range: up to **100 metres** | Data rate: **20 to 250 Kbps**.

### ZigBee Device Types

| Device | Role |
|--------|------|
| **ZigBee Coordinator (ZC)** | Root of network; initiates network; stores network info |
| **ZigBee Router (ZR) / FFD** | Passes information device-to-device |
| **ZigBee End Device (ZED) / RFD** | End device; can only talk with ZR or ZC |

```
         ZC (Coordinator)
        /       |        \
      ZR        ZR        ZR
     /  \      /  \      /  \
   ZED ZED   ZED ZED   ZED ZED
```

**Applications:** Home automation, smart metering, smoke detectors, medical/patient tracking, HVAC control.

---

## Q19. IrDA (Infrared) Communication Interface

- Serial, **half duplex**, **line of sight** based wireless technology.
- Uses infrared waves of the electromagnetic spectrum.
- Typical range: **10 cm to 1 m** (extendable by increasing transmitting power).
- Infrared LED = transmitter; photodiode = receiver.
- Device with both = **Transceiver** (for bidirectional communication).

### IrDA Data Rate Classification

| Type | Data Rate |
|------|-----------|
| Serial IR (SIR) | 9600 bps to 115.2 kbps |
| Medium IR (MIR) | 0.576 Mbps and 1.152 Mbps |
| Fast IR (FIR) | Up to 4 Mbps |
| Very Fast IR (VFIR) | Up to 16 Mbps |
| Ultra Fast IR (UFIR) | Up to 96 Mbps |
| GigaIR | 512 Mbps to 1 Gbps |

---

## Q20. Embedded OS Based Firmware Approach (GPOS vs RTOS)

The OS-based approach is used when tasks need flexible scheduling, real-time response, or multi-tasking.

### General Purpose OS (GPOS)
- Similar to conventional PC development (Windows/Linux).
- Application runs on top of OS.
- OS-supported APIs are used; driver software needed for hardware communication.
- Example: Microsoft Windows XP Embedded.
- Used in: PDAs, handheld devices, POS terminals.

### Real Time OS (RTOS)
- For products demanding **real-time response**.
- Contains a Real Time kernel for **pre-emptive multitasking**, scheduler, multiple threads.
- Flexible scheduling of CPU and memory; task communication mechanisms.
- Examples: **Windows CE, VxWorks, ThreadX, MicroC/OS-II, Embedded Linux, Symbian**.
- Used in: mobile phones, PDAs, flight control systems, ABS.

---

## Q21. Embedded Firmware Development Languages

Three approaches:
1. Assembly Language (low level)
2. High Level Language (C / C++ / Java)
3. Combination of both

### Assembly Language — Advantages
- **Efficient code and data memory usage** — developer knows architecture; optimised code.
- **High performance** — optimised code improves system performance.
- **Low level hardware access** — suitable for OS kernel, device drivers, ISR routines.
- **Code reverse engineering** — machine code can be dis-assembled back.

### Assembly Language — Drawbacks
- **High development time** — harder to program; more lines of code needed.
- **Developer dependency** — no common rules; undocumented approaches make maintenance difficult.
- **Non-portable** — code for one processor family cannot be reused for another; complete rewriting required.

### High Level Language (C) — Advantages
- **Reduced development time** — fewer lines of code per task.
- **Developer independency** — universal syntax; strict rules; easy for a second developer to understand.
- **Portability** — firmware in 'C' can be ported by replacing processor-specific functions and recompiling.

### High Level Language (C) — Limitations
- **Poor optimisation** — some cross-compilers may not generate optimised target-specific instructions.
- **Not suitable for low-level hardware** — inefficient when hardware access timing is critical (nano/microseconds).
- **High investment cost** — integrated development environments with cross-compilers are expensive.

---

## Q22. 'C' vs 'Embedded C'

| **'C'** | **'Embedded C'** |
|---|---|
| General purpose programming language | Subset of conventional 'C' |
| Follows ANSI standard; extensive library files | ANSI 'C' library tailored to target processor |
| Platform-specific compiler | Uses a **Cross-compiler** |
| Runs on systems with abundant memory/storage | Optimised for limited storage and working memory |
| Platform-specific development (OS + processor) | Firmware developed on one machine for a different target |

### Compiler vs Cross-Compiler

| **Native Compiler** | **Cross-Compiler** |
|---|---|
| Converts source code on the same target processor it runs on | Converts source code on one processor for a **different** target processor |
| OS, compiler, and application all run on the same machine | Used in cross-platform development |
| Example: gcc on Linux for Linux | Example: Keil C51 (runs on x86, compiles for 8051) |

---

## Q23. Sensors and Actuators

**Sensor:** A transducer that converts energy from one form to another for measurement or control purposes.
- Captures changes in the environment (events) via the input port.
- Examples: Temperature sensor, magnetic hall effect sensor, humidity sensor.

**Actuator:** A transducer (mechanical or electrical) that converts signals to corresponding physical action (motion). Acts as an output device.
- Examples: Stepper motor, solenoid valve.

> An embedded system is a **reactive system** — sensors feed events in; actuators produce outputs; the core processes the in-between.

---

## ⚡ QUICK REVISION CHEAT SHEET

| Topic | Key Fact |
|-------|----------|
| SPI | 4-wire, full duplex, single master, SS = active LOW |
| I²C | 2-wire (SCL + SDA), half duplex, multi-master, pull-up resistors needed |
| Wi-Fi | IEEE 802.11, 2.4/5 GHz, WEP/WPA security |
| Bluetooth | 2.4 GHz, FHSS, Piconet = 1 master + max 7 slaves |
| ZigBee | IEEE 802.15.4, mesh network, 20–250 Kbps, 100m range |
| IrDA | Line of sight, half duplex, 10cm–1m range |
| Little-Endian | LSB at lowest address (Intel x86) |
| Big-Endian | MSB at lowest address (Motorola 68000) |
| Super Loop | `while(1){}`, no OS, not time-critical, serial execution |
| Availability | $A_i = MTBF / (MTBF + MTTR)$ |
| SRAM | Flip-flops, fast, no refresh, expensive, low density |
| DRAM | Capacitor, slow, needs refresh, cheap, high density |
| FLASH | Latest ROM tech, sector-erasable, high capacity |
| RISC | Few instructions, pipelining, fixed-length, Harvard |
| CISC | Complex instructions, variable-length, more silicon |
| Harvard | Separate buses — fast, pipelined |
| Von-Neumann | Single bus — slower, cheaper, self-modifying code |
| Operational QA | Response, Throughput, Reliability, Maintainability, Security, Safety |
| Non-Operational QA | Testability, Evolvability, Portability, Time-to-market, Cost |

---

*Module-1 Final Q&A — ECE23601 | All numerical answers verified ✅ | All topics from syllabus covered ✅*

# Embedded Systems (ECE23601) — Module-1 Focused Answers

> Answers based on the Module-1 notes provided. Anything added beyond the notes is flagged **[Beyond notes]**. Each diagram is a complete, standalone Overleaf/TikZ document — paste a block into Overleaf and it compiles as-is. Mark weights are shown per question; answers are sized to match.

---

# UNIT-1

## Q1. Comparison of Embedded System and General Computing System (7)

**Definition.** A *general purpose computing system* is a combination of **generic hardware** and a **general purpose OS** that can run a wide variety of user applications. An *embedded system* is a combination of **special-purpose hardware** and (optionally) an **embedded OS**, dedicated to a **specific set of functions**.

The computing revolution began with general-purpose needs, but these proved insufficient for embedded requirements, which demand *"something special"* — fast response to stimuli, meeting deadlines, power efficiency, and limited-memory operation.

| **General Purpose Computing System** | **Embedded System** |
|---|---|
| Generic hardware + General Purpose OS for many applications | Special-purpose hardware + embedded OS for a specific application set |
| Always contains a GPOS | May or may not contain an OS |
| Applications are user-alterable (reinstall OS, add/remove apps) | Firmware is pre-programmed, non-alterable by end-user (except kernel flashing via special settings) |
| Performance is the key factor — "Faster is Better" | Application-specific needs (performance, power, memory) are the key factors |
| Not tailored for low power | Highly tailored for power-saving modes |
| Response is not time-critical | Response is highly time-critical for mission-critical types |
| Execution need not be deterministic | Deterministic for hard real-time types |

**Step-by-step reasoning:**
1. The two differ first in *hardware + OS*: generic+GPOS vs special+embedded OS.
2. They differ in *flexibility*: a general system is reprogrammable by the user; embedded firmware is fixed.
3. They differ in *design priority*: general systems chase raw speed; embedded systems balance performance against power, memory, and deterministic timing.

---

## Q2. Characteristics of an Embedded System (6)

Embedded systems share six important characteristics:

1. **Application and Domain Specific** — designed to perform only its intended function and cannot be repurposed. *Example:* a microwave oven's control unit cannot replace an air conditioner's.
2. **Reactive and Real Time** — constantly interacts with the real world through sensors. Any change in the environment (an **event**) is captured and the control algorithm **reacts** in a designed manner. **Real-time** types must respond within deterministic deadlines and must not miss them (flight control, ABS). *Not all embedded systems are real-time* (e.g. a toy).
3. **Operates in Harsh Environment** — may face dust, high temperature, vibration, shock, supply fluctuation, corrosion, and ageing; the system must withstand these.
4. **Distributed** — may be one unit within a larger system of independent cooperating units. *Example:* an ATM = card reader + transaction unit + currency counter + printer; also SCADA.
5. **Small Size and Weight** — product aesthetics matter ("small is beautiful"); most products demand compact, low-weight designs.
6. **Power Concerns** — must minimise heat dissipation (high heat needs bulky cooling fans); use low-power components and processors with power-saving modes — critical for battery-operated devices.

**Step-by-step reasoning:**
1. A dedicated single purpose makes it application/domain specific.
2. Continuous sensing and reaction on deadlines make it reactive + real-time.
3. Real-world field deployment forces harsh-environment tolerance, distribution, small size, and low power.

---

## Q3. Operational Quality Attributes (6)

**Definition.** Operational quality attributes are **non-functional requirements** concerning the system **while it is in operation/online mode**. Six attributes:

1. **Response** — a measure of the **quickness** of the system; how fast it tracks changes in input. Most embedded systems need near-real-time response (e.g. a flight control system); some don't (an electronic toy).
2. **Throughput** — deals with **efficiency**: the rate of operation over a stated period (e.g. transactions/hour for a card reader). Measured against a **benchmark** — a reference standard for comparison.
3. **Reliability** — how much the proper functioning of the system can be relied upon. Quantified by:
   - **MTBF (Mean Time Between Failures)** — frequency of failures.
   - **MTTR (Mean Time To Repair)** — how long the system may be out of order after a failure.
4. **Maintainability** — ease of maintenance/support; complementary to reliability (more reliable → less corrective maintenance). Two kinds: **preventive/scheduled** (e.g. replace a printer cartridge every *n* prints) and **corrective** (repair after an unexpected failure). It indicates **availability**:

$$A_i = \frac{MTBF}{MTBF + MTTR}$$

5. **Security** — three measures: **Confidentiality** (no unauthorised disclosure), **Integrity** (no unauthorised modification), **Availability** (no unauthorised users).
6. **Safety** — deals with possible damage to operators, the public, and the environment from a breakdown or hazardous/radioactive emission; needs safety analysis to bring consequences to an acceptable level. Threats may be sudden (breakdown) or gradual (emissions).

**Step-by-step reasoning:**
1. "Operational" = qualities visible while the system runs.
2. Response and throughput measure speed and efficiency; reliability and maintainability measure uptime and repair (tied by the MTBF/MTTR availability formula); security and safety protect data/users and people/environment.

---

## Q4. Availability — MTBF = 6 months, MTTR = 4 weeks (5)

**Given:** MTBF = 6 months = **180 days**; MTTR = 4 weeks = **28 days**.

**Formula:**

$$A_i = \frac{MTBF}{MTBF + MTTR}$$

**Substitute:**

$$A_i = \frac{180}{180 + 28} = \frac{180}{208} = 0.8654$$

$$\boxed{A_i = 0.8654 \approx 86.54\%}$$

**Step-by-step reasoning:**
1. Convert both to common units: 6 months = 180 days, 4 weeks = 28 days.
2. Apply the availability formula: `180 / (180 + 28) = 180/208`.
3. Evaluate: **0.8654 → 86.54%**.

---

## Q5. MTTR — Availability = 80%, MTBF = 35 days (5)

**Given:** `A_i` = 80% = **0.8**; MTBF = **35 days**.

**Start from the availability formula and rearrange:**

$$A_i = \frac{MTBF}{MTBF + MTTR} \;\Rightarrow\; MTBF + MTTR = \frac{MTBF}{A_i} \;\Rightarrow\; MTTR = \frac{MTBF}{A_i} - MTBF$$

**Substitute:**

$$MTTR = \frac{35}{0.8} - 35 = 43.75 - 35 = 8.75 \text{ days}$$

$$\boxed{MTTR = 8.75 \text{ days} = 210 \text{ hours}}$$

**Step-by-step reasoning:**
1. Rearrange the availability formula to isolate MTTR.
2. `35 / 0.8 = 43.75` days; subtract MTBF: `43.75 − 35 = 8.75` days.
3. Convert to hours: `8.75 × 24 = 210` hours.

---

## Q6. Non-Operational Quality Attributes (5)

**Definition.** Quality attributes addressed **not** on the basis of operational/runtime aspects — they concern building, evolving, and selling the product. Five attributes:

1. **Testability & Debug-ability** — *Testability* is how easily the design and application can be tested (both **hardware** — peripherals work as desired — and **firmware** — functions as expected). *Debug-ability* is the means of finding unexpected behaviour, at **hardware level** (hardware faults) and **firmware level** (coding flaws).
2. **Evolvability** — the ease with which the product (firmware + hardware) can be **modified** to take advantage of new firmware or hardware technologies.
3. **Portability** — a measure of **system independence**; how easily firmware can run on a different processor/controller/OS. Firmware in **C** ports easily (replace processor-specific functions and recompile); **assembly** code ports poorly.
4. **Time-to-Prototype and Market** — *Time-to-market* is the time from product conception to it being ready for sale; reduced through **rapid prototyping** using off-the-shelf and reusable components. Critical in a competitive market — a competitor releasing first, or the technology being superseded, causes losses.
5. **Per-Unit Cost and Revenue** — cost is a sensitive factor; it must be positioned for marginal profit through market study and cost-benefit analysis. Governed by the **product life-cycle** phases: development → introduction → growth → maturity → decline.

**Step-by-step reasoning:**
1. Non-operational = qualities not tied to the system running.
2. Three concern engineering the product (testability, evolvability, portability) and two concern its commercial viability (time-to-market, cost).

---

## Q7. Core of an Embedded System (10)

**Definition.** Every embedded system is built around a **single-chip controller** that acts as the **master brain**. This controller can be a **microprocessor, microcontroller, FPGA, DSP, or ASIC/ASSP**.

**Reactive nature.** An embedded system is a **reactive system**: control is achieved by **processing information from sensors and user interfaces** and **driving actuators** that regulate a physical variable.
- Input user-interface devices: keyboards, push-button switches.
- Output user-interface devices: LEDs, LCDs, piezoelectric buzzers.

**Elements of the core:**

- **System core (controller)** — executes the firmware (the master brain). May be a µP, µC, FPGA, DSP, or ASIC.
- **Memory** — holds the control algorithm and configuration.
  - *Program storage (ROM)* — OTP, PROM, UVEPROM, EEPROM, FLASH (non-volatile).
  - *Working memory (RAM)* — SRAM, DRAM, NVRAM (volatile).
- **Sensors** — at input ports; convert a real-world quantity into an electrical signal.
- **Actuators** — at output ports; convert the core's output signal into physical action.
- **Communication interfaces** — *onboard:* I²C, SPI, UART, parallel bus; *external:* IR, Bluetooth, Wi-Fi.
- **Other supporting ICs and subsystems** — supervisory circuits, level shifters, etc.

**Operation.** The sensors feed real-world data to the core; the firmware in memory processes it using the control algorithm; the core then drives the actuators to produce the required output, continuously reacting to new events.

**Overleaf / TikZ code — elements of an embedded system:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta}
\begin{document}
\begin{tikzpicture}[
  font=\sffamily,
  core/.style={draw, very thick, rounded corners, fill=blue!12,
               minimum width=4.4cm, minimum height=2.4cm, align=center},
  blk/.style={draw, thick, rounded corners, fill=gray!8,
              minimum width=3cm, minimum height=1.2cm, align=center},
  io/.style={draw, thick, rounded corners, fill=green!10,
             minimum width=3cm, minimum height=1.2cm, align=center},
  ar/.style={-{Latex[length=2.5mm]}, very thick}
]
\node[core] (C) at (0,0)
  {\textbf{System Core}\\[2pt]\footnotesize Microprocessor / Microcontroller\\\footnotesize FPGA / DSP / ASIC / SoC};
\node[blk] (FW)   at (0, 3.0)    {Embedded Firmware};
\node[blk] (MEM)  at (-4.8, 2.6) {Memory\\\footnotesize ROM + RAM};
\node[blk] (COMM) at ( 4.8, 2.6) {Communication\\ Interface};
\node[blk] (SUP)  at (0,-3.0)    {Other supporting\\ ICs \& subsystems};
\node[io]  (SEN)  at (-4.8,-1.2) {Input Ports\\\footnotesize Sensors};
\node[io]  (ACT)  at ( 4.8,-1.2) {Output Ports\\\footnotesize Actuators};
\draw[thick] (FW)   -- (C);
\draw[thick] (MEM)  -- (C);
\draw[thick] (COMM) -- (C);
\draw[thick] (SUP)  -- (C);
\draw[ar] (SEN) -- (C);
\draw[ar] (C)   -- (ACT);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. One controller chip (µP/µC/FPGA/DSP/ASIC) is the brain that runs the firmware.
2. Memory holds the program (ROM) and working data (RAM).
3. Sensors feed inputs and actuators take outputs; communication interfaces and supporting ICs complete the core — together forming a reactive system.

---

# UNIT-2

## Q8. I²C Communication Interface (8)

**Definition.** I²C (**Inter-Integrated Circuit**) is a **synchronous, bi-directional, half-duplex, two-wire** serial interface bus, developed by **Philips Semiconductors** in the early 1980s (originally for TV peripheral chips).

**The two bus lines.**

| Line | Full form | Function |
|---|---|---|
| **SCL** | Serial Clock Line | Carries synchronisation clock pulses (always from the master) |
| **SDA** | Serial Data Line | Carries the serial data (address + data) |

Both lines are **pulled up** to V_CC via resistors, and every device taps onto the same two shared lines.

**Master and slave roles.**
- **Master** — initiates and terminates a transfer, generates the clock, and addresses slaves.
- **Slave** — waits for commands addressed to it and responds.
- Both master and slave can act as transmitter or receiver. I²C supports **multiple masters** on the same bus.

**Working (communication sequence).**
1. **Start condition** — the master pulls SCL HIGH, then pulls **SDA LOW while SCL is HIGH**. This signals the start of a transfer.
2. **Address phase** — the master sends a **7-bit (or 10-bit) slave address** on SDA, MSB first, clocked by SCL. Data is valid during the HIGH period of the clock.
3. **R/W bit** — the master sends a **Read (1) / Write (0)** bit to set the direction.
4. **Acknowledge** — the addressed slave replies with an **ACK** bit on SDA.
5. **Data transfer** — for a **write**, the master sends 8-bit data bytes to the slave; for a **read**, the slave sends data to the master. An **ACK** follows every byte (master ACKs on read, slave ACKs on write).
6. **Stop condition** — the master pulls **SDA HIGH while SCL is HIGH**, ending the transfer.

**Overleaf / TikZ code — I²C two-wire bus:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[
  font=\sffamily,
  dev/.style={draw, very thick, rounded corners, fill=blue!7,
              minimum width=2.4cm, minimum height=1.4cm, align=center},
  res/.style={draw, thick, minimum width=0.4cm, minimum height=0.9cm},
  dot/.style={circle, fill=black, inner sep=1.3pt}
]
\draw[very thick] (0,2) -- (11,2) node[right]{\textbf{SCL}};
\draw[very thick] (0,1) -- (11,1) node[right]{\textbf{SDA}};
\draw[thick] (0.3,3.4) -- (1.7,3.4);
\node[right] at (1.7,3.4) {$V_{CC}$};
\node[res] (rscl) at (0.7,2.75) {};
\node[res] (rsda) at (1.3,2.75) {};
\node[font=\scriptsize,left]  at (rscl.west) {$R_p$};
\node[font=\scriptsize,right] at (rsda.east) {$R_p$};
\draw[thick] (0.7,3.4) -- (rscl.north);
\draw[thick] (1.3,3.4) -- (rsda.north);
\draw[thick] (rscl.south) -- (0.7,2);  \node[dot] at (0.7,2) {};
\draw[thick] (rsda.south) -- (1.3,1);  \node[dot] at (1.3,1) {};
\node[dev] (M)  at (3,-1) {\textbf{Master}};
\node[dev] (S1) at (6,-1) {\textbf{Slave 1}};
\node[dev] (S2) at (9,-1) {\textbf{Slave 2}};
\foreach \x in {3,6,9}{
  \draw[thick] (\x-0.5,-0.3) -- (\x-0.5,2);  \node[dot] at (\x-0.5,2) {};
  \draw[thick] (\x+0.5,-0.3) -- (\x+0.5,1);  \node[dot] at (\x+0.5,1) {};
}
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. I²C needs only two shared pull-up lines — SCL (clock) and SDA (data).
2. A transfer is framed by Start (SDA falls while SCL high) and Stop (SDA rises while SCL high) conditions.
3. Between them the master sends address + R/W, and ACK-checked data bytes flow in the chosen direction — half-duplex, synchronous, multi-master.

---

## Q9. SPI Communication Protocol (8)

**Definition.** SPI (**Serial Peripheral Interface**) is a **synchronous, bi-directional, full-duplex, four-wire** serial interface bus, introduced by **Motorola**. It is a **single-master, multi-slave** system (only one master is active at a time).

**The four signal lines.**

| Line | Full form | Function |
|---|---|---|
| **MOSI** | Master Out Slave In (SI/SDI) | Data from master to slave |
| **MISO** | Master In Slave Out (SO/SDO) | Data from slave to master |
| **SCLK** | Serial Clock | Clock signal, generated by the master |
| **SS** | Slave Select (active low) | Selects which slave is active |

**Working.**
1. The **master generates the clock (SCLK)**, which synchronises all transfers.
2. To talk to a slave, the master **drives that slave's SS line LOW** (active low). Only the selected slave responds; the **MISO lines of unselected slaves float at high impedance** so they don't conflict on the shared bus.
3. Because SPI is **full-duplex**, on each clock pulse a bit is shifted **out on MOSI** and a bit is shifted **in on MISO simultaneously** — data moves both directions at once.
4. Each slave needs its **own SS line**, while MOSI, MISO, and SCLK are shared.

**Configuration registers.** SPI devices contain a **control register** (master/slave selection, baud rate, clock control) and a **status register** (transmission/reception status).

**Overleaf / TikZ code — SPI single-master, two-slave bus:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta}
\begin{document}
\begin{tikzpicture}[
  font=\sffamily,
  master/.style={draw, very thick, rounded corners, fill=blue!8,
                 minimum width=3cm, minimum height=5cm, align=center},
  slave/.style={draw, very thick, rounded corners, fill=green!8,
                minimum width=3cm, minimum height=1.7cm, align=center},
  out/.style={-{Latex[length=2.5mm]}, thick},
  in/.style={{Latex[length=2.5mm]}-, thick},
  lab/.style={font=\scriptsize, fill=white, inner sep=1.5pt}
]
\node[master] (M)  at (0,0)   {\textbf{SPI}\\\textbf{Master}};
\node[slave]  (S1) at (8,1.6) {\textbf{Slave 1}};
\node[slave]  (S2) at (8,-1.6){\textbf{Slave 2}};
\draw[out] (1.5, 2.3) -- node[lab,above]{MOSI} (6.5, 2.3);
\draw[in]  (1.5, 1.9) -- node[lab,below]{MISO} (6.5, 1.9);
\draw[out] (1.5, 1.5) -- node[lab,above]{SCLK} (6.5, 1.5);
\draw[out] (1.5, 1.1) -- node[lab,below]{$\overline{\text{SS1}}$} (6.5, 1.1);
\draw[out] (1.5,-0.9) -- node[lab,above]{MOSI} (6.5,-0.9);
\draw[in]  (1.5,-1.3) -- node[lab,below]{MISO} (6.5,-1.3);
\draw[out] (1.5,-1.7) -- node[lab,above]{SCLK} (6.5,-1.7);
\draw[out] (1.5,-2.1) -- node[lab,below]{$\overline{\text{SS2}}$} (6.5,-2.1);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. SPI uses four wires: shared MOSI/MISO/SCLK plus one SS per slave.
2. The master clocks the bus and selects a slave by pulling its SS low.
3. Full-duplex shifting sends on MOSI and receives on MISO at the same time; unselected slaves release MISO (high-Z).

---

## Q10. Types of Read Only Memory (ROM) (10)

**Definition.** ROM is the **program/code storage memory** that holds the control algorithm. It is **non-volatile** — it retains its contents after power is removed.

**1. Masked ROM (MROM)**
- A **one-time programmable** device using hardwired technology; **factory-programmed** by a masking and metallisation process from the user's data.
- *Advantage:* very **low cost** at high production volume.
- *Limitation:* firmware **cannot be modified** for upgrades.

**2. PROM (Programmable ROM) / OTP**
- Not pre-programmed; the **end user programs it once** using a PROM programmer that selectively **burns fuses** (nichrome/polysilicon). Unburned fuse = logic 1, burned = logic 0 (default state 1).
- Low-cost; **cannot be reprogrammed**. Used for proven, finalised code (e.g. pacemakers, alarm and door-lock systems).

**3. EPROM (Erasable PROM)**
- **Re-programmable**; stores bits by charging the **floating gate** of an FET with high voltage.
- Erased by exposing a **quartz window to UV light** for 20–30 minutes. Tedious — the chip must be removed from the board.

**4. EEPROM (Electrically Erasable PROM)**
- Erased and reprogrammed **electrically, in-circuit**, at **byte/register level**; chip-erase in a few milliseconds.
- More flexible, but **limited capacity** (a few KB) compared to standard ROM.

**5. FLASH**
- A variation of EEPROM combining EEPROM's **reprogrammability** with the **high capacity** of standard ROM; the most popular ROM today.
- Organised in **sectors/pages**, stored in floating-gate MOSFETs; erased per **sector/page** (must erase before rewriting); endurance ~thousands of cycles. *Example:* SST39LF010 (1 Mbit, 100,000 cycles). Used in USB/thumb drives and memory cards.

**Overleaf / TikZ code — ROM classification:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  n/.style={draw, thick, rounded corners, fill=blue!8, align=center, minimum height=0.95cm},
  root/.style={draw, very thick, rounded corners, fill=blue!16, align=center, minimum height=1cm},
  ar/.style={-{Latex[length=2mm]}, thick}
]
\node[root] (rom) at (0,0) {Program Storage Memory (ROM)\\\footnotesize non-volatile};
\node[n] (m)  at (-6.4,-2.3) {Masked ROM\\(MROM)};
\node[n] (p)  at (-3.2,-2.3) {PROM\\(OTP)};
\node[n] (e)  at ( 0,-2.3)   {EPROM\\\footnotesize UV erase};
\node[n] (ee) at ( 3.2,-2.3) {EEPROM\\\footnotesize electrical};
\node[n] (f)  at ( 6.4,-2.3) {FLASH\\\footnotesize sector erase};
\foreach \x in {m,p,e,ee,f}{\draw[ar] (rom) -- (\x);}
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. All ROM is non-volatile; the types differ by **who programs it** and **how it is erased**.
2. MROM (factory, no erase) → PROM (user once, no erase) → EPROM (UV erase) → EEPROM (electrical byte erase) → FLASH (electrical sector erase, high capacity).
3. Flexibility and capacity rise from MROM to FLASH; cost-effectiveness for mass production is highest for MROM.

---

## Q11. Types of Random Access Memory (RAM) (6)

**Definition.** RAM is the **data/working memory** of the controller. It is **volatile** (contents lost on power-off) and offers **direct access** to any location.

**1. SRAM (Static RAM)**
- Stores data as a **voltage**, held in **flip-flops** (typically a 6-transistor cell: 4 form the latch, 2 control access).
- **Fastest** RAM; **does not need refreshing**.
- *Limitations:* **low capacity** and **high cost**. Access time ≈ 10 ns.

**2. DRAM (Dynamic RAM)**
- Stores data as a **charge** on a capacitor (1 MOS transistor + 1 capacitor per cell).
- *Advantages:* **high density, low cost**.
- *Disadvantage:* charge **leaks**, so it must be **refreshed periodically** (every few ms) by a DRAM controller. Access time ≈ 60 ns.

**3. NVRAM (Non-Volatile RAM)**
- SRAM combined with a small **battery** in one package, so it **retains data without external power** (~10-year life). *Example:* DS1644 (32 KB).

| Feature | SRAM | DRAM |
|---|---|---|
| Storage cell | 6 transistors (flip-flop), voltage | 1 transistor + capacitor, charge |
| Refresh | Not needed | Required periodically |
| Density / capacity | Low | High |
| Cost | High | Low |
| Speed | Fast (~10 ns) | Slower (~60 ns) |

**Overleaf / TikZ code — RAM classification:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  n/.style={draw, thick, rounded corners, fill=green!10, align=center, minimum height=0.95cm, minimum width=3cm},
  root/.style={draw, very thick, rounded corners, fill=green!18, align=center, minimum height=1cm},
  ar/.style={-{Latex[length=2mm]}, thick}
]
\node[root] (ram) at (0,0) {Read/Write Memory (RAM)\\\footnotesize volatile};
\node[n] (s) at (-3.4,-2.3) {SRAM\\\footnotesize voltage / flip-flops};
\node[n] (d) at ( 0,-2.3)   {DRAM\\\footnotesize charge / capacitor};
\node[n] (nv) at (3.4,-2.3)  {NVRAM\\\footnotesize SRAM + battery};
\foreach \x in {s,d,nv}{\draw[ar] (ram) -- (\x);}
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. RAM is volatile working memory; three kinds.
2. SRAM uses flip-flops → fast, costly, no refresh; DRAM uses a capacitor → dense, cheap, needs refresh.
3. NVRAM is SRAM + battery, giving RAM speed with non-volatile retention.

---

## Q12. Storage of 32-bit data 3D7A2390 at 0x30000 (Little vs Big Endian) (5)

**Endianness** specifies the order in which the bytes of a multi-byte word are stored in memory.

Split the word into bytes (MSB → LSB): **Byte 3 = 3D**, Byte 2 = 7A, Byte 1 = 23, **Byte 0 = 90**.

- **Little-Endian** — the **lowest-order byte (Byte 0) goes to the lowest address** ("little end first"). *E.g. Intel x86.*
- **Big-Endian** — the **highest-order byte (Byte 3) goes to the lowest address** ("big end first"). *E.g. Motorola 68000.*

| Address | Little-Endian | Big-Endian |
|---|---|---|
| 0x30000 | 90 | 3D |
| 0x30001 | 23 | 7A |
| 0x30002 | 7A | 23 |
| 0x30003 | 3D | 90 |

**Overleaf / TikZ code — memory layout:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[
  font=\sffamily,
  byte/.style={draw, thick, minimum width=1.7cm, minimum height=0.9cm},
  addr/.style={draw, thick, fill=gray!15, minimum width=2.1cm, minimum height=0.9cm, font=\ttfamily},
  hdr/.style={font=\bfseries\large}
]
\node[hdr] at (-2.3, 1.0) {Little-Endian};
\node[hdr] at ( 0.0, 1.0) {Address};
\node[hdr] at ( 2.3, 1.0) {Big-Endian};
\foreach \i/\a/\le/\be in {%
  0/0x30000/90/3D,
  1/0x30001/23/7A,
  2/0x30002/7A/23,
  3/0x30003/3D/90}{
  \node[byte] at (-2.3, -\i) {\le};
  \node[addr] at ( 0.0, -\i) {\a};
  \node[byte] at ( 2.3, -\i) {\be};
}
\node[font=\itshape] at (0, -4.0)
  {Word 3D7A2390: MSB = 3D (Byte\,3), LSB = 90 (Byte\,0)};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Break the word into four bytes from MSB to LSB: 3D, 7A, 23, 90.
2. **Little-endian:** LSB first → 90 at 0x30000, then 23, 7A, 3D.
3. **Big-endian:** MSB first → 3D at 0x30000, then 7A, 23, 90.

---

## Q13. UART Communication Interface (6)

> **[Beyond notes]** — The Module-1 notes only *list* UART as an onboard interface; there is no detailed section, so this answer uses standard knowledge.

**Definition.** UART (**Universal Asynchronous Receiver/Transmitter**) is an **asynchronous, serial, full-duplex** interface. It has **no shared clock line** — instead both ends are pre-configured to the same **baud rate** (bits per second).

**Signal lines.**
- **TX** (transmit) and **RX** (receive), plus a common **GND**.
- The **TX of one device connects to the RX of the other** and vice-versa, giving full-duplex communication.

**Frame format.** Data is sent one byte at a time, framed so the receiver can synchronise:

| Field | Purpose |
|---|---|
| **Start bit** (1) | Line pulled LOW to signal the start of a frame |
| **Data bits** (5–8) | The actual data, sent LSB first |
| **Parity bit** (optional) | Simple error check (even/odd) |
| **Stop bit(s)** (1–2) | Line returns HIGH to mark the end |

**Working.**
1. The line **idles HIGH**.
2. The transmitter sends a **start bit (HIGH→LOW)**; this falling edge tells the receiver to begin sampling.
3. The receiver samples **5–8 data bits** at the agreed baud rate, then an optional **parity bit**, then the **stop bit(s)**.
4. Because timing (not a clock wire) keeps the two in sync, both must agree on baud rate, data length, parity, and stop bits.

Common uses: RS-232 links, debug/console ports, and GPS/Bluetooth modules.

```text
Device A  TX ───────────────► RX  Device B
          RX ◄─────────────── TX
          GND ─────────────── GND

Frame:  idle(H) [Start=L] D0 D1 D2 D3 D4 D5 D6 D7 [Parity] [Stop=H]
                              (data sent LSB first)
```

**Step-by-step reasoning:**
1. UART is asynchronous — no clock line, so sender and receiver use a pre-agreed baud rate.
2. Each byte is wrapped in start/stop bits (with optional parity) so the receiver can lock onto it.
3. Cross-connecting TX↔RX on both sides gives full-duplex serial communication.

---

## Q14. Two Wireless Communication Interfaces (8)

### (a) Bluetooth

**Definition.** A **low-cost, low-power, short-range** wireless technology for data and voice, first proposed by **Ericsson (1994)**.
- Operates at **2.4 GHz** using **Frequency Hopping Spread Spectrum (FHSS)**.
- **Data rates:** v1.2 → 1 Mbps; v2.0+EDR → 3 Mbps; v3.0/v4.0 → up to 24 Mbps. **Range** ≈ 30–100 feet.
- **Structure:** a *physical link* (RF transmission) and a *protocol stack* (rules of communication, held in the Bluetooth IC).
- Each device has a **48-bit unique address**; communication is **packet-based**; supports point-to-point and point-to-multipoint.
- A network of one **master + up to 7 slaves** is called a **Piconet**.

### (b) Wi-Fi

**Definition.** A popular wireless technology for **networked** communication, following the **IEEE 802.11** standard.
- **IP-based** — every device has a unique **IP address**.
- Requires a **Wi-Fi router / Wireless Access Point** that manages communication, **assigns IP addresses**, and **routes data packets**.
- Operates at **2.4 GHz or 5 GHz**; secured by **WEP / WPA**.
- **Data rates:** 1 Mbps–1.73 Gbps (802.11 a/b/g/n/ac); **range** ≈ 100–1000 feet.
- **Connecting:** enable the Wi-Fi radio → scan for networks → list SSIDs → select one → enter password (if secured) → connected.

**Step-by-step reasoning:**
1. Both are 2.4 GHz wireless interfaces, but for different scopes.
2. Bluetooth = short-range, low-power, master + ≤7 slaves (piconet), using FHSS.
3. Wi-Fi = networked, IP-based through an access point, with much higher data rate and range.

---

# UNIT-3

## Q15. RTOS-Based Embedded Firmware Approach (6)

**Context.** In the **OS-based firmware approach**, the application runs on top of an operating system. When a product demands **real-time, time-critical response**, a **Real Time Operating System (RTOS)** is used instead of a general-purpose OS.

**Key features of an RTOS.**
- Contains a **Real-Time kernel** that provides **pre-emptive multitasking** — a higher-priority task can interrupt a lower-priority one.
- Has a **scheduler** that flexibly allocates CPU time, and supports **multiple threads/tasks**.
- Manages **memory** allocation and provides **task communication and synchronisation** mechanisms (so tasks can exchange data safely).
- Guarantees **deterministic** response — tasks meet their deadlines, which a plain super loop cannot guarantee.

**Examples.** Windows CE, VxWorks, pSOS, ThreadX, MicroC/OS-II, Embedded Linux, Symbian — used in mobile phones, PDAs, and handheld devices.

**Step-by-step reasoning:**
1. RTOS is the OS-based approach chosen for time-critical products.
2. Its real-time kernel adds pre-emptive multitasking, scheduling, memory management, and task communication.
3. These give deterministic, deadline-meeting behaviour that the super loop lacks.

---

## Q16. Super Loop-Based Embedded Firmware Approach (6)

**Definition.** The **super loop** (conventional procedural) approach is used for applications that are **not time-critical**. After initialisation, the tasks execute **serially inside an infinite loop**.

**Structure.**
```c
void main()
{
    Configurations();
    Initializations();
    while(1)            // the "super loop"
    {
        Task_1();
        Task_2();
        :
        Task_n();
    }
}
```

**Working.** Common parameters are configured and the system initialised once; then Task 1 … Task n run in a **fixed, hard-coded order**, after which control jumps back to Task 1 and repeats forever. The only way to break the loop is a **hardware reset** (returns to the start) or an **interrupt** (suspends the current task, runs the ISR, then resumes).

**Advantages.**
- No operating system required.
- No need for scheduling or priority assignment.
- Simple and low-cost; priorities/execution order are fixed and hard-coded.

**Applications.** Low-cost products where response time is not critical, and inherently sequential tasks (e.g. a card reader: check card → authenticate → read/write; an electronic video-game toy).

**Drawbacks.**
- A failure in any one task can **hang the whole system** (a Watch Dog Timer helps but adds cost/overhead).
- **Lack of real-timeliness** — as the number of tasks grows, the loop time grows, raising the chance of **missing events** (e.g. a keypress missed unless held long enough). Interrupts can handle urgent external events.

**Step-by-step reasoning:**
1. Initialise once, then loop the tasks endlessly in a fixed order.
2. No OS/scheduler keeps it simple and cheap but not real-time.
3. More tasks → slower loop → missed events; only reset or an interrupt breaks the normal flow.

---

## Q17. Assembly Language → Machine Language Conversion (7)

**Goal.** Convert human-readable **assembly (mnemonics)** into the **machine-code hex file** that is dumped into the code memory.

**Stages.**
1. **Source files (`.asm` / `.src`)** — the program is written as one or more modules; each can be assembled separately to catch syntax errors. Modules share variables/subroutines via the `PUBLIC` and `EXTRN` keywords.
2. **Module Assembler** — translates each source module into a **relocatable object file (`.obj`)** (no absolute addresses yet). *Example:* A51 from Keil.
3. **Linker / Locator** — links all object modules together with the **library files**, resolves external references between modules, and assigns **absolute memory addresses**, producing an **absolute object file**. *Example:* BL51 from Keil.
4. **Object-to-Hex Converter** — converts the absolute object file into the final **machine-code hex file** (ASCII representation of the machine code) for the target's code memory. *Example:* OH51 from Keil.

**Overleaf / TikZ code — assembly to machine code:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=0.9cm,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=4.4cm, minimum height=1cm, fill=blue!6},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[b] (src){Source files (.asm / .src)};
\node[b, below=of src]  (asm){Module Assembler};
\node[b, below=of asm]  (obj){Object files (.obj, relocatable)};
\node[b, below=of obj]  (link){Linker / Locator};
\node[b, below=of link] (abs){Absolute object file};
\node[b, below=of abs]  (hexc){Object-to-Hex converter};
\node[b, below=of hexc] (hex){Machine code (Hex file)};
\node[b, right=1.6cm of link, fill=gray!10] (lib){Library files};
\draw[ar] (src)--(asm);
\draw[ar] (asm)--(obj);
\draw[ar] (obj)--(link);
\draw[ar] (link)--(abs);
\draw[ar] (abs)--(hexc);
\draw[ar] (hexc)--(hex);
\draw[ar] (lib)--(link);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Each assembly module is assembled into a relocatable `.obj`.
2. The linker/locator merges the objects with libraries, resolves references, and fixes absolute addresses → absolute object file.
3. The hex converter emits the final machine-code hex file for the code memory.

---

## Q18. High-Level Language → Machine Language Conversion (7)

**Goal.** Convert **C / Embedded C** source into the target's **machine-code hex file**. This is **cross-platform development**: the firmware is written on a PC (Intel/AMD) and compiled for a *different* target processor (8051, PIC, ARM), so a **cross-compiler** is used.

**Stages.**
1. **Source files (`.c`)** — the program is written in C/Embedded C.
2. **Cross-compiler** — translates each C module into a **relocatable object file (`.obj`)** for the target processor. *Example:* Keil C51.
3. **Linker / Locator** — links the object files with the **library files**, resolves references, and assigns **absolute addresses** → **absolute object file**.
4. **Object-to-Hex Converter** — produces the final **machine-code hex file** for the target's code memory.

> **[Beyond notes]** The notes describe the assembly conversion flow and the *concept* of cross-compilation but do not give an explicit HLL block diagram. The diagram below is the standard representation — the assembly back-end with a **cross-compiler** as the front-end.

**Overleaf / TikZ code — high-level language to machine code:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=0.9cm,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=4.4cm, minimum height=1cm, fill=green!8},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[b] (src){Source files (.c — Embedded C)};
\node[b, below=of src]  (cc){Cross-compiler (e.g. Keil C51)};
\node[b, below=of cc]   (obj){Object files (.obj, relocatable)};
\node[b, below=of obj]  (link){Linker / Locator};
\node[b, below=of link] (abs){Absolute object file};
\node[b, below=of abs]  (hexc){Object-to-Hex converter};
\node[b, below=of hexc] (hex){Machine code (Hex file)};
\node[b, right=1.6cm of link, fill=gray!10] (lib){Library files};
\draw[ar] (src)--(cc);
\draw[ar] (cc)--(obj);
\draw[ar] (obj)--(link);
\draw[ar] (link)--(abs);
\draw[ar] (abs)--(hexc);
\draw[ar] (hexc)--(hex);
\draw[ar] (lib)--(link);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. C source is fed to a **cross-compiler** (runs on the PC, targets another processor) → relocatable `.obj`.
2. The linker/locator merges objects with libraries and assigns absolute addresses.
3. The hex converter emits the final machine-code hex file — the same back-end as the assembly flow, only the front-end differs (compiler vs assembler).

---

## Q19. Advantages and Drawbacks of High-Level Language (5)

**Advantages.**
- **Reduced development time** — requires less knowledge of internal hardware, and fewer lines of code achieve a task than in assembly.
- **Developer independence** — high-level syntax is universal, and with strict rules plus commenting, the firmware is understandable by any other developer.
- **Portability** — firmware written in C can be ported to a different processor by replacing only the processor-specific functions and recompiling.

**Drawbacks.**
- **Poor optimisation by some cross-compilers** — generated code may not be optimised for the target (larger size, lower performance), though modern compilers are improving.
- **Not ideal for low-level hardware** — inefficient where hardware access timing is critical (order of ns/µs).
- **High investment cost** — IDEs with cross-compilers cost more than assembly tools.

**Step-by-step reasoning:**
1. HLL wins on development speed, readability, developer-independence, and portability.
2. It loses on fine code optimisation, precise timing-critical hardware access, and tooling cost.

---

## Q20. Three Types of Mixing Assembly with High-Level Language (6)

Certain situations need assembly and a high-level language combined. There are three types:

**1. Mixing Assembly into High-Level Language** — the program is mostly in **C**, with **assembly added** where needed. Used when:
- the cross-compiler lacks support for a feature (e.g. ISR functions),
- hand-written assembly is wanted for speed/optimisation over compiler output,
- timing-critical hardware access cannot meet specs in C.
*(In Keil C51: use `#pragma SRC` to make the compiler emit a `.SRC` file, rename it to `.A51`, then insert the assembly into the function body.)*

**2. Mixing High-Level Language into Assembly** — the program is mostly in **assembly** (for optimised, high-performance, expertly handled code), with **C added** for parts that are hard in assembly (e.g. 16-bit multiply/division on the 8051) or to use built-in C **library functions**.
*(In C51, up to three arguments are passed via registers R2–R7.)*

**3. Inline Assembly** — assembly instructions are inserted **directly inside the C source**, avoiding the overhead of calling a separate assembly routine. The block is delimited by cross-compiler-specific keywords.
*(In C51: `#pragma asm` … `#pragma endasm`.)*

**Step-by-step reasoning:**
1. Type 1 is C-dominant with assembly added for missing features, speed, or timing.
2. Type 2 is assembly-dominant with C added for difficult operations or library functions.
3. Type 3 embeds assembly inline within C to avoid the cost of a routine call.

---

*End of Module-1 Focused Answers*

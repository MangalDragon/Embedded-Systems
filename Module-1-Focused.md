# Embedded Systems (ECE23601) — Module-1 Focused Answers

> Answers based strictly on the Module-1 notes provided. Anything added beyond the notes is explicitly flagged **[Beyond notes]**. Each diagram is a complete, standalone Overleaf/TikZ document — paste a block into Overleaf and it compiles as-is.

---

# UNIT-1

## Q1. Comparison of Embedded System and General Computing System (7)

| **General Purpose Computing System** | **Embedded System** |
|---|---|
| Combination of generic hardware and a General Purpose OS for a variety of applications | Combination of special-purpose hardware and embedded OS for a specific set of applications |
| Contains a General Purpose Operating System (GPOS) | May or may not contain an operating system |
| Applications are alterable by the user (OS reinstall, add/remove apps) | Firmware is pre-programmed and non-alterable by the end-user (except OS kernel flashing via special settings) |
| Performance is the key deciding factor — "Faster is Better" | Application-specific needs (performance, power, memory) are the key deciding factors |
| Not tailored toward reduced operating power | Highly tailored to use hardware/OS power-saving modes |
| Response requirements are not time-critical | Response time is highly critical for mission-critical systems |
| Need not be deterministic in execution | Execution is deterministic for hard real-time types |

**Step-by-step reasoning:**
1. General = generic hardware + GPOS; embedded = special hardware + optional embedded OS.
2. User can reprogram a general system; embedded firmware is fixed.
3. General optimises raw speed; embedded optimises application-specific constraints (power, memory, timing, determinism).

---

## Q2. Characteristics of an Embedded System (6)

1. **Application and Domain Specific** — built to do only its intended function; cannot be repurposed (a microwave's control unit can't replace an AC's).
2. **Reactive and Real Time** — constantly senses real-world **events** and reacts in a designed way; real-time types must respond within deterministic deadlines (flight control, ABS). Not all are real-time.
3. **Operates in Harsh Environment** — must withstand dust, heat, vibration, shock, supply fluctuation, corrosion, ageing.
4. **Distributed** — may be one unit of a larger system (ATM = card reader + transaction + currency counter + printer; SCADA).
5. **Small Size and Weight** — aesthetics matter; most products demand small, low-weight designs.
6. **Power Concerns** — minimise heat dissipation, use low-power components and power-saving modes; critical for battery devices.

**Step-by-step reasoning:**
1. One dedicated job → application/domain specific.
2. Senses events and reacts on deadlines → reactive + real time.
3. Field deployment → harsh environment; often a sub-unit → distributed; product constraints → small size and low power.

---

## Q3. Operational Quality Attributes (6)

Attributes of the system while it is **operational/online**. Six:

1. **Response** — quickness; how fast it tracks input changes (most need near real-time, e.g. flight control).
2. **Throughput** — efficiency; rate of operation over a period (e.g. transactions/hour), measured against a **benchmark**.
3. **Reliability** — how much you can rely on correct functioning; expressed via **MTBF** (mean time between failures) and **MTTR** (mean time to repair).
4. **Maintainability** — ease of support/repair; complementary to reliability. **Preventive** (scheduled, e.g. replace printer cartridge) and **corrective** (after failure). Linked to availability: `A = MTBF / (MTBF + MTTR)`.
5. **Security** — **Confidentiality** (no unauthorised disclosure), **Integrity** (no unauthorised modification), **Availability** (no unauthorised users).
6. **Safety** — protects operators/public/environment from breakdown or hazardous emissions; needs safety analysis.

**Step-by-step reasoning:**
1. Operational = behaviour while running.
2. Response/throughput = speed & efficiency; reliability/maintainability = uptime & repair (MTBF/MTTR/availability); security/safety = protection of data and people.

---

## Q4. Availability — MTBF = 6 months, MTTR = 4 weeks (5)

**Given:** MTBF = 6 months = 180 days; MTTR = 4 weeks = 28 days.

$$A_i = \frac{MTBF}{MTBF + MTTR} = \frac{180}{180 + 28} = \frac{180}{208}$$

$$\boxed{A_i = 0.8654 \approx 86.54\%}$$

**Step-by-step reasoning:**
1. Convert to common units: 6 months = 180 days, 4 weeks = 28 days.
2. Apply `A = MTBF/(MTBF+MTTR) = 180/208`.
3. = **0.8654 → 86.54%**.

---

## Q5. MTTR — Availability = 80%, MTBF = 35 days (5)

**Given:** `A_i` = 80% = 0.8; MTBF = 35 days.

$$A_i = \frac{MTBF}{MTBF + MTTR} \;\Rightarrow\; MTTR = \frac{MTBF}{A_i} - MTBF$$

$$MTTR = \frac{35}{0.8} - 35 = 43.75 - 35$$

$$\boxed{MTTR = 8.75 \text{ days} = 210 \text{ hours}}$$

**Step-by-step reasoning:**
1. Rearrange the availability formula for MTTR.
2. `35/0.8 = 43.75` days; subtract MTBF: `43.75 − 35 = 8.75` days.
3. Convert: `8.75 × 24 = 210` hours.

---

## Q6. Non-Operational Quality Attributes (5)

Attributes addressed **outside** operational aspects. Five:

1. **Testability & Debug-ability** — how easily the design/firmware can be tested, and how easily faults can be traced (at both hardware and firmware level).
2. **Evolvability** — ease of modifying the product (firmware/hardware) to take advantage of new technologies.
3. **Portability** — measure of system independence; ease of moving firmware to another processor/OS. Code in **C** ports easily (replace processor-specific functions + recompile); assembly ports poorly.
4. **Time-to-Prototype and Market** — time from concept to a sellable product; reduced via rapid prototyping and off-the-shelf/reusable components; critical in a competitive market.
5. **Per-Unit Cost and Revenue** — cost must be positioned correctly for marginal profit; governed by the product life-cycle phases (development → introduction → growth → maturity → decline).

**Step-by-step reasoning:**
1. Non-operational = qualities not tied to runtime behaviour.
2. They cover building/maintaining/evolving the product (testability, evolvability, portability) and its business viability (time-to-market, cost).

---

## Q7. Core of an Embedded System (10)

An embedded system is built around a **single-chip controller** acting as the **master brain**. The controller can be a **microprocessor, microcontroller, FPGA, DSP, or ASIC/ASSP**.

It is a **reactive system**: it processes information from **sensors** and user inputs, and controls **actuators** that regulate physical variables.
- Input user interfaces: keyboards, push-button switches. Output: LEDs, LCDs, buzzers.

**Core elements:**
- **Memory** — holds the control algorithm/configuration. Program storage = ROM (OTP, PROM, UVEPROM, EEPROM, FLASH); working memory = RAM (SRAM, DRAM, NVRAM).
- **Communication interfaces** — onboard: I²C, SPI, UART, parallel bus; external: IR, Bluetooth, Wi-Fi.
- **Sensors** (input ports) and **actuators** (output ports).
- **Other supporting ICs and subsystems.**

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
1. One controller chip is the brain (µP/µC/FPGA/DSP/ASIC).
2. It reads sensors, runs the algorithm, drives actuators (reactive).
3. Supported by memory (ROM+RAM), communication interfaces, and other ICs; firmware directs it all.

---

# UNIT-2

## Q8. I²C Communication Interface (8)

**I²C (Inter-Integrated Circuit)** — a **synchronous, bi-directional, half-duplex, two-wire** serial bus, developed by **Philips** (early 1980s).

**Two lines:** **SCL** (serial clock) and **SDA** (serial data), both with pull-ups.

**Features:** shared bus; devices are **Master** (initiates/terminates transfer, generates clock) or **Slave**; clock is always from the master; supports **multi-master**.

**Sequence:**
1. Master pulls SCL HIGH, then pulls SDA LOW while SCL HIGH → **Start**.
2. Master sends 7-/10-bit slave address (MSB first) + **R/W** bit.
3. Addressed slave returns an **acknowledge** bit.
4. Data byte transferred (master→slave on write, slave→master on read), each byte ACKed.
5. Master pulls SDA HIGH while SCL HIGH → **Stop**.

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
1. Two shared pull-up lines: SCL (clock) + SDA (data).
2. Master starts (SDA low while SCL high), addresses a slave, sets R/W, exchanges ACKed bytes, then stops.
3. Half-duplex, multi-master, clock always from master.

---

## Q9. SPI Communication Protocol (8)

**SPI (Serial Peripheral Interface)** — a **synchronous, bi-directional, full-duplex, four-wire** serial bus by **Motorola**; **single-master, multi-slave**.

**Four lines:**

| Line | Function |
|---|---|
| **MOSI** (Master Out Slave In) | Data master → slave |
| **MISO** (Master In Slave Out) | Data slave → master |
| **SCLK** (Serial Clock) | Clock from master |
| **SS** (Slave Select, active low) | Selects a slave |

Master generates the clock and selects a slave by driving its **SS LOW**; unselected slaves keep MISO at **high impedance**. Devices have a **control register** (master/slave, baud, clock) and **status register**.

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
1. Four wires: shared MOSI/MISO/SCLK + a separate SS per slave.
2. Master drives the clock and selects one slave via SS low.
3. Full-duplex: simultaneous send (MOSI) and receive (MISO); unselected slaves release MISO (high-Z).

---

## Q10. Types of Read Only Memory (ROM) (10)

ROM is **non-volatile** program/code storage. Types:

1. **Masked ROM (MROM)** — one-time, factory-programmed by masking/metallisation. **Low cost** at high volume but **cannot be modified** for upgrades.
2. **PROM / OTP** — programmed once by the **end user** by selectively burning nichrome/polysilicon fuses (unburned = 1, burned = 0). Low cost, cannot be reprogrammed (e.g. pacemakers, alarm/door-lock systems).
3. **EPROM** — re-programmable; stores bits by charging a floating gate; **erased by UV light** through a quartz window (20–30 min). Tedious (must remove from board).
4. **EEPROM** — erased/reprogrammed **electrically, in-circuit**, at byte level; chip-erase in milliseconds. More flexible but limited capacity (a few KB).
5. **FLASH** — variation of EEPROM combining EEPROM's reprogrammability with high capacity; organised in **sectors/pages** (erase per sector before rewrite); ~thousands of erase cycles. The most popular ROM today (e.g. SST39LF010).

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
\node[n] (e)  at ( 0,-2.3)   {EPROM};
\node[n] (ee) at ( 3.2,-2.3) {EEPROM};
\node[n] (f)  at ( 6.4,-2.3) {FLASH};
\foreach \x in {m,p,e,ee,f}{\draw[ar] (rom) -- (\x);}
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. All ROM is non-volatile; types differ by *who* programs and *how* it is erased.
2. MROM (factory, none) → PROM (user once, none) → EPROM (UV erase) → EEPROM (electrical byte erase) → FLASH (electrical sector erase, high capacity).
3. Flexibility and capacity increase from MROM to FLASH.

---

## Q11. Types of Random Access Memory (RAM) (6)

RAM is **volatile** working memory (direct access). Types:

1. **SRAM (Static RAM)** — stores data as **voltage** in **flip-flops** (typically 6 transistors/cell). **Fastest**, no refresh needed; but **low capacity and high cost**. Access time ~10 ns.
2. **DRAM (Dynamic RAM)** — stores data as **charge** on a capacitor (1 transistor + 1 capacitor). **High density, low cost**; but charge leaks → needs **periodic refresh** (via a DRAM controller). Access time ~60 ns.
3. **NVRAM (Non-Volatile RAM)** — SRAM with a small **battery backup** packaged together; retains data without external power (~10-year life), e.g. DS1644 (32 KB).

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
2. SRAM = flip-flops, fast, costly, no refresh; DRAM = capacitor, dense, cheap, needs refresh; NVRAM = SRAM + battery for non-volatility.

---

## Q12. Storage of 32-bit data 3D7A2390 at 0x30000 (Little vs Big Endian) (5)

Bytes (MSB→LSB): Byte 3 = `3D`, Byte 2 = `7A`, Byte 1 = `23`, Byte 0 = `90`.

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
1. Bytes from MSB→LSB: 3D, 7A, 23, 90.
2. **Little-endian** = LSB first → 90 at the lowest address 0x30000, up to 3D.
3. **Big-endian** = MSB first → 3D at 0x30000, up to 90.

---

## Q13. UART Communication Interface (6)

> **[Beyond notes]** — The Module-1 notes only list UART as an onboard interface but give no detailed section, so this answer uses standard knowledge.

**UART (Universal Asynchronous Receiver/Transmitter)** is an **asynchronous, serial, full-duplex** interface — there is **no shared clock line**; instead both ends agree on a **baud rate**.

- **Two data lines:** **TX** (transmit) and **RX** (receive); the TX of one device connects to the RX of the other (and vice-versa), with a common ground.
- **Frame format:** one **start bit** (line pulled low) → **5–8 data bits** (LSB first) → optional **parity bit** (error check) → one or two **stop bits** (line high).
- The line idles HIGH; the falling start bit tells the receiver to begin sampling at the agreed baud rate.
- Common in RS-232 links, debug consoles, GPS/Bluetooth modules.

```text
Device A  TX ───────────────► RX  Device B
          RX ◄─────────────── TX
          GND ─────────────── GND

Frame:  [Start] D0 D1 D2 D3 D4 D5 D6 D7 [Parity] [Stop]
        idle high → start low → data (LSB first) → stop high
```

**Step-by-step reasoning:**
1. UART is asynchronous — no clock line, so sender and receiver must use the same baud rate.
2. Data is framed by start/stop bits (with optional parity) so the receiver can sync on each byte.
3. TX↔RX cross-connection gives full-duplex serial communication.

---

## Q14. Two Wireless Communication Interfaces (8)

### (a) Bluetooth

- Low-cost, low-power, **short-range** wireless for data and voice; first proposed by **Ericsson (1994)**.
- Operates at **2.4 GHz** using **Frequency Hopping Spread Spectrum (FHSS)**.
- Data rates: v1.2 up to 1 Mbps; v2.0+EDR up to 3 Mbps; v3.0/v4.0 up to 24 Mbps. Range ~30–100 feet.
- Each device has a **48-bit unique address**; **packet-based**; point-to-point and point-to-multipoint.
- A network of one master + up to **7 slaves** is a **Piconet**.

### (b) Wi-Fi

- Wireless networked communication following **IEEE 802.11**; **IP-based**, each device has a unique **IP address**.
- Needs a **Wi-Fi router / access point** that assigns IPs and routes packets.
- Operates at **2.4 GHz or 5 GHz**; security via **WEP/WPA**.
- Data rates 1 Mbps–1.73 Gbps (802.11 a/b/g/n/ac); range ~100–1000 feet.
- Connect: enable radio → scan SSIDs → select SSID → enter password → connected.

**Step-by-step reasoning:**
1. Both are 2.4 GHz wireless interfaces but for different scopes.
2. Bluetooth = short-range, low-power, piconet (≤7 slaves), FHSS.
3. Wi-Fi = networked, IP-based via an access point, higher rate and range.

---

# UNIT-3

## Q15. RTOS-Based Embedded Firmware Approach (6)

In the **OS-based approach**, an application runs on top of an operating system. For products needing real-time response, a **Real Time Operating System (RTOS)** is used.

- Contains a **real-time kernel** providing **pre-emptive multitasking**, a **scheduler**, and support for **multiple threads/tasks**.
- Allows **flexible scheduling** of CPU and memory and provides **task communication/synchronisation** mechanisms.
- Suited to products demanding deterministic, time-critical response.
- Examples: **Windows CE, VxWorks, pSOS, ThreadX, MicroC/OS-II, Embedded Linux, Symbian** (used in mobile phones, PDAs, handheld devices).

**Step-by-step reasoning:**
1. RTOS = OS-based approach for time-critical products.
2. Its real-time kernel gives pre-emptive multitasking, scheduling, and task communication.
3. This guarantees deterministic response, unlike a plain super loop.

---

## Q16. Super Loop-Based Embedded Firmware Approach (6)

Used for applications that are **not time-critical**. After initialisation, tasks run **serially inside an infinite loop**.

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

- Execution order is **fixed and hard-coded** (the "super loop"); only a **hardware reset** or an **interrupt** breaks it.
- **Advantages:** no OS needed; no scheduling/priority assignment; simple and cheap.
- **Applications:** low-cost products, inherently sequential tasks (card reader, video-game toy).
- **Drawbacks:** failure in one task can hang the whole system (WDT helps); **lack of real-timeliness** — as tasks grow, loop time grows and events may be missed.

**Step-by-step reasoning:**
1. Initialise once, then loop the tasks forever in fixed order.
2. No OS/scheduler — simple but not real-time.
3. More tasks → slower loop → risk of missed events; only reset/interrupt interrupts the flow.

---

## Q17. Assembly Language → Machine Language Conversion (7)

The flow converts human-readable assembly (mnemonics) into the hex machine code dumped to code memory:

1. **Source files** (`.asm` / `.src`) — one per module; can be assembled separately.
2. **Assembler** — translates each module to an **object file** (`.obj`, relocatable; modules share data via `PUBLIC`/`EXTRN`). e.g. A51.
3. **Linker/Locator** — links all object modules + **library files**, resolves references, and assigns **absolute addresses** → **absolute object file**. e.g. BL51.
4. **Object-to-Hex converter** — produces the **machine code (hex file)**. e.g. OH51.

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
\node[b, below=of asm]  (obj){Object files (.obj)};
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
1. Each source module → assembler → relocatable `.obj`.
2. Linker/locator merges objects + libraries and fixes absolute addresses → absolute object file.
3. Hex converter outputs the final machine-code hex file for the code memory.

---

## Q18. High-Level Language → Machine Language Conversion (7)

The same back-end flow, but the front-end is a **cross-compiler** (firmware is written on a PC and compiled for a *different* target processor).

1. **Source files** (`.c`) — written in C/Embedded C.
2. **Cross-compiler** — translates each C module to a relocatable **object file** (`.obj`). e.g. Keil C51.
3. **Linker/Locator** — links objects + **library files**, resolves references, assigns absolute addresses → **absolute object file**.
4. **Object-to-Hex converter** — produces the **machine code (hex file)** for the target's code memory.

> **[Beyond notes]** The notes describe the assembly flow and the *concept* of cross-compilation, but not an explicit HLL block diagram; the diagram below mirrors the assembly flow with the compiler as the front-end, which is the standard representation.

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
\node[b, below=of cc]   (obj){Object files (.obj)};
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
1. C source → **cross-compiler** (runs on PC, targets another processor) → relocatable `.obj`.
2. Linker/locator merges objects + libraries and assigns absolute addresses.
3. Hex converter produces the final machine-code hex file — same back-end as the assembly flow.

---

## Q19. Advantages and Drawbacks of High-Level Language (5)

**Advantages:**
- **Reduced development time** — less hardware knowledge needed; fewer lines of code per task.
- **Developer independence** — universal syntax + commenting make firmware understandable by any developer.
- **Portability** — C firmware ports to another processor by replacing processor-specific functions and recompiling.

**Drawbacks:**
- **Poor optimisation by some cross-compilers** — may not generate optimised target-specific code (larger/slower), though modern compilers are improving.
- **Not ideal for low-level hardware** — inefficient when access timing is critical (ns/µs order).
- **High investment cost** — IDEs with cross-compilers cost more than assembly tools.

**Step-by-step reasoning:**
1. HLL wins on development speed, readability/portability.
2. It loses on fine optimisation, precise timing-critical hardware access, and tool cost.

---

## Q20. Three Types of Mixing Assembly with High-Level Language (6)

1. **Mixing Assembly into High-Level Language** — the program is mostly in C, but assembly is used where the cross-compiler lacks a feature (e.g. ISRs), where hand-written assembly is faster/more optimised, or where timing-critical hardware access can't meet specs in C. (In Keil C51: use `#pragma SRC` to emit a `.SRC`, rename to `.A51`, insert the assembly.)
2. **Mixing High-Level Language into Assembly** — the program is mostly in assembly (for optimisation/expert control), but C is added for parts hard to do in assembly (e.g. 16-bit multiply/divide on 8051) or to use built-in C library functions. (C51 passes up to 3 arguments via registers R2–R7.)
3. **Inline Assembly** — assembly instructions are inserted directly inside C source, avoiding the overhead of calling a separate routine; delimited by cross-compiler-specific keywords (C51: `#pragma asm` … `#pragma endasm`).

**Step-by-step reasoning:**
1. Type 1: C-dominant, assembly added for missing features/speed/timing.
2. Type 2: assembly-dominant, C added for hard operations/library functions.
3. Type 3: inline assembly embedded in C to avoid call overhead.

---

*End of Module-1 Focused Answers*

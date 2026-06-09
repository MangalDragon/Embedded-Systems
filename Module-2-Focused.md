# Embedded Systems (ECE23601) — Module-2 Focused Answers

> Answers based strictly on the Module-2/3 notes provided. Anything added beyond the notes is explicitly flagged **[Beyond notes]**. Each diagram is a complete, standalone Overleaf/TikZ document — paste one block into Overleaf and it compiles as-is.

---

# UNIT-1

## Q1. Data Flow Model of the ARM Core (8)

A programmer can view the ARM core as a set of **functional units connected by data buses**, where arrows show data flow, lines are buses, and boxes are operation units or storage.

- Data enters the core through the **Data bus**. The data may be an instruction or a data item. In a **Von Neumann** implementation instructions and data share one bus; in a **Harvard** implementation they use separate buses.
- The **instruction decoder** translates instructions before execution.
- The ARM, like all RISC processors, uses a **load–store architecture**: **Load** copies memory → register, **Store** copies register → memory. There are no instructions that process data directly in memory — **all data processing happens in registers**.
- Operands are read from the **register file** onto the internal **A bus (Rn)** and **B bus (Rm)**.
- The **ALU / MAC** takes Rn and Rm and computes a result. Register **Rm can first be pre-processed in the barrel shifter** before entering the ALU.
- **Sign-extend** hardware converts signed 8-/16-bit values to 32 bits when read from memory.
- The result in **Rd** is written back via the **Result bus**. For load/store, the **incrementer** updates the **address register** (broadcast on the **Address bus**) to point to the next sequential location.

**Overleaf / TikZ code — ARM core dataflow model:**

```latex
\documentclass[border=12pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[
  font=\sffamily\small,
  box/.style={draw, thick, rounded corners, fill=blue!6, align=center,
              minimum width=2.6cm, minimum height=1cm},
  rf/.style={draw, thick, fill=green!8, align=center,
             minimum width=6cm, minimum height=1.2cm},
  ar/.style={-{Latex[length=2.4mm]}, semithick}
]
% Data bus
\draw[semithick] (-6,7) -- (4,7) node[right]{\textbf{Data bus}};
% Decode & sign extend
\node[box] (dec) at (-4,5.5) {Instruction\\decoder};
\node[box] (sx)  at ( 2,5.5) {Sign extend};
\draw[ar] (-4,7) -- (dec.north);
\draw[ar] ( 2,7) -- (sx.north);
% Register file
\node[rf] (reg) at (0,3) {Register file\\\footnotesize r0--r15,\ \ cpsr,\ \ spsr};
\draw[ar] (sx.south) -- (sx.south |- reg.north);
% Functional units
\node[box] (bs)   at (-2.5,0.5)  {Barrel shifter};
\node[box] (alu)  at ( 0.5,-1.5) {ALU / MAC};
\node[box] (addr) at ( 5,-1.5)   {Address\\register};
\node[box] (inc)  at ( 5, 1)     {Incrementer};
% Buses from register file
\draw[ar] ([xshift=-5mm]reg.south) -- node[left,font=\scriptsize]{B bus (Rm)} (bs.north);
\draw[ar] ([xshift=10mm]reg.south) -- node[right,font=\scriptsize]{A bus (Rn)} ([xshift=5mm]alu.north);
\draw[ar] (bs.south) |- node[below,font=\scriptsize]{N} (alu.west);
% Address generation
\draw[ar] (alu.east) -- (addr.west);
\draw[ar] (addr.north) -- (inc.south);
\draw[ar] (inc.east) -- ++(1,0) |- (addr.east);
\draw[ar] (addr.south) -- ++(0,-1.2) node[below]{\textbf{Address bus}};
% Result bus
\draw[ar] (alu.south) -- ++(0,-0.8) -| (-6,-2.3) |- (reg.west)
      node[pos=0.78,above,font=\scriptsize]{Result bus};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Data/instructions enter on the Data bus; the decoder interprets instructions.
2. Operands come out of the register file on A (Rn) and B (Rm) buses.
3. Rm may pass through the barrel shifter, then ALU/MAC computes the result.
4. Result returns via the Result bus; addresses go out via the address register/incrementer — reflecting the load–store, register-only processing model.

---

## Q2. Format of the Current Program Status Register (CPSR) (8)

The `cpsr` is a dedicated **32-bit** register the core uses to monitor and control internal operation. It is divided into **four 8-bit fields**:

| Field | Bits | Contents |
|---|---|---|
| **Flags** | [31:24] | Condition flags N, Z, C, V (and Q, J on some cores) |
| **Status** | [23:16] | Reserved (future use) |
| **Extension** | [15:8] | Reserved (future use) |
| **Control** | [7:0] | I, F (interrupt masks), T (state), Mode[4:0] |

- **Condition flags:** **N** (negative, = bit 31 of result), **Z** (zero), **C** (carry), **V** (signed overflow). **Q** (sticky saturation, DSP cores) and **J** (Jazelle state) sit in the flags field on cores that support them.
- **Control field:** **I** bit masks IRQ when 1, **F** bit masks FIQ when 1, **T** bit selects Thumb state, **Mode[4:0]** selects the processor mode.
- Notation: an **uppercase** letter = bit set (1); **lowercase** = bit clear (0).

**Overleaf / TikZ code — CPSR layout:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  f/.style={draw, thick, minimum height=1cm, align=center},
  b/.style={draw, thick, minimum width=0.8cm, minimum height=0.9cm, align=center}
]
\node at (4.5,4.4) {\textbf{CPSR --- 32-bit Current Program Status Register}};
% Four 8-bit fields
\node[f, minimum width=3cm, fill=blue!8]   (fl) at (0,3) {Flags\\\scriptsize[31:24]};
\node[f, minimum width=3cm, fill=gray!12]  (st) at (3,3) {Status\\\scriptsize[23:16]};
\node[f, minimum width=3cm, fill=gray!12]  (ex) at (6,3) {Extension\\\scriptsize[15:8]};
\node[f, minimum width=3cm, fill=green!10] (ct) at (9,3) {Control\\\scriptsize[7:0]};
% Detailed flag bits
\node[b, fill=blue!8] at (-1.2,1) {N};
\node[b, fill=blue!8] at (-0.4,1) {Z};
\node[b, fill=blue!8] at ( 0.4,1) {C};
\node[b, fill=blue!8] at ( 1.2,1) {V};
\node[b, fill=blue!8] at ( 2.0,1) {Q};
\node[b, fill=blue!8] at ( 2.8,1) {J};
\node[font=\scriptsize] at (0.0,0.2) {31\quad30\quad29\quad28\quad27\quad\ 24};
% Detailed control bits
\node[b, fill=green!10] at (6.8,1) {I};
\node[b, fill=green!10] at (7.6,1) {F};
\node[b, fill=green!10] at (8.4,1) {T};
\node[b, fill=green!10, minimum width=2.2cm] at (10.0,1) {Mode[4:0]};
\node[font=\scriptsize] at (7.6,0.2) {7\quad\ 6\quad\ 5};
\node[font=\scriptsize] at (10.0,0.2) {4\ \dots\ 0};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. The 32 bits split into flags / status / extension / control fields.
2. Flags (top bits) hold N Z C V used for conditional execution; status & extension are reserved.
3. The control field (low byte) holds I/F masks, the T state bit, and the 5 mode bits.
4. Together these let the CPSR both *report* results (flags) and *control* operation (masks, state, mode).

---

## Q3. Banked Registers of the ARM Core (8)

The full register file holds **37 registers**: 16 data registers + 2 status registers visible at a time, but **20 of the 37 are "banked"** — hidden except when the processor is in a particular mode.

- A banked register is denoted by the mode suffix, e.g. abort mode has `r13_abt`, `r14_abt`, `spsr_abt`.
- A banked register **maps one-to-one onto a user-mode register**: on a mode change, the new mode's banked register **replaces** the existing one. For example, in IRQ mode instructions still name `r13`/`r14`, but they are physically `r13_irq`/`r14_irq`; `r13_usr`/`r14_usr` are untouched. `r0`–`r12` remain normally accessible.
- **All modes except System** have their own banked registers; **FIQ** banks the most (`r8`–`r14`). Each privileged exception mode also has its own **`spsr`** (there is **no spsr in user/system mode**).
- On an exception the core banks in the new `r13`/`r14`, copies the old `cpsr` into the new mode's `spsr`, and a special return instruction later restores it.

**Overleaf / TikZ code — complete ARM register bank (shaded = banked):**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\footnotesize,
  c/.style={draw, minimum width=1.7cm, minimum height=0.6cm},
  bank/.style={draw, minimum width=1.7cm, minimum height=0.6cm, fill=orange!25},
  hd/.style={font=\sffamily\bfseries\scriptsize, align=center}
]
\def\dx{2.0}\def\dy{0.64}
% Headers
\node[hd] at (0*\dx,0.8){User/\\System};
\node[hd] at (1*\dx,0.8){FIQ};
\node[hd] at (2*\dx,0.8){IRQ};
\node[hd] at (3*\dx,0.8){SVC};
\node[hd] at (4*\dx,0.8){Undef};
\node[hd] at (5*\dx,0.8){Abort};
% User/System column
\foreach \i/\n in {0/r0,1/r1,2/r2,3/r3,4/r4,5/r5,6/r6,7/r7,8/r8,9/r9,
  10/r10,11/r11,12/r12,13/r13 (sp),14/r14 (lr),15/r15 (pc),16/cpsr}{
  \node[c] at (0,-\i*\dy){\n};}
% FIQ banked
\foreach \i/\n in {8/r8\_fiq,9/r9\_fiq,10/r10\_fiq,11/r11\_fiq,12/r12\_fiq,
  13/r13\_fiq,14/r14\_fiq}{\node[bank] at (1*\dx,-\i*\dy){\n};}
\node[bank] at (1*\dx,-17*\dy){spsr\_fiq};
% IRQ / SVC / Undef / Abort banked (r13,r14,spsr)
\foreach \m/\s in {2/irq,3/svc,4/und,5/abt}{
  \node[bank] at (\m*\dx,-13*\dy){r13\_\s};
  \node[bank] at (\m*\dx,-14*\dy){r14\_\s};
  \node[bank] at (\m*\dx,-17*\dy){spsr\_\s};}
\node[font=\sffamily\scriptsize, anchor=north west] at (-1.0,-18.4*\dy)
  {Shaded = banked (visible only in that mode). 37 registers total; 20 banked.};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. 37 physical registers exist, but only ~18 are visible at once.
2. The hidden ones are "banked" — tied to specific modes and named with a `_mode` suffix.
3. On a mode switch the banked `r13`/`r14` (and `spsr`) swap in for the user ones, preserving the interrupted context.
4. FIQ banks `r8`–`r14` for fast handling; user/system have no `spsr`.

---

## Q4. Comparison of ARM and Thumb Instruction Sets (6)

| Feature | ARM (`cpsr` T = 0) | Thumb (`cpsr` T = 1) |
|---|---|---|
| Instruction size | 32-bit | 16-bit |
| Core instructions | 58 | 30 |
| Conditional execution | Most instructions | Only branch instructions |
| Data processing | Direct access to barrel shifter and ALU | Separate barrel-shifter and ALU instructions |
| Program status register | Read–write in privileged mode | No direct access |
| Register usage | 15 general-purpose registers + pc | 8 general-purpose + 7 high registers + pc |

**Step-by-step reasoning:**
1. ARM = full 32-bit set; Thumb = compressed 16-bit set for higher code density.
2. The trade-off: Thumb has fewer instructions, limited conditional execution, and no direct PSR access.
3. The T bit in the `cpsr` selects which set is active.

---

## Q5. ARM Registers and Processor Modes (7)

**Registers** (all 32-bit). Up to 18 active at a time = 16 data + 2 status:

- `r0`–`r12` — general-purpose
- `r13` — **stack pointer (sp)** (head of the current stack)
- `r14` — **link register (lr)** (subroutine return address)
- `r15` — **program counter (pc)** (address of next instruction)
- `cpsr` — current program status register
- `spsr` — saved program status register (privileged modes only)

In ARM state `r0`–`r13` are **orthogonal** (any instruction applies to any of them); `r14`/`r15` are treated specially.

**Processor modes** — 7 total (6 privileged + 1 nonprivileged). Privileged = full read/write of `cpsr`; nonprivileged (user) = read-only of the control field.

| Mode | Abbr. | Privileged | Mode[4:0] |
|---|---|---|---|
| User | usr | no | 10000 |
| FIQ (fast interrupt) | fiq | yes | 10001 |
| IRQ (interrupt) | irq | yes | 10010 |
| Supervisor (after reset / OS kernel) | svc | yes | 10011 |
| Abort (memory fault) | abt | yes | 10111 |
| Undefined (bad instruction) | und | yes | 11011 |
| System (privileged user) | sys | yes | 11111 |

**Step-by-step reasoning:**
1. 16 data registers (3 with special roles: sp, lr, pc) + cpsr/spsr.
2. The mode determines which banked registers are active and the access rights to the cpsr.
3. Six privileged modes handle reset/exceptions; user mode runs applications with restricted rights.

---

## Q6. Pipeline Comparison — ARM7 / ARM9 / ARM10 (10)

A pipeline overlaps **fetch, decode, execute** so the core can complete one instruction per cycle once filled.

| Core | Stages | Pipeline | Throughput |
|---|---|---|---|
| **ARM7** | 3 | Fetch → Decode → Execute | baseline |
| **ARM9** | 5 | Fetch → Decode → Execute → Memory → Write | ~1.1 Dhrystone MIPS/MHz (~13% more than ARM7) |
| **ARM10** | 6 | Fetch → Issue → Decode → Execute → Memory → Write | ~1.3 Dhrystone MIPS/MHz (~34% more than ARM7) |

Longer pipelines do less work per stage → higher clock frequency and throughput, but **higher latency** (more cycles to fill) and possible **data dependencies** between stages.

**Overleaf / TikZ code — the three pipelines:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  s/.style={draw, thick, minimum width=1.8cm, minimum height=0.9cm,
            align=center, fill=blue!6}]
% ARM7
\node[font=\bfseries] at (-2.6,0){ARM7};
\node[s](a1) at (0,0){Fetch};
\node[s,right=2mm of a1](a2){Decode};
\node[s,right=2mm of a2](a3){Execute};
% ARM9
\node[font=\bfseries] at (-2.6,-1.6){ARM9};
\node[s](b1) at (0,-1.6){Fetch};
\node[s,right=2mm of b1](b2){Decode};
\node[s,right=2mm of b2](b3){Execute};
\node[s,right=2mm of b3](b4){Memory};
\node[s,right=2mm of b4](b5){Write};
% ARM10
\node[font=\bfseries] at (-2.6,-3.2){ARM10};
\node[s](c1) at (0,-3.2){Fetch};
\node[s,right=2mm of c1](c2){Issue};
\node[s,right=2mm of c2](c3){Decode};
\node[s,right=2mm of c3](c4){Execute};
\node[s,right=2mm of c4](c5){Memory};
\node[s,right=2mm of c5](c6){Write};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. ARM7 has the classic 3 stages; ARM9 adds Memory + Writeback (5); ARM10 adds Issue (6).
2. More stages → smaller per-stage work → higher frequency and MIPS/MHz.
3. Cost is higher latency and more inter-stage dependency.

---

# UNIT-2

## Q7. Exceptions and Interrupts in ARM7 (12)

An **exception/interrupt** suspends normal sequential execution and forces the `pc` to a fixed entry in the vector table; the old `cpsr` is saved into the new mode's `spsr` and the mode changes. The following cause a mode change:

- **Reset** — on power-up; the first instruction executed; branches to initialization code (enters **Supervisor** mode).
- **Undefined instruction** — the processor cannot decode an instruction (enters **Undefined** mode); also taken if a coprocessor instruction is unrecognized.
- **Software interrupt (SWI)** — executed deliberately by a `SWI` instruction; the usual way to call an operating-system routine (enters **Supervisor** mode).
- **Prefetch abort** — failed attempt to **fetch an instruction** from an address without correct access permission; detected in the **decode** stage (enters **Abort** mode).
- **Data abort** — an instruction tries to access **data memory** without correct permission (enters **Abort** mode).
- **Interrupt request (IRQ)** — raised by external hardware; the normal interrupt level; only taken if the **I bit** is clear (not masked).
- **Fast interrupt request (FIQ)** — for hardware needing faster response; has the most banked registers; only taken if the **F bit** is clear.

Key behaviour: an instruction already in the **execute** stage completes; the rest of the pipeline is abandoned and refilled from the vector entry. The `cpsr` is saved to `spsr` **only** on an exception/interrupt — not when software writes the `cpsr` directly.

**Step-by-step reasoning:**
1. Seven events break normal flow: reset, undef, SWI, prefetch abort, data abort, IRQ, FIQ.
2. Each switches to a specific mode, banks `r13`/`r14`, and saves `cpsr` → `spsr`.
3. IRQ/FIQ are maskable via the I/F bits; the others are not.
4. The pc jumps to that event's fixed vector-table address.

---

## Q8. Vector Table (6)

The **vector table** is a reserved block of memory (a set of 32-bit words) starting at address `0x00000000` (optionally relocated to `0xffff0000`). When an exception/interrupt occurs the processor loads the `pc` from the corresponding entry; each entry holds a **branch instruction** to that event's handler routine.

| Exception / Interrupt | Shorthand | Address | High address |
|---|---|---|---|
| Reset | RESET | 0x00000000 | 0xffff0000 |
| Undefined instruction | UNDEF | 0x00000004 | 0xffff0004 |
| Software interrupt | SWI | 0x00000008 | 0xffff0008 |
| Prefetch abort | PABT | 0x0000000C | 0xffff000C |
| Data abort | DABT | 0x00000010 | 0xffff0010 |
| Reserved | — | 0x00000014 | 0xffff0014 |
| Interrupt request | IRQ | 0x00000018 | 0xffff0018 |
| Fast interrupt request | FIQ | 0x0000001C | 0xffff001C |

**Overleaf / TikZ code — vector table (addresses increase upward):**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  cell/.style={draw, thick, minimum width=5.6cm, minimum height=0.8cm, align=center},
  ad/.style={font=\ttfamily\footnotesize, anchor=east}
]
\foreach \i/\addr/\name in {
  0/0x1C/Fast interrupt request (FIQ),
  1/0x18/Interrupt request (IRQ),
  2/0x14/Reserved,
  3/0x10/Data abort (DABT),
  4/0x0C/Prefetch abort (PABT),
  5/0x08/Software interrupt (SWI),
  6/0x04/Undefined instruction (UNDEF),
  7/0x00/Reset (RESET)}{
  \node[cell] at (0,\i*0.82){\name};
  \node[ad]  at (-3.0,\i*0.82){\addr};
}
\node[font=\bfseries] at (0,8*0.82){Vector Table (base 0x00000000)};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A fixed table at `0x0` (or `0xffff0000`) holds one 32-bit branch per exception.
2. On an event the pc is set to that entry's address.
3. The branch there jumps to the handler — so the table is the dispatch mechanism for all exceptions.

---

## Q9. Core Extensions + Von Neumann Architecture with Cache (6)

**Core extensions** are standard hardware blocks ARM wraps around the core to improve performance and add functionality. There are three:
1. **Cache and tightly coupled memory (TCM)**
2. **Memory management** (MPU / MMU)
3. **Coprocessor interface**

**Von Neumann + unified cache:** Von Neumann cores share one bus for data and instructions, so they use a single **unified cache** holding both. The cache sits between the core and main memory, connected through logic/control and the **AMBA bus interface**, letting the core mostly avoid waiting on slow external memory.

**Overleaf / TikZ code — Von Neumann with unified cache:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=2.8cm, minimum height=1cm, fill=blue!6},
  ar/.style={-{Latex[length=2.4mm]}, thick},
  bi/.style={{Latex[length=2.4mm]}-{Latex[length=2.4mm]}, thick}
]
\node[b] (core){ARM core};
\node[b, right=1.3cm of core] (cache){Unified cache\\\footnotesize(data + instr.)};
\node[b, right=1.3cm of cache] (logic){Logic \&\\control};
\node[b, below=1.2cm of logic] (amba){AMBA bus\\interface unit};
\node[b, left=1.3cm of amba, fill=green!8] (mem){Main memory};
\draw[ar] (core) -- (cache);
\draw[ar] (cache) -- (logic);
\draw[ar] (logic) -- (amba);
\draw[bi] (amba) -- node[above,font=\scriptsize]{on-chip AMBA bus} (mem);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. List the three extensions, then focus on cache.
2. Von Neumann shares one bus → one *unified* cache for both data and instructions.
3. Core → cache → logic → AMBA → main memory is the access path.

---

## Q10. Core Extensions + Harvard Architecture with TCM (6)

The three **core extensions** are cache/TCM, memory management, and the coprocessor interface (as in Q9).

A plain cache boosts performance but makes timing **unpredictable**. Real-time code needs **deterministic** access time, achieved with **Tightly Coupled Memory (TCM)** — fast SRAM placed close to the core that guarantees the cycles to fetch instructions/data. Harvard cores have **separate data and instruction paths**, so they use a **Data TCM** and an **Instruction TCM**, both appearing in the address map and reachable to main memory via the AMBA interface.

**Overleaf / TikZ code — Harvard with TCMs:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=2.6cm, minimum height=1cm, fill=blue!6},
  ar/.style={-{Latex[length=2.4mm]}, thick},
  bi/.style={{Latex[length=2.4mm]}-{Latex[length=2.4mm]}, thick}
]
\node[b] (core){ARM core};
\node[b, right=1.4cm of core] (logic){Logic \&\\control};
\node[b, above right=0.5cm and 1.4cm of logic] (itcm){Instruction\\TCM};
\node[b, below right=0.5cm and 1.4cm of logic] (dtcm){Data\\TCM};
\node[b, below=2.4cm of logic] (amba){AMBA bus\\interface unit};
\node[b, left=1.4cm of amba, fill=green!8] (mem){Main memory};
\draw[ar] (core) -- (logic);
\draw[ar] (logic) -- node[above,font=\scriptsize]{I} (itcm);
\draw[ar] (logic) -- node[below,font=\scriptsize]{D} (dtcm);
\draw[ar] (logic) -- (amba);
\draw[bi] (amba) -- node[above,font=\scriptsize]{AMBA bus (D+I)} (mem);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Caches help throughput but hurt predictability.
2. TCM = fast SRAM with guaranteed cycle count → deterministic real-time access.
3. Harvard separates I and D, so two TCMs (Instruction + Data) hang off the logic/control to main memory.

---

## Q11. Core Extensions + Harvard Architecture with Cache and TCM (6)

The three **core extensions** are cache/TCM, memory management, and the coprocessor interface (as in Q9).

Combining both technologies gives **both** improved performance **and** predictable real-time response: the Harvard core keeps separate **instruction and data caches** (for speed on slow memory) **and** separate **instruction and data TCMs** (for deterministic timing). All connect through logic/control and the AMBA bus interface to main memory.

**Overleaf / TikZ code — Harvard with caches and TCMs:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=2.5cm, minimum height=0.95cm, fill=blue!6},
  ar/.style={-{Latex[length=2.4mm]}, thick},
  bi/.style={{Latex[length=2.4mm]}-{Latex[length=2.4mm]}, thick}
]
\node[b] (core){ARM core};
\node[b, right=1.3cm of core] (logic){Logic \&\\control};
\node[b, above right=0.9cm and 1.0cm of logic] (itcm){Instr. TCM};
\node[b, below right=0.9cm and 1.0cm of logic] (dtcm){Data TCM};
\node[b, right=4.6cm of logic] (icache){Instr. cache};
\node[b, below=0.6cm of icache] (dcache){Data cache};
\node[b, below=2.8cm of logic] (amba){AMBA bus interface unit};
\node[b, left=1.3cm of amba, fill=green!8] (mem){Main memory};
\draw[ar] (core) -- (logic);
\draw[ar] (logic) -- node[above,font=\scriptsize]{I} (itcm);
\draw[ar] (logic) -- node[below,font=\scriptsize]{D} (dtcm);
\draw[ar] (logic) -- node[pos=0.6,above,font=\scriptsize]{I} (icache);
\draw[ar] (logic) -- node[pos=0.6,below,font=\scriptsize]{D} (dcache);
\draw[ar] (icache) |- (amba);
\draw[ar] (dcache) |- (amba);
\draw[bi] (amba) -- node[above,font=\scriptsize]{AMBA bus (D+I)} (mem);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Cache = performance; TCM = determinism — a Harvard core can have both.
2. Being Harvard, each comes as a pair: instruction + data cache, instruction + data TCM.
3. Caches feed the AMBA interface to main memory; TCMs are fast local memory in the address map.

---

## Q12. Memory Management Schemes in ARM (6)

ARM cores provide three levels of memory-management hardware to organize multiple memory devices and protect against inappropriate accesses:

- **No extension (nonprotected)** — fixed memory, no protection; for small, simple systems with no need to guard against rogue applications.
- **Memory Protection Unit (MPU)** — limited protection using a small number of **memory regions**, each defined with access permissions via special coprocessor registers; for systems needing protection but with a simple memory map.
- **Memory Management Unit (MMU)** — full protection; uses **translation tables** in main memory to give a **virtual-to-physical** address map plus access permissions; for sophisticated platform operating systems that support multitasking.

**Step-by-step reasoning:**
1. Three options scale from none → MPU → MMU.
2. MPU = region-based permissions (simple); MMU = table-based virtual memory (full).
3. The choice depends on whether the system needs protection and/or virtual memory.

---

## Q13. Coprocessors (5)

A **coprocessor** attaches to the ARM and extends the core by adding **new instructions** or **configuration registers**. More than one can be connected via the **coprocessor interface**, accessed through a dedicated group of **load–store-type** ARM instructions.

- Example: **coprocessor 15** controls the cache, TCMs, and memory management.
- Example: a coprocessor can add **vector floating-point (VFP)** instructions to the standard set.
- Coprocessor instructions are handled in the **decode** stage: if a coprocessor is present and recognizes the instruction it executes it; otherwise the ARM takes an **undefined instruction exception**, allowing the operation to be **emulated in software**.

**Step-by-step reasoning:**
1. Coprocessors extend the instruction set or add config registers.
2. Accessed via dedicated load–store coprocessor instructions (e.g. CP15 for cache/MMU).
3. Unrecognized coprocessor instructions trap as undefined → software emulation.

---

# UNIT-3

## Q14. MOV and MVN Instructions (6)

Both are **move** (data-processing) instructions. Syntax: `<instruction>{<cond>}{S} Rd, N`, where N is a register or immediate.

| Mnemonic | Description | Operation |
|---|---|---|
| MOV | Move a 32-bit value into a register | `Rd = N` |
| MVN | Move the NOT (bitwise complement) of the value | `Rd = ~N` |

**MOV example** (from notes, Example 3.1):

```text
PRE:  r5 = 5, r7 = 8
      MOV r7, r5      ; r7 = r5
POST: r5 = 5, r7 = 5
```

**MVN example** **[Beyond notes — constructed to illustrate `Rd = ~N`]**:

```text
PRE:  r0 = 0x00000000
      MVN r0, #0      ; r0 = NOT(0)
POST: r0 = 0xFFFFFFFF
```

**Step-by-step reasoning:**
1. MOV copies the operand straight into Rd.
2. MVN copies the bitwise complement (every bit inverted) into Rd.
3. So `MVN r0,#0` inverts all zeros → all ones = `0xFFFFFFFF`.

---

## Q15. Barrel Shifter and its Operations (8)

The **barrel shifter** is hardware that can shift/rotate the 32-bit value in source register **Rm** by a number of positions **before it enters the ALU**, within the same instruction cycle. This adds power to data-processing instructions and gives fast multiply/divide by powers of two. (Some instructions — `MUL`, `CLZ`, `QADD` — do not use it.) Register **Rn** enters the ALU unshifted.

| Mnemonic | Operation | Result |
|---|---|---|
| **LSL** | Logical shift left | `x << y` |
| **LSR** | Logical shift right | `(unsigned) x >> y` |
| **ASR** | Arithmetic shift right | `(signed) x >> y` (keeps sign) |
| **ROR** | Rotate right | bits rotated right, wrap around |
| **RRX** | Rotate right with extend | 1-bit rotate right through the C flag |

The **C flag** (when `S` is used) is set from the last bit shifted out — bit `(32 − y)` of the original value.

**Overleaf / TikZ code — barrel shifter and ALU:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  b/.style={draw, thick, rounded corners, align=center,
            minimum width=2.8cm, minimum height=1cm, fill=blue!6},
  ar/.style={-{Latex[length=2.4mm]}, thick}
]
\node[b] (rm){Rm};
\node[b, below=1cm of rm] (bs){Barrel shifter};
\node[b, right=2.4cm of bs] (alu){ALU};
\node[b, above=1cm of alu] (rn){Rn};
\node[b, right=2.4cm of alu] (rd){Rd};
\draw[ar] (rm) -- (bs);
\draw[ar] (bs) -- node[above,font=\scriptsize]{N} (alu);
\draw[ar] (rn) -- (alu);
\draw[ar] (alu) -- (rd);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Rm is optionally pre-shifted by the barrel shifter; Rn goes straight to the ALU.
2. Five operations: LSL, LSR, ASR, ROR, RRX (ASR preserves sign; RRX uses the carry).
3. The shift happens free within the instruction cycle, enabling fast ×/÷ by powers of two.

---

## Q16. Result of `MOV r7, r5, LSL #2` (r5 = 5, r7 = 8) (5)

`LSL #2` shifts r5 left by 2 bits (= ×4); MOV copies the result into r7. MOV does not change the source r5.

```text
PRE:   r5 = 5,  r7 = 8
       MOV r7, r5, LSL #2      ; r7 = r5 << 2 = r5 * 4
POST:  r5 = 5
       r7 = 20        (0x00000014)
```

**Step-by-step reasoning:**
1. `5 << 2` = `5 × 4` = `20`.
2. The shifted value is written to r7 → **r7 = 20**.
3. r5 is only a source → unchanged at **5**.

---

## Q17. Two Compare Instructions with Examples (8)

Comparison instructions test/compare a register against a 32-bit value `N`, **updating only the `cpsr` condition flags** (no register is changed). Syntax: `<instruction>{<cond>} Rn, N`.

**1. CMP (compare)** — does `Rn − N`, discards the result, sets the flags. Example (from notes, 3.10):

```text
PRE:   cpsr = nzcvqiFt_USER,  r0 = 4,  r9 = 4
       CMP r0, r9
POST:  cpsr = nZcvqiFt_USER        ; Z set → operands equal
```

**2. TST (test)** — does a logical `Rn AND N`, discards the result, sets the flags (used to test if particular bits are set). Example **[Beyond notes — constructed to illustrate TST]**:

```text
PRE:   cpsr = nzcvqiFt_USER,  r0 = 0x00000001
       TST r0, #0x1               ; test bit 0
POST:  cpsr = nzcvqiFt_USER       ; bit set → result non-zero → Z stays clear
```

(`TEQ` works the same way using logical EOR.)

**Step-by-step reasoning:**
1. CMP = subtract-and-discard; equal operands give zero → Z flag set.
2. TST = AND-and-discard; used to check whether selected bits are set.
3. Neither writes a destination register — they only update the cpsr flags for later conditional execution.

---

## Q18. BIC and EOR Instructions with Examples (8)

Both are **logical** data-processing instructions. Syntax: `<instruction>{<cond>}{S} Rd, Rn, N`.

| Mnemonic | Description | Operation |
|---|---|---|
| **BIC** | Bit clear (AND NOT) | `Rd = Rn AND NOT(N)` |
| **EOR** | Exclusive OR | `Rd = Rn XOR N` |

**BIC example** (from notes, Example 3.9) — clears the bits of r1 that are set in r2:

```text
PRE:   r1 = 0b1111,  r2 = 0b0101
       BIC r0, r1, r2            ; r0 = r1 AND NOT(r2)
POST:  r0 = 0b1010
```

**EOR example** **[Beyond notes — constructed to illustrate XOR]**:

```text
PRE:   r1 = 0b1100,  r2 = 0b1010
       EOR r0, r1, r2            ; r0 = r1 XOR r2
POST:  r0 = 0b0110
```

**Step-by-step reasoning:**
1. BIC inverts N then ANDs with Rn → it **clears** in Rn exactly the bits set in N (`1111` clear `0101` = `1010`).
2. EOR sets each result bit where the two inputs **differ** (`1100` XOR `1010` = `0110`).
3. Both write Rd and leave the sources unchanged.

---

## Q19. Result of `ADD r4, r7, r5, LSR #3` (r5 = 5, r7 = 8) (5)

`LSR #3` shifts r5 right (logical) by 3 bits; the result is added to r7 and stored in r4. r5 and r7 (sources) are unchanged.

```text
r5 = 5 = 0b101
r5 LSR #3 = 0b101 >> 3 = 0      (all bits shifted out)
r4 = r7 + (r5 LSR #3) = 8 + 0 = 8
```

```text
POST:  r4 = 8   (0x00000008)
       r5 = 5
       r7 = 8
```

**Step-by-step reasoning:**
1. `5` is `0b101`; shifting right by 3 pushes every bit out → `0`.
2. `r4 = r7 + 0 = 8 + 0 = 8`.
3. r5 and r7 are sources only → unchanged (**r5 = 5, r7 = 8**).

---

## Q20. Result of `EOR r0, r1, r3` (5)

> Note: the question text lists "r0, r1, r2", but the given instruction is `EOR r0, r1, r3` and supplies `r3`. I answer for the actual instruction (`r0, r1, r3`); **r2 is not involved** and is unchanged.

`EOR` is bitwise XOR. With `r1 = 0x02040608`, `r3 = 0x10305072`:

```text
   0x02040608
 ⊕ 0x10305072
 ------------
   0x1234567A
```

Byte-wise: `02⊕10=12`, `04⊕30=34`, `06⊕50=56`, `08⊕72=7A`.

```text
POST:  r0 = 0x1234567A
       r1 = 0x02040608   (unchanged)
       r3 = 0x10305072   (unchanged)
```

**Step-by-step reasoning:**
1. XOR each bit of r1 with r3; equal bits → 0, differing bits → 1.
2. Done per byte: `12 34 56 7A`.
3. Result goes to r0 = **0x1234567A**; sources r1, r3 unchanged.

---

*End of Module-2 Focused Answers*

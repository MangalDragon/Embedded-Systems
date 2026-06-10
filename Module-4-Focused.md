# Embedded Systems (ECE23601) — Module-4 Focused Answers

> Exception & Interrupt Handling on the ARM processor. Answers based on the Module-4 notes provided; anything added beyond them is flagged **[Beyond notes]**. Each diagram is a complete, standalone Overleaf/TikZ document — paste a block into Overleaf and it compiles as-is.
>
> **Notation reminder used throughout:** in the `cpsr`, a capital letter = bit **set**; for the **I/F interrupt masks a capital letter means the interrupt is *disabled*** (lowercase = enabled). e.g. `nzcvqIft_irq` ⇒ IRQ disabled, FIQ enabled.

---

# UNIT-1

## Q1. ARM Processor Exceptions and Modes (10)

**Definition.** An **exception** is any condition that halts the normal sequential execution of instructions. The ARM processor has **seven exceptions**: Reset, Data Abort, FIQ, IRQ, Prefetch Abort, Software Interrupt (SWI), and Undefined Instruction.

**Each exception forces a specific processor mode.** When an exception causes a mode change, the core **automatically**:
1. saves the `cpsr` into the **`spsr`** of the exception mode,
2. saves the `pc` into the **`lr`** of the exception mode,
3. sets the `cpsr` to the **exception mode** (and switches to **ARM state**),
4. sets the `pc` to the address of the **exception handler** (vector entry).

**User** and **System** modes are the only two **not** entered by an exception — they must be entered by manually modifying the `cpsr`.

| Exception | Mode | Main purpose |
|---|---|---|
| Fast Interrupt Request | FIQ | Fast interrupt handling |
| Interrupt Request | IRQ | Interrupt handling |
| SWI and Reset | SVC | Protected mode for operating systems |
| Prefetch Abort & Data Abort | Abort | Virtual memory / memory-protection handling |
| Undefined Instruction | Undefined | Software emulation of coprocessors |

**Overleaf / TikZ code — exceptions and associated modes:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  ex/.style={draw, thick, rounded corners, fill=blue!8,  minimum width=4cm, minimum height=0.8cm, align=center},
  md/.style={draw, thick, rounded corners, fill=green!12, minimum width=2.6cm, minimum height=0.8cm, align=center},
  ar/.style={-{Latex[length=2mm]}, thick}
]
\node[ex] (reset) at (0, 5) {Reset};
\node[ex] (swi)   at (0, 4) {Software Interrupt};
\node[ex] (dabt)  at (0, 3) {Data Abort};
\node[ex] (pabt)  at (0, 2) {Prefetch Abort};
\node[ex] (fiq)   at (0, 1) {Fast Interrupt Request};
\node[ex] (irq)   at (0, 0) {Interrupt Request};
\node[ex] (und)   at (0,-1) {Undefined Instruction};
\node[md] (svc)  at (7, 4.5) {SVC};
\node[md] (abt)  at (7, 2.5) {Abort};
\node[md] (fiqm) at (7, 1)   {FIQ};
\node[md] (irqm) at (7, 0)   {IRQ};
\node[md] (undm) at (7,-1)   {Undefined};
\draw[ar] (reset)--(svc);   \draw[ar] (swi)--(svc);
\draw[ar] (dabt)--(abt);    \draw[ar] (pabt)--(abt);
\draw[ar] (fiq)--(fiqm);     \draw[ar] (irq)--(irqm);
\draw[ar] (und)--(undm);
\node[font=\itshape\scriptsize] at (3.5,-2)
  {On any exception the core also switches to ARM state.};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Seven exceptions can break normal flow; each maps to a dedicated mode.
2. On entry the core saves `cpsr`→`spsr`, `pc`→`lr`, switches mode + ARM state, and jumps to the handler.
3. Only User/System modes need a manual `cpsr` change to enter.

---

## Q2. Vector Table Offsets, Example, and Exception Priorities (10)

**Vector table.** A table of addresses (starting at `0x00000000`, optionally `0xffff0000`) the core branches to when an exception is raised. Each entry holds a branch-type instruction to the handler. Common forms:
- `B <address>` — pc-relative branch.
- `LDR pc, [pc, #offset]` — loads an absolute 32-bit handler address stored near the table (slight delay, but reaches any address).
- `LDR pc, [pc, #-0xff0]` — loads the ISR address from `0xfffff030` (only with a VIC PL190).
- `MOV pc, #immediate` — copies an 8-bit immediate (rotated right by an even count) into pc.

| Exception | Mode | Vector offset |
|---|---|---|
| Reset | SVC | +0x00 |
| Undefined Instruction | UND | +0x04 |
| Software Interrupt (SWI) | SVC | +0x08 |
| Prefetch Abort | ABT | +0x0C |
| Data Abort | ABT | +0x10 |
| Not assigned | — | +0x14 |
| IRQ | IRQ | +0x18 |
| FIQ | FIQ | +0x1C |

**Example (typical vector table).** Most entries use an indirect `LDR pc,[pc,#…]`; the Undefined entry uses a direct branch. The FIQ entry also uses `LDR` (it does *not* exploit being able to place the handler directly at the FIQ vector):

```asm
0x00000000  RESET:  ldr  pc, [pc, #reset]
0x00000004  UNDEF:  b    undInstr
0x00000008  SWI  :  ldr  pc, [pc, #swi]
0x0000000c  PABT :  ldr  pc, [pc, #prefetch]
0x00000010  DABT :  ldr  pc, [pc, #data]
0x00000014  -    :  ldr  pc, [pc, #notassigned]
0x00000018  IRQ  :  ldr  pc, [pc, #irq]
0x0000001c  FIQ  :  ldr  pc, [pc, #fiq]
```

**Priority levels** (highest = 1):

| Exception | Priority | I bit | F bit |
|---|---|---|---|
| Reset | 1 | 1 | 1 |
| Data Abort | 2 | 1 | — |
| FIQ | 3 | 1 | 1 |
| IRQ | 4 | 1 | — |
| Prefetch Abort | 5 | 1 | — |
| SWI | 6 | 1 | — |
| Undefined Instruction | 6 | 1 | — |

**Overleaf / TikZ code — vector table (offset and mode):**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  cell/.style={draw, thick, minimum width=5.4cm, minimum height=0.8cm, align=center},
  off/.style={font=\ttfamily\footnotesize, anchor=east},
  md/.style={font=\footnotesize, anchor=west}
]
\foreach \i/\o/\n/\m in {%
  7/+0x1C/{Fast Interrupt Request}/FIQ,
  6/+0x18/{Interrupt Request}/IRQ,
  5/+0x14/{Not assigned}/{--},
  4/+0x10/{Data Abort}/ABT,
  3/+0x0C/{Prefetch Abort}/ABT,
  2/+0x08/{Software Interrupt}/SVC,
  1/+0x04/{Undefined Instruction}/UND,
  0/+0x00/{Reset}/SVC}{
  \node[cell] at (0,\i*0.82){\n};
  \node[off] at (-2.85,\i*0.82){\o};
  \node[md]  at ( 2.85,\i*0.82){\m};
}
\node[font=\bfseries] at (0,8*0.82){Vector Table\quad(offset \textbar\ entry \textbar\ mode)};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. The table at `0x0` holds one branch per exception at fixed offsets (0x00…0x1C).
2. Entries usually use `LDR pc,[pc,#…]` (any address) or a direct `B`.
3. When several exceptions coincide, the priority table decides order (Reset highest, SWI/Undefined lowest).

---

## Q3. Exceptions on the ARM Processor and their Priority Levels (10)

Because exceptions can occur simultaneously, the processor uses a **priority mechanism** (see priority table in Q2). Summary of each, highest first:

- **Reset (1)** — highest priority; taken whenever signalled (power-on). The handler **initialises the system** (memory, caches, **stack pointers for all modes**). External sources are initialised **before** enabling IRQ/FIQ (to avoid spurious interrupts); the first instructions assume no exception/interrupt occurs, so they avoid SWIs, undefined instructions, and abortable memory accesses.
- **Data Abort (2)** — raised when the MMU/memory controller flags an **invalid data address** or an access without correct permission. **FIQ is not disabled**, so an FIQ can be taken inside the Data Abort handler and control returns afterwards.
- **FIQ (3)** — highest-priority **interrupt**; raised when a peripheral asserts `nFIQ`. On entry **both IRQ and FIQ are disabled**, so no external source can interrupt until re-enabled by software.
- **IRQ (4)** — second-highest interrupt; raised on `nIRQ`. Entered only if **no FIQ and no Data Abort** occur. On entry **IRQ is disabled** and stays disabled until the source is cleared.
- **Prefetch Abort (5)** — raised when an **instruction fetch** faults; taken in the **execute stage** if no higher exception is pending. On entry IRQ is disabled, **FIQ unchanged** (an enabled FIQ can still be taken).
- **SWI (6)** — raised by executing the `SWI` instruction; on entry the `cpsr` is set to **supervisor mode**. For nested SWIs, save `r14` and `spsr` first to avoid corruption.
- **Undefined Instruction (6)** — raised when an instruction not in the ARM/Thumb set reaches execute and **no coprocessor claims it**. Shares priority 6 with SWI because the two **cannot occur at the same time** (an instruction can't be both).

**Step-by-step reasoning:**
1. Priority resolves simultaneous exceptions; Reset is absolute, Data Abort next, then FIQ > IRQ > Prefetch Abort > {SWI, Undefined}.
2. Higher exceptions also mask interrupts (I/F bits) so a handler isn't immediately re-interrupted.
3. SWI and Undefined share the lowest level because they are mutually exclusive in the same instruction.

---

## Q4. Loading the Offset Address into the Link Register during an Exception (10)

When an exception occurs, `lr` is set from the current `pc`, but the **correct return address depends on the exception** (because of the pipeline). The handler must adjust `lr` before returning.

| Exception | Return address | Use |
|---|---|---|
| Reset | — | `lr` undefined on reset |
| Data Abort | `lr − 8` | Re-execute the faulting instruction |
| FIQ | `lr − 4` | Return from FIQ handler |
| IRQ | `lr − 4` | Return from IRQ handler |
| Prefetch Abort | `lr − 4` | Re-execute the faulting instruction |
| SWI | `lr` | Next instruction after the SWI |
| Undefined Instruction | `lr` | Next instruction after the instruction |

**Method 1 — `SUBS` on return** (subtract the offset while returning). The `S` with `pc` as destination **auto-restores `cpsr` from `spsr`**:

```asm
handler
    <handler code>
    SUBS pc, r14, #4    ; pc = r14 - 4, and cpsr <- spsr
```

**Method 2 — adjust `lr` at entry, then `MOVS`:**

```asm
handler
    SUB  r14, r14, #4   ; correct the return offset first
    <handler code>
    MOVS pc, r14        ; return; cpsr <- spsr
```

**Method 3 — save `lr` on the interrupt stack** (needed when the handler nests/uses subroutines):

```asm
handler
    SUB   r14, r14, #4          ; correct offset
    STMFD r13!, {r0-r3, r14}    ; save context on the stack
    <handler code>
    LDMFD r13!, {r0-r3, pc}^    ; return; ^ restores cpsr <- spsr
```

**Step-by-step reasoning:**
1. The pipeline makes the stored `lr` point a few bytes past the true return point, so most interrupts need `lr − 4` (data/prefetch abort need `lr − 8`; SWI/undef need `lr`).
2. Method 1 corrects and returns in one `SUBS`; Method 2 corrects at entry then `MOVS`; Method 3 stores the adjusted `lr` on the stack and restores via `LDMFD …}^`.
3. In every case the `S`/`^` restores `cpsr` from `spsr` on return.

---

## Q5. Assigning Interrupts and Interrupt Latency (10)

### Assigning interrupts
A designer decides which peripheral generates which request (in hardware, software, or both). An **interrupt controller** connects many external sources to the two ARM lines and can route a source to IRQ or FIQ. Standard practice:
- **Software Interrupts (SWI)** — reserved to call **privileged OS routines** (e.g. switching a user-mode program to a privileged mode).
- **Interrupt Requests (IRQ)** — general-purpose interrupts (e.g. a periodic timer for context switching). IRQ has **lower priority and higher latency**.
- **Fast Interrupt Requests (FIQ)** — reserved for a **single source needing fast response** (e.g. DMA block moves).

So in an embedded OS, FIQ serves one specific application, leaving IRQ for general OS activity.

### Interrupt latency
**Interrupt latency** is the interval from an interrupt request being raised to the **first fetch of an instruction of its ISR**. It depends on **both hardware and software**; if interrupts aren't handled promptly the system responds slowly. Software has two main ways to minimise latency:
1. **Nested interrupt handler** — re-enables interrupts as soon as the current source is serviced (but before the handler is fully done), so further interrupts can be handled; control returns to the original ISR afterwards.
2. **Prioritization** — program the controller to ignore same-/lower-priority interrupts so only a **higher-priority** interrupt can pre-empt the handler, then re-enable interrupts. Higher-priority interrupts thus get **lower average latency**.

**Step-by-step reasoning:**
1. Assignment policy: SWI → OS calls, IRQ → general, FIQ → one fast source.
2. Latency = request-to-first-ISR-fetch, set by HW + SW.
3. Nesting and prioritization both cut latency for time-critical interrupts.

---

## Q6. IRQ and FIQ Exceptions with Flow Diagram (10)

IRQ/FIQ are taken only when their mask is clear. The core **finishes the current execute-stage instruction**, then hardware:
1. changes to the interrupt-request mode,
2. saves `cpsr` → `spsr_<mode>`,
3. saves `pc` → `lr_<mode>`,
4. disables interrupts (IRQ only, or **both** for FIQ),
5. branches to the vector entry.

**IRQ (Example 9.5).** From user mode (both interrupts enabled): on IRQ the core sets the **I bit (IRQ disabled)** but leaves **FIQ enabled** (higher priority), enters IRQ mode, copies `cpsr`→`spsr_irq`, sets `r14_irq = pc`, and `pc = 0x18`.

**FIQ (Example 9.6).** Same procedure, but **both I and F bits are set** (IRQ *and* FIQ disabled). Because `r8–r12` are **banked in FIQ mode**, they need not be saved and can hold temporary data — ideal for a single-source, high-priority, low-latency interrupt; `pc = 0x1C`.

**Overleaf / TikZ code — IRQ and FIQ state flow:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  st/.style={draw, thick, rounded corners, align=center, fill=blue!6,
             text width=4.2cm, minimum height=1.5cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
% IRQ row
\node[font=\bfseries] at (-3.2,0) {IRQ};
\node[st] (i1) at (0,0)  {\textbf{1.} \texttt{nzcvqift\_usr}\\(IRQ \& FIQ enabled)};
\node[st] (i2) at (5.2,0){\textbf{2.} \texttt{nzcvqIft\_irq}\\spsr\_irq = cpsr\\r14\_irq = pc\\pc = 0x18};
\node[st] (i3) at (10.4,0){\textbf{3.} Software\\handler (ISR)};
\draw[ar] (i1)--node[above,font=\scriptsize]{IRQ} (i2);
\draw[ar] (i2)--(i3);
\draw[ar] (i3) to[out=-90,in=-90] node[below,font=\scriptsize]{return to user code} (i1);
% FIQ row
\node[font=\bfseries] at (-3.2,-4) {FIQ};
\node[st] (f1) at (0,-4)  {\textbf{1.} \texttt{nzcvqift\_usr}\\(IRQ \& FIQ enabled)};
\node[st] (f2) at (5.2,-4){\textbf{2.} \texttt{nzcvqIFt\_fiq}\\spsr\_fiq = cpsr\\r14\_fiq = pc\\pc = 0x1C};
\node[st] (f3) at (10.4,-4){\textbf{3.} Software\\handler (ISR)};
\draw[ar] (f1)--node[above,font=\scriptsize]{FIQ} (f2);
\draw[ar] (f2)--(f3);
\draw[ar] (f3) to[out=-90,in=-90] node[below,font=\scriptsize]{return to user code} (f1);
\end{tikzpicture}
\end{document}
```

> **[Note on notation]** Your notes' diagram labels state 1 as `…IFt…`, but its text says both interrupts are *enabled* in state 1 — so the correct label is `…ift…` (lowercase = enabled), used above.

**Step-by-step reasoning:**
1. Both share the same 5-step hardware entry sequence.
2. IRQ disables only IRQ (FIQ stays enabled); FIQ disables both.
3. FIQ's banked `r8–r12` avoid register saving → faster, low-latency single-source handling.

---

## Q7. Enabling and Disabling IRQ and FIQ Exceptions (10)

Interrupts are enabled/disabled by modifying the `cpsr` **control field** (`_c`, bits [7:0]) while in a **privileged mode**, using three instructions:
1. `MRS` — copy `cpsr` into a register (e.g. `r1`).
2. `BIC` (to enable) or `ORR` (to disable) — clear/set the mask bit. **IRQ = bit 7 = `0x80`**, **FIQ = bit 6 = `0x40`**.
3. `MSR` — write the register back into `cpsr_c`.

The change takes effect only once the `MSR` completes the **execute stage**.

**Enable** (clear the mask bit with `BIC`):
```asm
enable_irq            ; pre:  nzcvqIFt_SVC
    MRS r1, cpsr
    BIC r1, r1, #0x80  ; clear IRQ mask
    MSR cpsr_c, r1     ; post: nzcvqIft_SVC

enable_fiq
    MRS r1, cpsr
    BIC r1, r1, #0x40  ; clear FIQ mask
    MSR cpsr_c, r1     ; post: nzcvqIft_SVC
```

**Disable** (set the mask bit with `ORR`):
```asm
disable_irq           ; pre:  nzcvqIft_SVC
    MRS r1, cpsr
    ORR r1, r1, #0x80  ; set IRQ mask
    MSR cpsr_c, r1     ; post: nzcvqIFt_SVC

disable_fiq
    MRS r1, cpsr
    ORR r1, r1, #0x40  ; set FIQ mask
    MSR cpsr_c, r1     ; post: nzcvqIFt_SVC
```

**Step-by-step reasoning:**
1. Read `cpsr` (`MRS`), modify the mask bit, write back (`MSR cpsr_c`).
2. `BIC` clears the bit → enables; `ORR` sets it → disables. Masks: IRQ `0x80`, FIQ `0x40`.
3. Must be in privileged mode; effect lands when `MSR` finishes execute.

---

## Q8. Stack-Memory Design Aspects for Interrupts & Two Memory Layouts (10)

Exception handlers use stacks heavily; **each mode has its own banked stack pointer (`r13`)**. Stack design depends on:
- **Operating-system requirements** — each OS has its own stack needs.
- **Target hardware** — sets the physical size and position limits.

Two decisions:
- **Location** — where the stack begins. Most ARM systems use a **descending** stack (top at a high address).
- **Size** — depends on handler type: a **nested** handler needs **more** space (the stack grows with each nested interrupt) than a non-nested one.

A good design avoids **stack overflow** (stack growing past its allocation → memory corruption / instability). Two protective techniques:
1. Use **memory protection**.
2. Call a **stack-check function** at the start of each routine.

The **IRQ stack must be set up before interrupts are enabled** (in init code); its maximum size must be known since it is reserved at boot.

**Two layouts (Figure 9.6).** Both in a linear address space:
- **Layout A** — interrupt stack sits **just above the vector table** (below the code segment).
- **Layout B** — interrupt stack sits at the **top of memory**, above the user stack.

**Advantage of B over A:** an interrupt-stack overflow in B does **not corrupt the vector table**, so the system can detect and recover.

**Overleaf / TikZ code — two memory layouts:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  seg/.style={draw, thick, minimum width=4cm, minimum height=0.85cm, align=center}
]
% Layout A (bottom = low address)
\node[font=\bfseries] at (0,5.4){Layout A};
\node[seg, fill=red!12]   at (0,0){Vector table\ \footnotesize(0x0)};
\node[seg, fill=orange!15] at (0,0.85){Interrupt stack};
\node[seg, fill=blue!8]    at (0,1.7){Code\ \footnotesize(0x8000)};
\node[seg, fill=gray!12]   at (0,2.55){Heap $\uparrow$};
\node[seg, fill=green!12]  at (0,3.4){User stack $\downarrow$};
% Layout B
\node[font=\bfseries] at (6,5.4){Layout B};
\node[seg, fill=red!12]    at (6,0){Vector table\ \footnotesize(0x0)};
\node[seg, fill=blue!8]     at (6,0.85){Code};
\node[seg, fill=gray!12]    at (6,1.7){Heap $\uparrow$};
\node[seg, fill=green!12]   at (6,2.55){User stack $\downarrow$};
\node[seg, fill=orange!15]  at (6,3.4){Interrupt stack $\downarrow$};
\node[font=\scriptsize, anchor=west] at (-2,-1)
  {Address increases upward. In B, interrupt-stack overflow cannot reach the vector table.};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Each mode has a banked `r13`; stacks are descending; size depends on nesting.
2. Overflow must be guarded (memory protection / stack-check); IRQ stack is fixed at boot before interrupts are enabled.
3. Layout B places the interrupt stack at the top so overflow can't corrupt the vector table — its key advantage over A.

---

## Q9. Implementation of Supervisor, IRQ, and User Mode Stacks (10)

Each processor mode owns a **banked stack pointer `r13_<mode>`**, so the modes use **separate stacks**. All are typically **full-descending** (top at a high address).

- **User mode stack (`r13_usr`)** — the stack for normal application/`User`-mode code. Set up during initialisation. Because User mode is non-privileged, application code runs on this stack and exception handlers switch away from it.
- **IRQ mode stack (`r13_irq`)** — must be **set up in the initialisation code before interrupts are enabled**, with a known maximum size reserved at boot. On an IRQ the handler saves its context here. For a **non-nested** handler it can be small; for a **nested** handler it must be larger (grows per nested interrupt). In a **reentrant** handler the IRQ stack is largely unused — `r13_irq` instead points to a small (≈12-byte) structure used to **temporarily save registers on entry**.
- **Supervisor mode stack (`r13_svc`)** — the mode after reset and the usual OS-kernel mode. Reentrant/nested handlers **switch to SVC (or system) mode to service interrupts on the task's stack**, so a `BL` uses `r14_svc` (avoiding `r14_irq` corruption). When a nested handler does a context switch, it **flattens the IRQ stack**, transferring saved registers into a **stack frame** on the SVC/task stack.

> **[Beyond notes]** The notes describe these behaviours in pieces (banked `r13`, IRQ stack set up before enabling, reentrant servicing in SVC mode, the 12-byte structure, stack frames). The consolidation per-mode above follows directly from those statements; exact init code is implementation-specific.

**Step-by-step reasoning:**
1. Banking gives each mode its own `r13`, so User, IRQ, and SVC stacks are independent and descending.
2. The User stack runs application code; the IRQ stack (set before enabling interrupts) holds interrupt context.
3. Handlers switch to SVC/system mode to service on the task (SVC) stack, protecting `r14_irq` and using stack frames for context switches.

---

## Q10. Compare IRQ and FIQ Exceptions (10)

| Aspect | IRQ (Interrupt Request) | FIQ (Fast Interrupt Request) |
|---|---|---|
| Priority | 4 (second-highest interrupt) | 3 (highest-priority interrupt) |
| Trigger | Peripheral asserts `nIRQ` | Peripheral asserts `nFIQ` |
| Masks set on entry | I bit only (IRQ disabled, FIQ stays enabled) | Both I and F bits (IRQ **and** FIQ disabled) |
| Can be pre-empted | Yes — by FIQ (and Data Abort) | No external source until re-enabled |
| Vector offset | +0x18 | +0x1C |
| Banked registers | `r13_irq`, `r14_irq` | `r8–r14_fiq` (also `r8–r12`) |
| Register saving | Must save working registers | `r8–r12` banked → little/no saving needed |
| Typical use | General-purpose interrupts (e.g. timer) | Single source needing fast, low-latency response (e.g. DMA) |

**Justification.** FIQ is faster because (a) it sits at a higher priority and masks **both** interrupts on entry (so it isn't pre-empted), (b) it has **more banked registers** (`r8–r12`), so the handler can use them directly without push/pop overhead, and (c) its vector is **last (0x1C)**, allowing the handler to be placed directly at the vector. IRQ, with fewer banked registers and only IRQ masked, suits general-purpose use where some latency is acceptable.

**Step-by-step reasoning:**
1. Both are hardware interrupts, but FIQ outranks IRQ.
2. FIQ masks both interrupts and banks `r8–r12`, eliminating save/restore overhead → lower latency.
3. Hence FIQ = one fast source; IRQ = general interrupts.

---

# UNIT-2

## Q1. Non-Nested Interrupt Handler (10)

**Definition.** The **simplest** scheme: interrupts stay **disabled** until control returns to the interrupted task, so it services **one interrupt at a time**. Not suitable for complex systems with multiple differing-priority interrupts.

**Stages (Figure 9.8):**
1. **Disable interrupt(s)** — hardware disables further IRQs, sets the interrupt mode, copies `cpsr`→`spsr_<mode>`, sets `pc` to the vector entry (which jumps to the handler).
2. **Save context** — handler saves a subset of the current-mode non-banked registers.
3. **Interrupt handler** — identifies the external interrupt source and calls the right ISR.
4. **Interrupt service routine (ISR)** — services the source and **resets/clears** the interrupt.
5. **Restore context** — ISR returns to the handler, which restores the saved registers.
6. **Enable interrupts** — restore `spsr_<mode>`→`cpsr` and set `pc` to the next instruction (`lr − 4`), returning to the task.

**Overleaf / TikZ code — non-nested handler flow:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=5mm,
  step/.style={draw, thick, rounded corners, fill=blue!6, align=center, text width=6.6cm, minimum height=0.85cm},
  term/.style={draw, thick, rounded corners=9pt, fill=green!12, align=center, minimum width=3cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[term] (st){Interrupt raised};
\node[step, below=of st] (s1){1. Disable interrupts; pc = vector entry; spsr$_{mode}$ = cpsr};
\node[step, below=of s1] (s2){2. Save context};
\node[step, below=of s2] (s3){3. Interrupt handler (identify source)};
\node[step, below=of s3] (s4){4. Interrupt service routine (service \& reset source)};
\node[step, below=of s4] (s5){5. Restore context};
\node[step, below=of s5] (s6){6. Enable interrupts; cpsr = spsr$_{mode}$; pc = lr $-$ 4};
\node[term, below=of s6] (rt){Return to task};
\draw[ar](st)--(s1); \draw[ar](s1)--(s2); \draw[ar](s2)--(s3);
\draw[ar](s3)--(s4); \draw[ar](s4)--(s5); \draw[ar](s5)--(s6); \draw[ar](s6)--(rt);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Interrupts are off for the whole handler → only one interrupt at a time.
2. Flow is strictly linear: disable → save → identify → service → restore → enable.
3. Simple and deterministic, but unfit for multi-priority systems.

---

## Q2. Nested Interrupt Handler (10)

**Definition.** Allows **another interrupt to occur within the current handler** by **re-enabling interrupts before** the current one is fully serviced. This improves performance but adds complexity (subtle timing bugs).

**Goals & safeguards:** respond to interrupts quickly (don't make them wait, don't make them wait on the handler); don't delay regular synchronous code; use a **defensive coding style** — protect **context restoration** from interruption so the next interrupt cannot overflow the stack or corrupt registers.

**Behaviour.** Entry code is the **same as the non-nested handler**, but on exit it tests a **flag** (set by the ISR) indicating whether further processing is needed:
- **No further processing** → restore context and exit.
- **Further processing** → may re-enable interrupts and/or do a context switch:
  - **Re-enabling** requires switching out of IRQ mode to **SVC/system** mode (re-enabling in IRQ mode risks `r14_irq` corruption, e.g. after a `BL`).
  - **Context switch** requires **flattening the IRQ stack**: all registers on the IRQ stack are moved to the task's stack (typically SVC), and remaining registers saved into a **stack frame**.

**Overleaf / TikZ code — nested handler flow:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning, shapes.geometric}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=5mm,
  step/.style={draw, thick, rounded corners, fill=blue!6, align=center, text width=5.6cm, minimum height=0.8cm},
  dec/.style={draw, thick, diamond, aspect=2.2, fill=orange!15, align=center, inner sep=1pt, text width=2.6cm},
  term/.style={draw, thick, rounded corners=9pt, fill=green!12, align=center, minimum width=2.6cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[term] (st){Interrupt};
\node[step, below=of st] (s1){1. Disable interrupt (enter handler)};
\node[step, below=of s1] (s2){2. Save context};
\node[dec,  below=6mm of s2] (d3){3. Service complete?};
\node[step, below=8mm of d3] (s5){5--7. Prepare stack, switch mode, start frame};
\node[step, below=of s5] (s8){8. Enable interrupts};
\node[step, below=of s8] (s9){9. Finish frame \& 10. complete servicing};
\node[step, below=of s9] (s11){11. Restore context};
\node[term, below=of s11] (rt2){Return to task};
% complete branch (right)
\node[step, right=18mm of d3] (s4){4. Restore context};
\node[term, below=of s4] (rt1){Return to task};
\draw[ar](st)--(s1); \draw[ar](s1)--(s2); \draw[ar](s2)--(d3);
\draw[ar](d3)--node[left,font=\scriptsize]{no} (s5);
\draw[ar](s5)--(s8); \draw[ar](s8)--(s9); \draw[ar](s9)--(s11); \draw[ar](s11)--(rt2);
\draw[ar](d3)--node[above,font=\scriptsize]{yes} (s4);
\draw[ar](s4)--(rt1);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Re-enabling interrupts mid-handler lets a new interrupt nest inside the current one.
2. An ISR-set flag decides on exit whether to finish or to nest / context-switch.
3. Safety requires switching to SVC/system mode (protect `r14_irq`) and flattening the IRQ stack into a frame before a context switch.

---

## Q3. Differentiate Nested vs Non-Nested Interrupt Handler (10)

| Aspect | Non-Nested Handler | Nested Handler |
|---|---|---|
| Interrupts during handler | Disabled throughout | Re-enabled before servicing completes |
| Interrupts serviced at once | Only one | Multiple (one nested in another) |
| Complexity | Simplest | Higher (timing/stack/register hazards) |
| Stack usage | Small, fixed | Larger (grows with nesting depth) |
| Latency for new interrupts | High (must wait) | Lower (can pre-empt) |
| Priority handling | None | None by itself (no priority filtering) |
| Suitability | Simple systems | Real-time systems needing better response |

**Step-by-step reasoning:**
1. The core difference is *when* interrupts are re-enabled — never (non-nested) vs early (nested).
2. That makes nesting able to service multiple interrupts with lower latency, at the cost of complexity and a bigger stack.
3. Neither filters by priority — that needs a reentrant/prioritized handler.

---

## Q4. Reentrant Interrupt Handler (10)

**Definition.** Handles multiple interrupts **filtered by priority** (so higher-priority interrupts get lower latency — something a plain nested handler can't do). Interrupts are **re-enabled early**, reducing latency.

**Key requirements:**
- Interrupts must be serviced in **SVC, system, undefined, or abort mode** (not IRQ mode). If a `BL` runs with interrupts re-enabled in IRQ mode, the return address in `r14_irq` would be destroyed by the next interrupt — so swap to **SVC/system** mode so `BL` uses `r14_svc`.
- The interrupt source must be **disabled at the controller** (set a bit in the interrupt mask register) **before** re-enabling interrupts in the `cpsr` — otherwise the same interrupt regenerates immediately → **infinite interrupt / race condition**.
- The **IRQ stack is unused** (servicing happens on the task/SVC stack); instead `r13_irq` points to a **12-byte structure** to temporarily store registers on entry.
- Interrupts **must be prioritized**; otherwise latency degrades to that of a nested handler (a low-priority interrupt could pre-empt a higher-priority one).

**Overleaf / TikZ code — reentrant handler flow:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning, shapes.geometric}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=5mm,
  step/.style={draw, thick, rounded corners, fill=blue!6, align=center, text width=6cm, minimum height=0.8cm},
  dec/.style={draw, thick, diamond, aspect=2.2, fill=orange!15, align=center, inner sep=1pt, text width=2.6cm},
  term/.style={draw, thick, rounded corners=9pt, fill=green!12, align=center, minimum width=2.6cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[term] (st){Interrupt};
\node[step, below=of st] (s1){1. Disable interrupts (enter handler)};
\node[step, below=of s1] (s2){2. Save partial context};
\node[step, below=of s2] (s3){3. Change mode (to SVC/system)};
\node[step, below=of s3] (s4){4. Reserve stack space \& save complete context};
\node[step, below=of s4] (s5){5. Clear external interrupt (at controller)};
\node[step, below=of s5] (s6){6. Enable interrupts};
\node[dec,  below=6mm of s6] (d7){7. Service complete?};
\node[step, below=8mm of d7] (s9){9--10. Enable external interrupt; resave context};
\node[step, below=of s9] (s11){11. Continue servicing interrupt};
\node[step, below=of s11] (s12){12. Restore context};
\node[term, below=of s12] (rt2){Return to task};
\node[step, right=16mm of d7] (s8){8. Restore context};
\node[term, below=of s8] (rt1){Return to task};
\draw[ar](st)--(s1);\draw[ar](s1)--(s2);\draw[ar](s2)--(s3);\draw[ar](s3)--(s4);
\draw[ar](s4)--(s5);\draw[ar](s5)--(s6);\draw[ar](s6)--(d7);
\draw[ar](d7)--node[left,font=\scriptsize]{no}(s9);
\draw[ar](s9)--(s11);\draw[ar](s11)--(s12);\draw[ar](s12)--(rt2);
\draw[ar](d7)--node[above,font=\scriptsize]{yes}(s8);\draw[ar](s8)--(rt1);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Re-enable interrupts early but only after switching mode (protect `r14`) and masking the source at the controller (avoid re-trigger).
2. Service on the task/SVC stack; `r13_irq` just holds a small save structure.
3. Priority filtering is essential, else it is no better than a nested handler.

---

## Q5. Differentiate Nested vs Reentrant Interrupt Handler (10)

| Aspect | Nested Handler | Reentrant Handler |
|---|---|---|
| Priority filtering | None — first-come, first-served | Yes — interrupts filtered by priority |
| Higher-priority latency | Not guaranteed lower | Lower (high priority pre-empts low) |
| Servicing mode | Largely in IRQ mode (switches for context switch) | Must service in SVC/system/undef/abort mode |
| Source masking | Re-enables after source serviced | Masks source at controller, then re-enables early |
| `r14` corruption risk | Avoided by switching for context switch | Avoided by switching mode so `BL` uses `r14_svc` |
| IRQ stack | Used to hold context | Largely unused; `r13_irq` → 12-byte save area |
| Risk if mis-designed | Timing/stack hazards | Low priority pre-empting high; infinite interrupt if source not masked |

**Step-by-step reasoning:**
1. Both allow interrupts within interrupts, but only the reentrant handler **prioritizes** them.
2. The reentrant handler re-enables interrupts earlier and services in a non-IRQ mode, masking the source first.
3. Without prioritization the reentrant handler degenerates to a nested handler.

---

## Q6. Prioritized Simple Interrupt Handler (10)

**Definition.** Unlike non-nested/nested handlers (first-come, first-served), a **prioritized** handler associates a **priority level** with each interrupt source, so a higher-priority interrupt takes precedence. Prioritization can be done in:
- **Hardware** — controller supplies the current highest-priority interrupt (simpler handler, but more startup/initialisation code to build the priority tables).
- **Software** — needs an external interrupt controller providing at least: set/unset masks, and read interrupt status/source.

This **simple** version is based on a reentrant handler. Stages (Figure 9.11):
1. Disable interrupts.
2. Save minimum context.
3. Get external interrupt status (from the controller).
4. Identify interrupt priority, **mask off lower-priority interrupts**, and enable IRQs.
5. Jump to the service routine.
6. Create a (full) context.
7. Service the interrupt.
8. Switch internal interrupts back on, then the external interrupt.
9. Restore context and return.

**Overleaf / TikZ code — prioritized simple handler flow:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=5mm,
  step/.style={draw, thick, rounded corners, fill=blue!6, align=center, text width=6.8cm, minimum height=0.8cm},
  term/.style={draw, thick, rounded corners=9pt, fill=green!12, align=center, minimum width=2.6cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[term] (st){Interrupt};
\node[step, below=of st] (s1){1. Disable interrupts};
\node[step, below=of s1] (s2){2. Save minimum context};
\node[step, below=of s2] (s3){3. Get external interrupt status};
\node[step, below=of s3] (s4){4. Identify priority; mask lower-priority interrupts; enable IRQs};
\node[step, below=of s4] (s5){5. Jump to service routine};
\node[step, below=of s5] (s6){6. Create a context};
\node[step, below=of s6] (s7){7. Service interrupt};
\node[step, below=of s7] (s8){8. Switch on internal then external interrupts};
\node[step, below=of s8] (s9){9. Restore context};
\node[term, below=of s9] (rt){Return to task};
\draw[ar](st)--(s1);\draw[ar](s1)--(s2);\draw[ar](s2)--(s3);\draw[ar](s3)--(s4);
\draw[ar](s4)--(s5);\draw[ar](s5)--(s6);\draw[ar](s6)--(s7);\draw[ar](s7)--(s8);
\draw[ar](s8)--(s9);\draw[ar](s9)--(rt);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A priority level per source decides servicing order (HW or SW prioritization).
2. The handler reads status, identifies priority, masks lower-priority sources, then re-enables IRQs.
3. Built on a reentrant handler so higher-priority interrupts can still pre-empt.

---

## Q7. Prioritized Standard Interrupt Handler (10)

**Definition.** An optimisation of the prioritized simple handler. The simple version **tests every interrupt** to find the highest priority — inefficient but **deterministic** (each takes the same time to identify). The standard handler instead **jumps early** as soon as the highest-priority interrupt is identified — so identification is more involved but faster on average.

**Working:**
- Compare the interrupt source from **highest to lowest** priority; the **first match** sets the incoming priority level (each source has a preset level). On a match, branch to the routine that **masks off lower-priority interrupts**.
- That routine computes the level using base address in `r11` and `lr` (`r14`): after a `SUB`, `r11` holds 4/12/20/28 (= level × 8 + 4). Dividing `r11` by 8 and adding the `priority_table` address, then `LDRB`, gives the priority number (0–3).
- The mask is found by shifting left by 2 and adding to `r10` (address of `priority_mask`). The controller base in `r14_irq` gives the **IRQ Enable** register in `r12`; a binary **AND** of mask (`r10`) and `r12` is stored into the **IRQ Enable Clear** register to clear lower-priority interrupts. It is then safe to clear the `i` bit in the `cpsr`.
- Finally, jump to the ISR: shift `r11` left by 2 and add to `pc`, loading the ISR address into `pc`. The **jump table follows** the `LDR pc` instruction, with a `NOP` between them because `pc` points **two instructions (8 bytes) ahead**.
- *Note:* the **priority mask table is in interrupt-bit order**; the **priority table is in priority order**.

**Overleaf / TikZ code — prioritized standard handler (priority-decision part):**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning, shapes.geometric}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small, node distance=5mm,
  step/.style={draw, thick, rounded corners, fill=blue!6, align=center, text width=5.4cm, minimum height=0.8cm},
  dec/.style={draw, thick, diamond, aspect=2.4, fill=orange!15, align=center, inner sep=1pt, text width=2.8cm},
  term/.style={draw, thick, rounded corners=9pt, fill=green!12, align=center, minimum width=2.6cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[step] (s3){3. Obtain external interrupt status};
\node[dec, below=6mm of s3] (d1){Priority 1 interrupt?};
\node[dec, below=8mm of d1] (d2){Priority 2 interrupt?};
\node[step, right=20mm of d1] (s5){5. Disable lower-priority interrupts};
\node[step, below=of s5] (s6){6. Enable external interrupts};
\node[step, below=of s6] (s7){7. Enable internal interrupts};
\node[step, below=of s7] (s8){8. Service interrupt};
\node[step, below=of s8] (s9){9. Restore context};
\node[term, below=of s9] (rt){Return to task};
\draw[ar](s3)--(d1);
\draw[ar](d1)--node[left,font=\scriptsize]{no}(d2);
\draw[ar](d1)--node[above,font=\scriptsize]{yes}(s5);
\draw[ar](d2)-|node[near start,below,font=\scriptsize]{yes/no $\to$ jump}(s5);
\draw[ar](s5)--(s6);\draw[ar](s6)--(s7);\draw[ar](s7)--(s8);\draw[ar](s8)--(s9);\draw[ar](s9)--(rt);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Test priorities high→low and **jump on the first match** (faster than scanning all).
2. Compute the priority number, derive the mask, AND it into IRQ-Enable-Clear to mask lower priorities, then clear the `i` bit.
3. Jump via a table after the `LDR pc` (with a `NOP` for the pipeline) to the matching ISR.

---

## Q8. Prioritized Direct Interrupt Handler (10)

**Definition.** A variant of the prioritized standard handler with two differences:
1. The code that **masks out lower-priority interrupts is moved out of the handler into each ISR** (each ISR masks the fixed set of lower priorities for its level, since the level is already known).
2. The handler **jumps directly to the appropriate ISR**.

This keeps the **handler simple** (masking is done in the ISR), at the cost of **small code duplication** (each ISR does the same masking task).

**Working:**
- Establish the priority by checking **highest first down to lowest**. On identification, load `pc` with the ISR address: the indirect address is at `isr_table + (priority << 2)` (i.e. level × 4). Alternatively use a conditional `BNE`.
- The **`isr_table` is ordered highest-priority first.**
- Inside each ISR (e.g. `service_timer1`): copy the controller base into `r14_irq`; read **IRQEnable** into `r12`; load the priority-mask-table address into `r10`; shift `r11` left by 2 (offset 0/4/8/12) and add to the mask address to load the mask into `r10`; **AND** `r10` (ISR mask) with `r12` (current mask) and write to **IRQ Enable Clear**; then clear the `i` bit in the `cpsr` to re-enable IRQs.
- The handler keeps servicing unless a **higher-priority** interrupt occurs, which then takes precedence.

**Step-by-step reasoning:**
1. Identify priority high→low and jump straight to the ISR via `isr_table`.
2. Each ISR masks its own lower-priority interrupts (fixed mask) then re-enables IRQs.
3. Simpler handler, but the same masking code is duplicated across ISRs.

---

## Q9. Prioritized Grouped Interrupt Handler (10)

**Definition.** Designed to handle a **large set of interrupts** by **grouping** sources into subsets, each subset given a **group priority level**. The designer must choose the subsets carefully because the grouping determines system characteristics. Grouping **reduces handler complexity** (no need to scan every interrupt) and, if well designed, **dramatically improves response times**.

**Working:**
- `GROUP_x` defines assign sources to a priority level via binary **OR** of bit patterns; `GMASK_x` defines the group masks; `MASK_x` connects each `GMASK_x` to a specific source for the priority mask table.
- After saving context, the handler loads the **IRQ status register** (offset from the controller base).
- It finds the source's **group** using a binary **AND** on the source (the `S` suffix updates the flags). `r11` then holds the highest-priority group (0 or 1); other sources are masked with **AND `0xf`**.
- The **least-significant-bit table** address is loaded into `r11`, and a byte is read using `r10` (0–3) to get the LSB position; the handler then branches to a routine.
- `disable_lower_priority` first checks for a **spurious interrupt** (no longer present) → if so call `unknown_condition`. Otherwise it loads **IRQEnable** into `r12`, derives the mask (priority-mask-table address + (`r11` << 2) → 0/4/8/12) into `r10`, **AND**s `r10` with `r12`, and writes to **IRQEnableClear** to disable the lower-priority group; then clears the `i` bit to enable IRQs.
- Finally it jumps to the ISR: shift `r11` left by 2, add to `pc`. The **jump table follows the `LDR`**, with a `NOP` for the pipeline.

**Step-by-step reasoning:**
1. Sources are bundled into priority **groups**, so the handler resolves a group, not every source.
2. It identifies the group (AND), finds the LSB/priority, masks the lower-priority group at the controller, and enables IRQs.
3. It then jumps via the table to the ISR — fewer comparisons → faster response for large interrupt sets.

---

## Q10. Differentiate Prioritized Standard vs Prioritized Direct Interrupt Handler (10)

| Aspect | Prioritized Standard | Prioritized Direct |
|---|---|---|
| Where lower-priority masking is done | Inside the **handler** | Inside each **ISR** |
| Jump to ISR | Handler masks first, then jumps via jump table | Handler **jumps directly** to the ISR (via `isr_table`) |
| Handler complexity | More involved (does masking + jump) | Simpler (masking offloaded to ISRs) |
| Code duplication | Minimal (single masking routine) | Some duplication (each ISR masks lower priorities) |
| Priority identification | High→low, jump early on first match | High→low, load `pc` from `isr_table` on match |
| Best suited | When centralised masking is preferred | When fast, direct dispatch to ISR is preferred |

**Step-by-step reasoning:**
1. Both prioritize and jump early; the difference is **where the lower-priority masking lives**.
2. Standard keeps masking centralised in the handler; direct pushes it into each ISR and dispatches straight to it.
3. Trade-off: standard avoids duplication; direct gives a simpler handler and faster dispatch.

---

*End of Module-4 Focused Answers*

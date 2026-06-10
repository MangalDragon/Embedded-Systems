# Embedded Systems (ECE23601) — Module-5 Focused Answers

> Embedded / Real-Time Operating System Concepts. Answers based on the Module-5 notes provided; anything added beyond them is flagged **[Beyond notes]**. Each diagram is a complete, standalone Overleaf/TikZ document — paste a block into Overleaf and it compiles as-is.

---

# UNIT-1

## Q1. Operating System Architecture (10)

**Definition.** The operating system acts as a **bridge between user applications/tasks and the underlying system resources** through a set of system functionalities and services. It manages the system resources and makes them available to applications on a need basis.

**Primary functions:**
- Make the system convenient to use.
- Organise and manage system resources efficiently and correctly.

**Components.** The OS sits between the user applications (top) and the hardware (bottom):
- **Application Programming Interface (API)** — the interface through which user applications request OS services.
- **Kernel services** — the core, comprising: **Memory Management**, **Process Management**, **Time Management**, **File System Management**, and **I/O System Management**.
- **Device Driver Interface** — the interface between the kernel's I/O management and the underlying hardware.

**Overleaf / TikZ code — OS architecture:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  band/.style={draw, very thick, rounded corners, align=center, minimum width=11cm, minimum height=1cm},
  ksvc/.style={draw, thick, rounded corners, fill=blue!8, align=center, minimum width=2cm, minimum height=1cm},
  ar/.style={{Latex[length=2.5mm]}-{Latex[length=2.5mm]}, thick}
]
\node[band, fill=green!12] (ua) at (0,4) {User Applications / Tasks};
\node[band, fill=blue!16, minimum height=2.4cm] (kern) at (0,1.7) {};
\node[font=\bfseries] at (-4.6,2.7) {Kernel Services};
\node[ksvc] (mm) at (-4,1.3){Memory\\Mgmt};
\node[ksvc] (pm) at (-2,1.3){Process\\Mgmt};
\node[ksvc] (tm) at ( 0,1.3){Time\\Mgmt};
\node[ksvc] (fs) at ( 2,1.3){File System\\Mgmt};
\node[ksvc] (io) at ( 4,1.3){I/O System\\Mgmt};
\node[band, fill=gray!15] (hw) at (0,-0.6) {Underlying Hardware};
\draw[ar] (ua) -- node[right]{API} (kern.north);
\draw[ar] (kern.south) -- node[right]{Device Driver Interface} (hw);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. The OS is a layer between applications and hardware.
2. Applications reach kernel services via the API; the kernel manages memory, processes, time, files, and I/O.
3. The kernel reaches hardware through the device-driver interface.

---

## Q2. General Purpose and Real-Time Operating Systems (10)

OSs are classified by kernel type/services, the computing system they serve, and their responsiveness.

### General Purpose Operating System (GPOS)
- Deployed in **general computing systems**.
- Its kernel is **generalised**, containing all services needed for generic applications.
- Often **non-deterministic** — services can inject random delays and cause slow responsiveness at unexpected times.
- Used where **deterministic behaviour is not important** (e.g. a PC/desktop).
- **Examples:** Windows XP, MS-DOS.

### Real-Time Operating System (RTOS)
- **'Real-Time' implies deterministic timing behaviour** — OS services consume only **known and expected** amounts of time, regardless of the number of services.
- Implements **policies and rules** for time-critical allocation of resources; decides **which application runs in which order** and how much time each gets.
- **Predictable performance** is the hallmark, achieved by consistent application of policies (which guide design) and rules (which implement policies and resolve conflicts).
- **Examples:** Windows CE, QNX, VxWorks, MicroC/OS-II.

**Step-by-step reasoning:**
1. GPOS = generalised kernel, non-deterministic, for non-time-critical systems.
2. RTOS = deterministic timing, policy/rule-driven, for time-critical systems.
3. The deciding factor is whether predictable (deterministic) timing is required.

---

## Q3. Process and the Structure of a Process (10)

**Definition.** A **process** is a program, or part of it, in execution — an *instance* of a program in execution. (The terms **Task**, **Job**, and **Process** are used interchangeably.) Multiple instances of the same program can run simultaneously. A process is **sequential** in execution and needs system resources: **CPU** (to execute), **Memory** (for code and variables), and **I/O devices** (for information exchange).

**Structure of a process.** Concurrent execution (pseudo-parallelism) is achieved by sharing the CPU among processes. A process **mimics a processor** and holds:
- A set of **registers** (working registers, status registers).
- A **Program Counter (PC)** pointing to the next instruction.
- A **stack** for local variables.
- The **code** corresponding to the process.

Because it inherits the CPU's properties, a process is a **virtual processor** awaiting its turn; when scheduled, its registers and PC are mapped onto the physical CPU.

**Memory organisation** — the process memory has three regions:

| Region | Holds | Growth |
|---|---|---|
| **Stack Memory** | Temporary data / local variables | Grows downward ↓ |
| **Data Memory** | Global data | Grows upward ↑ |
| **Code Memory** | Program instructions | Fixed |

**Overleaf / TikZ code — process structure and memory organisation:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  reg/.style={draw, thick, fill=blue!8, align=center, minimum width=3.4cm, minimum height=0.7cm},
  mem/.style={draw, thick, align=center, minimum width=3.6cm, minimum height=1.1cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
% Process internals
\node[font=\bfseries] at (-3.2,3.4){Process};
\node[reg] (pc) at (-3.2,2.7){Program Counter (PC)};
\node[reg] (sr) at (-3.2,1.9){Status Registers};
\node[reg] (wr) at (-3.2,1.1){Working Registers};
\node[reg] (sp) at (-3.2,0.3){Stack (Stack Pointer)};
\node[reg, fill=gray!10] (cm) at (-3.2,-0.7){Code Memory of process};
\draw[ar] (sp) -- (cm);
% Memory organisation
\node[font=\bfseries] at (3.2,3.4){Memory organisation};
\node[mem, fill=green!10] (st) at (3.2,2.6){Stack Memory\\\footnotesize (grows down $\downarrow$)};
\node[mem, fill=orange!12] (da) at (3.2,1.4){Data Memory\\\footnotesize (grows up $\uparrow$)};
\node[mem, fill=gray!12]  (co) at (3.2,0.2){Code Memory};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A process is a program in execution that needs CPU, memory, and I/O.
2. It mimics a processor: registers + PC + stack + code → a virtual processor mapped to the CPU when scheduled.
3. Its memory splits into stack (local, down), data (global, up), and code (fixed).

---

## Q4. Process States and State Transition (10)

A process passes through several **states** from creation to termination — the **Process Life Cycle**. The change from one state to another is a **State Transition**.

| State | Description |
|---|---|
| **Created** | OS recognises the process but no resources are allocated yet. |
| **Ready** | Process is in memory, awaiting CPU time; placed in the **Ready list** queue. |
| **Running** | The process's instructions are being executed on the CPU. |
| **Blocked / Wait** | Running process is temporarily suspended (e.g. waiting for I/O or a shared resource). |
| **Completed** | Process has finished execution. |

**Transitions:** Created → Ready (admitted to memory); Ready → Running (scheduled); Running → Ready (interrupted/preempted); Running → Blocked (waits for I/O/resource); Blocked → Ready (I/O done / resource acquired); Running → Completed (execution finished).

**Overleaf / TikZ code — process state transition:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  st/.style={draw, very thick, rounded corners, fill=blue!8, align=center, minimum width=2.2cm, minimum height=1cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}, font=\small
]
\node[st] (cr) at (0,3)    {Created};
\node[st] (rd) at (0,0)    {Ready};
\node[st] (ru) at (5,0)    {Running};
\node[st] (bl) at (5,-3)   {Blocked /\\ Wait};
\node[st] (co) at (10,0)   {Completed};
\draw[ar] (cr) -- node[right,font=\scriptsize]{incepted into memory} (rd);
\draw[ar] (rd) -- node[above,font=\scriptsize]{scheduled} (ru);
\draw[ar] (ru) to[bend right=20] node[below,font=\scriptsize]{interrupted / preempted} (rd);
\draw[ar] (ru) -- node[right,font=\scriptsize]{wait I/O / resource} (bl);
\draw[ar] (bl) -- node[below,font=\scriptsize]{I/O done / resource acquired} (rd);
\draw[ar] (ru) -- node[above,font=\scriptsize]{execution complete} (co);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A process moves Created → Ready → Running, then to Completed when done.
2. From Running it can be preempted back to Ready, or blocked waiting for I/O/resources.
3. A blocked process returns to Ready (not directly to Running) once its wait is satisfied.

---

## Q5. Concept of Multithreading with Advantages (10)

**Thread.** A thread is a **single sequential flow of control within a process** — the primitive that executes code; also called a **light-weight process**. A process can have many threads; they **share the same address space** (code, data, heap) but each keeps its **own status (registers), PC, and stack**.

**Concept of multithreading.** A task may contain sub-operations (get I/O input, compute, update I/O). If run in strict sequence, the CPU is used inefficiently — e.g. while waiting for user input, the CPU enters a wait state. If the task is split into **threads** for different sub-functions, then when the I/O thread blocks, **other threads that don't need that I/O event can be switched in**. This gives more speedy execution and efficient CPU use.

If a process is split into threads, there is a **main thread**, and the rest are created within it (e.g. via `CreateThread`). **Code and data memory are shared** across all threads; each thread has its **own stack and registers**.

**Advantages:**
- **Better memory utilisation** — threads share the data address space, reducing inter-thread communication complexity (variables can be shared).
- **Speedy execution** — when one thread waits, the CPU runs other threads.
- **Efficient CPU utilisation** — the CPU stays engaged at all times.

**Overleaf / TikZ code — process with multiple threads:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  outer/.style={draw, very thick, rounded corners, fill=blue!5, minimum width=9.5cm, minimum height=4.5cm},
  shared/.style={draw, thick, fill=orange!12, align=center, minimum width=8.8cm, minimum height=0.8cm},
  th/.style={draw, thick, rounded corners, fill=green!10, align=center, minimum width=2.5cm, minimum height=1.6cm}
]
\node[outer] (p) at (0,0){};
\node[font=\bfseries, anchor=north west] at (-4.6,2.1){Process};
\node[shared] at (0,1.4){Code Memory (shared)};
\node[shared] at (0,0.5){Data Memory (shared)};
\node[th] (t1) at (-3,-1.1){Thread 1 (main)\\\footnotesize Stack + Registers};
\node[th] (t2) at (0,-1.1) {Thread 2\\\footnotesize Stack + Registers};
\node[th] (t3) at (3,-1.1) {Thread 3\\\footnotesize Stack + Registers};
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Threads are lightweight flows of control sharing a process's code/data but with private stack/registers.
2. Splitting a task into threads lets the CPU switch to a ready thread when another blocks on I/O.
3. Result: shared memory, faster execution, and fuller CPU utilisation.

---

## Q6. Types of Threads and Thread Binding Models (10)

### Types of threads

**User Level Threads**
- Have **no kernel/OS support**; exist solely within the running process.
- The OS treats the whole process as a **single thread** and does not switch among its threads — the **process schedules them itself**.
- **Non-preemptive** at thread level; switching happens only when the running thread **voluntarily blocks**.
- No OS calls in context switching → **very fast**.

**Kernel Level Threads**
- Treated by the OS as **separate, individual** threads.
- The OS can **interrupt and switch** between kernel threads per scheduling policy → **pre-emptive**.
- Involve **kernel overhead and system calls** for switching, but give clear abstraction and let threads use system calls independently.

### Thread binding models

| Model | Mapping | Examples |
|---|---|---|
| **Many-to-One** | Many user threads → one kernel thread (switch only when the running user thread blocks/yields) | Solaris Green Threads, GNU Portable Threads |
| **One-to-One** | Each user thread → its own kernel thread | Windows XP/NT/2000, Linux threads |
| **Many-to-Many** | Many user threads → many kernel threads | Windows NT/2000 with ThreadFibre package |

**Step-by-step reasoning:**
1. User-level threads: no OS support, non-preemptive, fast; kernel-level threads: OS-managed, preemptive, with overhead.
2. Binding models map user threads onto kernel threads in three ratios.
3. Many-to-One = one kernel thread; One-to-One = paired; Many-to-Many = flexible pool.

---

## Q7. Difference between Thread and Process (10)

| Thread | Process |
|---|---|
| A single unit of execution; part of a process. | A program in execution; contains one or more threads. |
| No own data/heap memory — **shares** data and heap with other threads of the same process. | Has its **own** code, data, and stack memory. |
| Cannot live independently; lives within a process. | Contains at least one thread. |
| Multiple threads per process; the main thread calls `main` and occupies the start of the process stack. | — |
| Shares code, data, and heap; keeps a **separate stack** area. | — |
| **Very inexpensive** to create. | **Very expensive** to create (many OS overheads). |
| Context switching is **inexpensive and fast**. | Context switching is complex and **slower**. |
| If a thread expires, its stack is reclaimed by the process. | If a process dies, the OS reclaims its resources and all its threads die. |

**Step-by-step reasoning:**
1. A process owns memory; a thread shares the process's memory but keeps a private stack.
2. Threads are cheap to create/switch; processes are expensive.
3. Threads live inside a process and die with it.

---

## Q8. Types of Multitasking (10)

**Co-operative Multitasking**
- The **most primitive** form. A task runs only when the currently executing task **voluntarily relinquishes** the CPU.
- A task can hold the CPU as long as it wants; if it is non-cooperative, others may wait a long time.

**Preemptive Multitasking**
- Ensures **every task gets a chance** to execute. The running task is **preempted** to give others a turn.
- Preemption is based on **time slots** or **task/process priority**.

**Non-preemptive Multitasking**
- The task given the CPU runs until it **terminates** (Completed) or **enters the Blocked/Wait** state (for I/O, resource, or event).

**Difference in the Blocked/Wait behaviour:**
- **Co-operative:** the task **need not** relinquish the CPU when it enters Blocked/Wait.
- **Non-preemptive:** the task **relinquishes** the CPU when it waits for I/O/resource/event.

**Step-by-step reasoning:**
1. Co-operative: task keeps CPU until it chooses to give it up.
2. Preemptive: scheduler forcibly preempts by time slot or priority.
3. Non-preemptive: task runs until it completes or blocks — and unlike co-operative, it gives up the CPU when it blocks.

---

## Q9. Task Scheduling and Selection Criteria (10)

**Task scheduling.** Determining **which task/process is to be executed at a given point of time** is task scheduling. **Scheduling policies** are the guidelines for this; they are implemented as an **algorithm** run by the kernel as a service — the **Scheduler**. Scheduling is broadly **non-preemptive** (task runs until it terminates or waits) or **preemptive** (running task is stopped and another is picked from the Ready queue).

**Criteria for selecting a scheduling algorithm:**

| Criterion | Meaning | Goal |
|---|---|---|
| **CPU Utilisation** | % of CPU kept busy | High |
| **Throughput** | Processes completed per unit time | High |
| **Turnaround Time (TAT)** | Total time to complete a process (memory wait + ready queue + I/O + execution) | Minimal |
| **Waiting Time** | Time spent in the Ready queue | Minimal |
| **Response Time** | Time from submission to first response | Minimal |

> A good algorithm has **high CPU utilisation, minimum TAT, maximum throughput, and least response time**.

**Step-by-step reasoning:**
1. Scheduling decides which Ready task runs next; the scheduler implements the policy.
2. It is preemptive or non-preemptive.
3. A good choice maximises CPU use and throughput while minimising TAT, waiting, and response time.

---

## Q10. Multithreaded Application using `pthread_create()` and `pthread_join()` (10)

> **[Beyond notes]** The notes give the POSIX primitive *signatures* (`pthread_create`, `pthread_join`) but not this full program; the code below is standard POSIX usage built from those primitives.

The main thread prints *"Hello I'm in main thread"* 5 times, and a new thread prints *"Hello I'm in new thread"* 5 times. `pthread_create()` starts the new thread; `pthread_join()` makes `main` wait for it to finish.

```c
#include <stdio.h>
#include <pthread.h>

/* Function executed by the new thread */
void *new_thread_function(void *arg)
{
    for (int i = 0; i < 5; i++)
        printf("Hello I'm in new thread\n");
    return NULL;
}

int main(void)
{
    pthread_t new_thread_ID;
    int i;

    /* Create the new thread to run new_thread_function */
    pthread_create(&new_thread_ID, NULL, new_thread_function, NULL);

    /* Main thread does its own printing */
    for (i = 0; i < 5; i++)
        printf("Hello I'm in main thread\n");

    /* Wait for the new thread to complete before exiting */
    pthread_join(new_thread_ID, NULL);

    return 0;
}
```

**How it maps to the primitives (from the notes):**
- `pthread_create(&new_thread_ID, NULL, new_thread_function, NULL)` — creates a thread running `new_thread_function`; `new_thread_ID` (type `pthread_t`) receives the handle/TCB; attribute `NULL` = default; last `NULL` = no arguments.
- `pthread_join(new_thread_ID, NULL)` — blocks `main` until the new thread completes; second argument `NULL` since we don't collect a status. A return value of **zero** from POSIX calls indicates success.

**Step-by-step reasoning:**
1. `main` is the main thread; `pthread_create` spawns a second thread for `new_thread_function`.
2. Both loops print their message 5 times — running concurrently (output order may interleave).
3. `pthread_join` makes `main` wait so the program doesn't exit before the new thread finishes.

---

# UNIT-2

## Q1. Preemptive SJF / SRT — P1=10, P2=5, P3=7 @0; P4=2 @2 ms (10)

**Rule.** Preemptive SJF (Shortest Remaining Time, SRT): always run the process with the **shortest remaining time**; preempt the running process if a newly arrived one has a smaller remaining time.

**Ready-queue progression:**

| Time | Action |
|---|---|
| 0 | P1=10, P2=5, P3=7 → **P2** scheduled (shortest) |
| 2 | P4=2 arrives; P2 has 3 left → P4(2) < 3 → P2 preempted, **P4** scheduled |
| 4 | P4 done; remaining P1=10, P2=3, P3=7 → **P2** scheduled |
| 7 | P2 done → **P3** scheduled |
| 14 | P3 done → **P1** scheduled |
| 24 | P1 done |

**Execution sequence:**

```
| P2 | P4 |  P2  |       P3       |          P1           |
0    2    4      7               14                       24  (ms)
```

**Waiting time** = (start − arrival) summed over runs, excluding execution:

| Process | Waiting time | TAT = wait + burst |
|---|---|---|
| P1 | 14 | 14 + 10 = 24 |
| P2 | (0) + (4−2) = 2 | 2 + 5 = 7 |
| P3 | 7 | 7 + 7 = 14 |
| P4 | 0 | 0 + 2 = 2 |

$$\text{Average Waiting Time} = \frac{14 + 2 + 7 + 0}{4} = \frac{23}{4} = 5.75\ \text{ms}$$

$$\text{Average TAT} = \frac{24 + 7 + 14 + 2}{4} = \frac{47}{4} = 11.75\ \text{ms}$$

**Step-by-step reasoning:**
1. P2 (shortest) runs first; at t=2 P4 arrives with 2 ms < P2's 3 ms remaining → preempt.
2. After P4, the shortest remaining (P2=3) runs, then P3, then P1.
3. Waiting = time in Ready not executing; TAT = waiting + burst.

---

## Q2. Round Robin (time slice = 2 ms) — P1=6, P2=4, P3=2 ms (10)

**Rule.** Each process runs for one time slice (2 ms) in FCFS circular order; if unfinished it goes to the back of the queue.

**Progression:**

| Time | Running | Queue after |
|---|---|---|
| 0–2 | P1 (4 left) | P2, P3, P1 |
| 2–4 | P2 (2 left) | P3, P1, P2 |
| 4–6 | P3 → **done** | P1, P2 |
| 6–8 | P1 (2 left) | P2, P1 |
| 8–10 | P2 → **done** | P1 |
| 10–12 | P1 → **done** | — |

**Execution sequence:**

```
| P1 | P2 | P3 | P1 | P2 | P1 |
0    2    4    6    8   10   12  (ms)
```

**Waiting times** (sum of gaps the process spends waiting):

| Process | Waiting time | TAT = wait + burst |
|---|---|---|
| P1 | 0 + (6−2) + (10−8) = 6 | 6 + 6 = 12 |
| P2 | (2−0) + (8−4) = 6 | 6 + 4 = 10 |
| P3 | (4−0) = 4 | 4 + 2 = 6 |

$$\text{Average Waiting Time} = \frac{6 + 6 + 4}{3} = \frac{16}{3} = 5.33\ \text{ms}$$

$$\text{Average TAT} = \frac{12 + 10 + 6}{3} = \frac{28}{3} = 9.33\ \text{ms}$$

**Step-by-step reasoning:**
1. Processes run 2 ms each in circular order P1→P2→P3→P1…
2. P3 finishes at 6, P2 at 10, P1 at 12.
3. Waiting = total time in Ready not running; TAT = waiting + burst.

---

## Q3. Priority Scheduling — P1=10(p1), P2=5(p3), P3=7(p2) @0 (10)

**Rule.** Lower priority number = higher priority (0 highest). All arrive at t=0, so the scheduler simply runs them in priority order; no preemption occurs (no later arrivals). Order by priority: **P1 (1) → P3 (2) → P2 (3)**.

**Execution sequence:**

```
|        P1        |      P3      |    P2   |
0                 10             17        22  (ms)
```

| Process | Waiting time | TAT = wait + burst |
|---|---|---|
| P1 (pri 1) | 0 | 0 + 10 = 10 |
| P3 (pri 2) | 10 | 10 + 7 = 17 |
| P2 (pri 3) | 17 | 17 + 5 = 22 |

$$\text{Average Waiting Time} = \frac{0 + 10 + 17}{3} = \frac{27}{3} = 9\ \text{ms}$$

$$\text{Average TAT} = \frac{10 + 17 + 22}{3} = \frac{49}{3} = 16.33\ \text{ms}$$

**Step-by-step reasoning:**
1. All arrive together, so run strictly in priority order: P1, then P3, then P2.
2. Each waits for the higher-priority processes ahead of it.
3. TAT = waiting + burst.

---

## Q4. Shared Memory and Pipes in Task Communication (10)

**Shared Memory.** Processes share a region of memory to communicate: the sender **writes** information to the shared area, and other processes **read** it. Mechanisms used by kernels to implement shared memory include **Pipes** and **Memory-Mapped Objects**.

```text
Process 1  <-->  [ Shared Memory Area ]  <-->  Process 2
```

**Pipes.** A **pipe** is a section of shared memory used for communication, following a **client–server architecture**:
- The process that **creates** the pipe = **pipe server**; the process that **connects** to it = **pipe client**.
- A pipe is a conduit with two ends; it can be **unidirectional** (one end writes, the other reads) or **bidirectional** (read and write at one end).

**Types of pipes (Windows):**

| Type | Description |
|---|---|
| **Anonymous Pipes** | Unnamed, **unidirectional**; transfer data between two processes. |
| **Named Pipes** | Named, unidirectional or bidirectional; the pipe server creates it and clients connect; allow point-to-point communication on the same machine or across a network. |

**Overleaf / TikZ code — shared memory and pipe:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  p/.style={draw, very thick, rounded corners, fill=blue!8, align=center, minimum width=2.4cm, minimum height=1.1cm},
  sh/.style={draw, very thick, rounded corners, fill=orange!15, align=center, minimum width=3cm, minimum height=1.1cm},
  ar/.style={{Latex[length=2.5mm]}-{Latex[length=2.5mm]}, thick},
  arr/.style={-{Latex[length=2.5mm]}, thick}
]
% Shared memory
\node[p] (a1) at (0,1.6){Process 1};
\node[sh](sm) at (3.5,1.6){Shared Memory};
\node[p] (a2) at (7,1.6){Process 2};
\draw[ar] (a1)--(sm); \draw[ar] (sm)--(a2);
% Pipe
\node[p] (b1) at (0,-1){Process 1\\\footnotesize write};
\node[sh](pp) at (3.5,-1){Pipe\\\footnotesize (named/anonymous)};
\node[p] (b2) at (7,-1){Process 2\\\footnotesize read};
\draw[arr] (b1)--(pp); \draw[arr] (pp)--(b2);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. Shared memory: one process writes to a common region, another reads — fast bulk sharing.
2. A pipe is shared memory with a server (creator) and client (connector), uni- or bidirectional.
3. Anonymous pipes are unnamed/unidirectional; named pipes can span machines and be bidirectional.

---

## Q5. Message Queue in Task Communication (10)

**Definition.** A **Message Queue** is a **First-In-First-Out (FIFO)** queue that temporarily stores messages in a system-defined memory object to pass them between processes. The sender **posts** a message to the queue; the receiver retrieves it, via `send` and `receive`:

```text
send(name of destination process, message)
receive(name of source process, message)
```

The implementation of the queue and the send/receive methods is **OS-kernel dependent**.

**Windows XP message queue.** The kernel keeps **one system message queue** and **one message queue per process/thread**. A thread posts a message to the **system** queue; the kernel picks it up, finds the destination thread, and posts it to **that thread's** queue.

| Type | Behaviour |
|---|---|
| **Asynchronous messaging** | The posting thread posts the message and does **not** wait for acceptance/return. |
| **Synchronous messaging** | The posting thread **enters a waiting (blocked) state** until the result arrives; the scheduler won't schedule it meanwhile. |

**Overleaf / TikZ code — message queue:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  p/.style={draw, very thick, rounded corners, fill=blue!8, align=center, minimum width=2.4cm, minimum height=1.1cm},
  q/.style={draw, thick, minimum width=1cm, minimum height=0.9cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[p] (p1) at (0,0){Process 1};
\node[q, fill=orange!15] (m1) at (3.2,0){M3};
\node[q, fill=orange!15] (m2) at (4.2,0){M2};
\node[q, fill=orange!15] (m3) at (5.2,0){M1};
\node[font=\scriptsize] at (4.2,-0.9){Message Queue (FIFO)};
\node[p] (p2) at (8.4,0){Process 2};
\draw[ar] (p1) -- node[above,font=\scriptsize]{send} (m1);
\draw[ar] (m3) -- node[above,font=\scriptsize]{receive} (p2);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A message queue is a FIFO store; sender posts, receiver reads via send/receive.
2. In Windows XP, a system queue routes each message to the destination thread's own queue.
3. Posting can be asynchronous (don't wait) or synchronous (sender blocks for the result).

---

## Q6. Mailbox in Task Communication (10)

**Definition.** A **Mailbox** is an alternate form of message queue used in RTOS for IPC, usually for **one-way messaging**.

**Working:**
- The task that wants to send creates a **mailbox** for posting messages.
- Interested tasks **subscribe** to the mailbox.
- The creator is the **mailbox server**; subscribers are **mailbox clients**.
- The server **posts** a message and **notifies** subscribed clients; clients **read** the message on notification.
- Creation, subscription, reading, and writing use OS-kernel API calls.

**Mailbox vs Message Queue (key difference):** a mailbox supports the exchange of a **single message** between two tasks (or between an ISR and a task), whereas a message queue supports **multiple messages**. A mailbox associates a **pointer** to the mailbox plus a **wait list** of tasks waiting for a message.

**Overleaf / TikZ code — mailbox broadcast:**

```latex
\documentclass[border=10pt]{standalone}
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning}
\begin{document}
\begin{tikzpicture}[font=\sffamily\small,
  t/.style={draw, very thick, rounded corners, fill=blue!8, align=center, minimum width=2.2cm, minimum height=1cm},
  mb/.style={draw, very thick, rounded corners, fill=orange!15, align=center, minimum width=2.4cm, minimum height=1cm},
  ar/.style={-{Latex[length=2.5mm]}, thick}
]
\node[t]  (t1) at (0,0){Task 1\\\footnotesize (server)};
\node[mb] (mb) at (3.4,0){Mailbox};
\node[t]  (t2) at (7,1.6){Task 2};
\node[t]  (t3) at (7,0)  {Task 3};
\node[t]  (t4) at (7,-1.6){Task 4};
\draw[ar] (t1) -- node[above,font=\scriptsize]{post} (mb);
\draw[ar] (mb) -- node[above,font=\scriptsize]{notify} (t2);
\draw[ar] (mb) -- (t3);
\draw[ar] (mb) -- (t4);
\end{tikzpicture}
\end{document}
```

**Step-by-step reasoning:**
1. A mailbox is a one-way message store; the server creates and posts, clients subscribe and read on notification.
2. It differs from a message queue by carrying a **single** message (vs multiple).
3. It uses a pointer + a wait list of tasks awaiting a message.

---

## Q7. Remote Procedure Call (RPC) and Sockets (10)

**Remote Procedure Call (RPC).** An IPC mechanism by which a process **calls a procedure of another process** running on the **same CPU or a different networked CPU**. (Also called Remote Invocation / Remote Method Invocation, RMI.)
- Mainly used for **distributed applications** (client–server) and works over a **heterogeneous network** (client and server on different OSs).
- The process containing the remotely invoked procedure = **server**; the initiator = **client**.
- Interfaces are defined using an **Interface Definition Language (IDL)** — Microsoft's is **MIDL**.
- **Communication modes:** **Synchronous/blocking** (caller blocks until the response) and **Asynchronous/non-blocking** (caller continues; results returned via callbacks).
- **Security:** RPC uses **authentication** (IDs, public-key cryptography like DES/3DES); without it, any client could access the procedure.

**Sockets.** A **socket** is a **logical endpoint** in a two-way communication link between two applications on a network, used for RPC communication.
- A **port number** is associated with a socket so the network layer delivers data to the right application.
- Types: **Internet (INET) sockets**, UNIX sockets, etc. INET sockets use TCP/IP, UDP.

| INET socket | Protocol | Nature |
|---|---|---|
| **Stream socket** | TCP | Connection-oriented, **reliable** |
| **Datagram socket** | UDP | Connectionless, **unreliable** |

**Client–server model (brief):** both client and server are assigned port numbers; the server **listens** on its port; the client sends a **connection request**; after **authentication** the connection is granted and a channel is established. Physical link uses Ethernet/Wi-Fi; the socket implementation is OS-kernel dependent.

**Step-by-step reasoning:**
1. RPC lets a process invoke a procedure in another (possibly remote) process — client calls, server executes.
2. It can be synchronous (block) or asynchronous (callback), secured by authentication.
3. Sockets (endpoints + port numbers) carry RPC traffic; stream/TCP = reliable, datagram/UDP = unreliable.

---

## Q8. Types of Task Communication (10)

Processes are classified by interaction as **co-operating** (one needs another's input) or **competing** (share only system resources). Co-operation is achieved **through sharing** (data via shared resources) or **through communication** (no data shared, only synchronisation). The **Inter-Process Communication (IPC)** mechanisms are:

| IPC mechanism | Idea |
|---|---|
| **Shared Memory** | Processes share a memory region; one writes, others read. Implemented via **Pipes** and **Memory-Mapped Objects**. |
| **Pipes** | Section of shared memory in client–server form (pipe server creates, client connects); uni- or bidirectional (anonymous / named pipes). |
| **Memory-Mapped Objects** | A mapping object reserves committed physical memory mapped into each process's virtual address space; reads/writes go to the shared physical area. |
| **Message Passing** | (A)synchronous exchange of a **limited** amount of data; faster, free of shared-memory sync overhead. Forms: **Message Queue, Mailbox, Signalling**. |
| **Message Queue** | FIFO store; sender posts, receiver reads via send/receive. |
| **Mailbox** | One-way single-message exchange; server posts and notifies subscribed clients. |
| **Signalling** | Primitive async notification; signals are **not queued and carry no data** (e.g. RTX51 Tiny, VxWorks). |
| **RPC and Sockets** | A process calls a procedure in another (local/remote) process; sockets (endpoints + ports) carry the communication. |

**Step-by-step reasoning:**
1. Processes co-operate (share data / sync) or compete (for resources).
2. Bulk data → shared memory (pipes, memory-mapped objects); limited data → message passing (queue, mailbox, signalling).
3. Across CPUs/networks → RPC over sockets.

---

## Q9. Priority Scheduling with later arrival — P1=10(p1), P2=5(p3), P3=7(p2) @0; P4=6(**p1**) @5 ms (10)

**Rule.** Lower number = higher priority; a newly arrived process preempts the running one **only if it has *higher* priority** (strictly smaller priority number).

> **Important — differs from the notes' worked example.** In the notes, P4 has **priority 0** (higher than P1's 1) and so **preempts** P1 at t=5. **Here P4 has priority 1, the *same* as P1.** Equal priority does **not** preempt, so **P1 continues to completion** and P4 waits. The numbers below reflect the question as given (P4 = priority 1).

**Ready-queue progression:**

| Time | Action |
|---|---|
| 0 | P1(p1), P2(p3), P3(p2) → **P1** scheduled (highest) |
| 5 | P4(6, **p1**) arrives; same priority as running P1 → **no preemption**, P1 continues |
| 10 | P1 done; Ready: P4(p1), P3(p2), P2(p3) → **P4** scheduled (highest) |
| 16 | P4 done → **P3** scheduled (p2) |
| 23 | P3 done → **P2** scheduled (p3) |
| 28 | P2 done |

**Execution sequence:**

```
|          P1          |     P4     |      P3      |    P2   |
0                     10           16            23        28  (ms)
```

| Process | Arrival | Waiting time | TAT = completion − arrival |
|---|---|---|---|
| P1 | 0 | 0 | 10 − 0 = 10 |
| P4 | 5 | 10 − 5 = 5 | 16 − 5 = 11 |
| P3 | 0 | 16 | 23 − 0 = 23 |
| P2 | 0 | 23 | 28 − 0 = 28 |

$$\text{Average Waiting Time} = \frac{0 + 5 + 16 + 23}{4} = \frac{44}{4} = 11\ \text{ms}$$

$$\text{Average TAT} = \frac{10 + 11 + 23 + 28}{4} = \frac{72}{4} = 18\ \text{ms}$$

**Step-by-step reasoning:**
1. P1 (highest) starts; P4 arrives at t=5 with **equal** priority → cannot preempt → P1 finishes at 10.
2. Then the Ready queue runs in priority order: P4 (p1) → P3 (p2) → P2 (p3).
3. Waiting = (start − arrival) for non-preempted runs; TAT = completion − arrival.

> If instead the intended priority of P4 were **0** (as in the source notes), P4 would preempt P1 at t=5, giving average waiting 11.25 ms and average TAT 18.25 ms. I answered the question exactly as written (P4 = priority 1).

---

## Q10. Non-preemptive SJF — P1=10, P2=5, P3=7 ms @0 (10)

**Rule.** All three arrive at t=0 and there are no later arrivals, so Shortest Job First runs them in increasing burst order with **no preemption**: **P2 (5) → P3 (7) → P1 (10)**.

**Execution sequence:**

```
|    P2    |      P3      |          P1          |
0          5             12                      22  (ms)
```

| Process | Waiting time | TAT = wait + burst |
|---|---|---|
| P2 | 0 | 0 + 5 = 5 |
| P3 | 5 | 5 + 7 = 12 |
| P1 | 12 | 12 + 10 = 22 |

$$\text{Average Waiting Time} = \frac{0 + 5 + 12}{3} = \frac{17}{3} = 5.67\ \text{ms}$$

$$\text{Average TAT} = \frac{5 + 12 + 22}{3} = \frac{39}{3} = 13\ \text{ms}$$

**Step-by-step reasoning:**
1. With all arriving together and no preemption, SJF orders by burst: P2, P3, P1.
2. Each process waits for the shorter ones ahead of it.
3. TAT = waiting + burst; averages over 3 processes.

---

*End of Module-5 Focused Answers*

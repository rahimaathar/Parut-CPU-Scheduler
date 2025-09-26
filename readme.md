# Parut CPU Scheduler – CS 367 

*Code available on request. Not posted due to Honor Code Policies.*

This project is my implementation of the **Parut CPU Scheduler**, the scheduling component used by the StrawHat Task Manager. StrawHat is a lightweight shell that swaps between Linux processes; my scheduler code is what decides which process gets CPU time, and how processes move between different queues.  

---

## Project Overview

The scheduler manages three **singly linked lists** (queues):  

- **Ready Queue** – processes waiting for CPU time  
- **Suspended Queue** – processes paused by the user  
- **Terminated Queue** – processes that have finished or been killed  

I implemented **12 required functions in `parut_sched.c`**, handling process creation, queue insertion/removal, state updates, and cleanup. The logic also ensures fairness with **priority scheduling + starvation protection**.

---

##  Key Functions

- **Initialization**
  - `parut_initialize()` → sets up the scheduler and its three queues  

- **Process Management**
  - `parut_create()` → builds a new process node from PID, command, and priority  
  - `parut_add()` → inserts a process into the Ready Queue  
  - `parut_length()` → returns number of processes in a queue  

- **Scheduling**
  - `parut_select()` → picks the next process:
    1. Critical process (if any)  
    2. Otherwise, starving process (age ≥ `STARVING_AGE`)  
    3. Otherwise, highest priority (lowest number)  
    - Also increments ages of all other processes  

- **State Transitions**
  - `parut_suspend()` / `parut_resume()` → move processes between Ready and Suspended queues  
  - `parut_exited()` / `parut_killed()` → move processes into the Terminated Queue with proper exit codes  

- **Cleanup**
  - `parut_reap()` → remove and free a terminated process while returning its exit code  
  - `parut_get_ec()` → extract the exit code from process state bits  
  - `parut_destroy()` → frees all queues, processes, and memory  

---

## Techniques Used

- **Linked Lists** – custom insert/remove logic without dummy nodes  
- **Bitwise Operations** – used to manage state flags (Ready, Suspended, Terminated, Critical) and encode exit codes in 16 bits  
- **Error Checking** – pointer validation, `malloc` return checks, safe handling of empty queues  
- **Aging** – starvation prevention by incrementing process age each time another process is selected  

---

##  Testing

- **Unit Testing** → with `test_parut_sched.c` to call functions directly  
- **StrawHat Integration** → running `./straw_hat` to verify scheduling in action  
- **Memory Leak Checks** → used `valgrind` to confirm all allocations were freed correctly  

---

##  Build & Run

On the **Zeus server**:  

```bash
# Extract starter code
tar -xvf project1_handout_v1_0.tar
cd handout

# Build StrawHat + Scheduler
make

# Run the Task Manager
./straw_hat

# Session 2 - Class Activity 01


## 1) Exercise Goals

[x] Understanding Task priority in RTOS 
[x] Understanding when is convenient to use a task

## 2) Materials & Setup

- **Tools/Software** - Editors: VS Code, Python 3.12

## 3) Procedure 

### Exercise  1

| Task Name                 | Trigger (Time / Event) | Periodic or Event-Based |
|---------------------------|------------------------|-------------------------|
| Temperature Read Task     | every 50 ms            | Periodic
| Wi-Fi Send Task           |      every 2 s         | Periodic                |
| Emergency Button Monitor  |      button press      | Event-Based             |
| Status LED Blink Task     |      1 Hz / 1 s        | Periodic                |
| Error Logging Task        |      error detected    | Event-Based             |

### Exercise  2

| Task Name                | Time-Critical | Can Block Safely | If Delayed… |
|--------------------------|---------------|------------------|-------------|
| Temperature Read Task    | No            | Yes              | Does not need to be precise with timing, temperature usually doesnt change fast |
| Wi-Fi Send Task          | Yes            | No               | Data can be lost. |
| Emergency Button Monitor | Yes           | No               | Emergency event may not be detected in time, causing safety risk. |
| Status LED Blink Task    | No            | Yes              | LED blink becomes irregular; no functional impact. |
| Error Logging Task       | No            | Yes              | Error information may be recorded late. |

### Exercise  3

| Task Name                | Priority (H/M/L) | Justification |
|--------------------------|------------------|---------------|
| Emergency Button Monitor | High             | Requires immediate response to ensure system and user safety. |
| Temperature Read Task    | Medium           | Must meet a fixed sampling period but tolerates small timing jitter. |
| Wi-Fi Send Task          | Medium           | Does not affect real-time behavior. |
| Status LED Blink Task    | Low              | Purely informative task with no impact on system functionality. |
| Error Logging Task       | Low              | Can be deferred without affecting real-time system operation. |

### Exercise  4

Which of the following should NOT necessarily be implemented as a FreeRTOS task?

- Emergency button monitoring
- Wi-Fi transmission
- Error logging
- Status LED blinking
- Explain why in 2–3 sentences.

Status LED blinking should not necessarily be implemented as a FreeRTOS task.
It can be handled efficiently using a software timer or the RTOS idle hook, since it has no real-time or safety requirements. Making it a full task would waste CPU time and scheduling resources without adding functional value.

### Exercise 5 — Identifying Hidden Tasks in Pseudo-Code
Task 5.1 — Identify Hidden Tasks

Acording to pseudocode shown in class webpage: 


| Hidden Task                | Trigger (Time / Event)        | Why it should be a Task |
|----------------------------|-------------------------------|--------------------------|
| Temperature Sampling       | Time (every loop / ~50 ms)    | Requires periodic execution with consistent timing. |
| Emergency Button Monitoring| Event (button press)          | Safety-critical and requires immediate response. |
| Wi-Fi Data Transmission    | Time (every 2 seconds)        | Long and blocking operation that should not delay other logic. |
| Status LED Blinking        | Time (1 Hz)                   | Periodic behavior independent of main control flow. |

Task 5.2 — Blocking Analysis
Answer the following:

Which function can block the CPU?
What other behaviors are affected while it blocks?
Which hidden task is most at risk because of this blocking?

- The function send_data_over_wifi() 
- While it blocks, button monitoring, temperature sampling, and LED timing are delayed.
- The emergency button 

Task 5.3 — RTOS Refactoring Thought Experiment

Which hidden task(s) should become FreeRTOS tasks?
Which behavior(s) should be handled using an interrupt?
Which task should have the highest priority, and why?

- Temperature sampling and Wi-Fi transmission should be implemented as FreeRTOS tasks to allow independent scheduling.
- Emergency button handling should be triggered by an interrupt to guarantee immediate response.
- The emergency button task should have the highest priority because it is safety-critical and has the strictest timing requirements.


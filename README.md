# Embedded Systems Tasks

This repository focuses on evaluating different tasks related to the performance, memory usage, and precision for embedded systems. Each task compares two or more methods for solving a specific problem.

## Task 1: Performance in Using Lookup Tables vs. math.h with Floating-Point

### 1. Using a Lookup Table

A lookup table is a predefined array where results for a range of mathematical inputs (e.g., sin(x), log(x)) are stored. Instead of calculating values in real-time, the program fetches them from the table.

#### Advantages:
- **Speed**: Fetching values from memory is much faster than performing calculations.
- **Predictability**: Constant retrieval time, crucial for real-time systems.
- **No FPU Dependency**: AVR microcontrollers like the ATmega32 lack hardware floating-point units, making this method more efficient.
- **Low CPU Usage**: Minimal CPU cycles are consumed.

#### Disadvantages:
- **Memory Usage**: Large lookup tables can consume a significant amount of memory.
- **Limited Precision**: You must balance precision and memory.
- **Less Flexibility**: Predefined tables can handle only specific operations.

### 2. Using math.h and Floating-Point Operations

The C Standard Library (math.h) offers functions like sin(), cos(), and pow() that compute operations at runtime using floating-point arithmetic.

#### Advantages:
- **High Precision**: Functions like sin() and cos() provide high precision results.
- **Flexibility**: Dynamically handle a wide range of inputs and complex calculations.
- **Minimal Memory Usage**: No need for large arrays.

#### Disadvantages:
- **Speed**: Floating-point operations are slower, especially without an FPU.
- **CPU Usage**: High CPU usage due to computationally expensive floating-point calculations.

---
### Practical Results

1. **Using Lookup Table (LUT)**: 
   - For 1000 iterations of calculating sin(45°), the command took **162 tick_time** by reading timer_reg before and after.
   - [SINE_LUT_program.c](APP/SINE_LUT_program.c).
   - [SINE_LUT_interface.h](APP/SINE_LUT_interface.h)

2. **Using math.h**:
   - For 1000 iterations of calculating sin(45°), the command took **244 tick_time** by reading timer_reg before and after.
   - The same timing technique by generating signal on pin and receive it on Oscilloscope (high signal before, low signal after) yielded these results.
   - ![Using_lut_5us](Documents/Using_lut_5us.png)
     *Figure 1: signal measurement for sine(45°) using LUT.*
     
   - ![Without-LUT_6us](Documents/Without-LUT_6us.png)
     *Figure 1: signal measurement for sine(45°) using math.h.*

---


## Task 2: Using Fixed-Point Math for Floating-Point Operations

In this task, we use fixed-point math to handle floating-point operations such as addition, subtraction, multiplication, and division. Fixed-point arithmetic offers several advantages in terms of speed, memory usage, and predictability, particularly in systems where floating-point units (FPUs) are not available.

### 1. Using Fixed-Point Math

Fixed-point math uses integers to represent real numbers by scaling the values. Typically, a **Q16.16** or **Q8.8** format is used, where part of the integer is reserved for the fractional value. 

- **Scale**: A shift factor like 2^16 (for Q16.16) is applied to real numbers to convert them to fixed-point.
- **Shift**: For operations like multiplication, a right shift is required to avoid overflow, and during division, a left shift is done to maintain precision.

This method is faster and more memory-efficient than floating-point arithmetic on systems like the ATmega32, which lack hardware FPUs.

The following operations have been implemented using fixed-point math:
- **Addition**: Add_fixed_point()
- **Subtraction**: Sub_fixed_point()
- **Multiplication**: mul_fixed_point()
- **Division**: div_fixed_point()

#### Code Implementation:
- [FixedPoint_program.c](APP/FixedPoint_program.c)
- [FixedPoint_interface.h](APP/FixedPoint_interface.h)

### 2. Advantages of Fixed-Point Math

#### Advantages:
- **Speed**: Fixed-point operations are significantly faster than floating-point because they involve integer arithmetic rather than complex floating-point calculations.
- **Memory Efficiency**: Fixed-point operations use less memory compared to floating-point, as no floating-point libraries or large variables are needed.
- **Deterministic Timing**: Fixed-point operations have constant and predictable execution times, which is essential in real-time systems.
- **No FPU Required**: Embedded systems like the ATmega32 do not have hardware FPUs, making floating-point operations computationally expensive. Fixed-point math eliminates the need for an FPU.

#### Disadvantages:
- **Lower Precision**: Compared to floating-point arithmetic, fixed-point offers lower precision, especially when dealing with numbers with many decimal places.
- **Manual Scaling**: Fixed-point math requires manual scaling and can be prone to overflow/underflow issues if not carefully managed, particularly in multiplication and division operations.

---

## Practical Results

1. **Multiplication(Fixed-Point)**:
   - The 1000 mul_fixed_point(229.899, 141.2569) function took **83 tick**.
   
2. **Multiplication (Floating-point)**:
   - The 1000 mul_floating_point(229.899, 141.2569) function took **242 tick**.

   ![fixed_point](Documents/fixed_point.png)
   *Figure 3: Timing measurement for multiplication using fixed-point math and floating point math.*

---

## Task 3: Design a Simple Event-Based System Using AVR Microcontroller

In an event-based system using an AVR microcontroller (MCU), the MCU reacts to external or internal events, triggering actions only when those events occur.

### 1. Button Press Event (External)

The MCU waits for a button press (an event) and reacts by toggling an LED.

#### Components:
- Atmega32 MCU
- Push button
- LED

#### Steps:
- **Event Source**: Button press (connected to an external interrupt pin, e.g., INT0).
- **Event Handler**: ISR (Interrupt Service Routine) toggles the LED.

### 2. Timer Event (Internal)

The MCU toggles the LED every 3 seconds using a timer (timer1 overflow event).

#### Components:
- Atmega32 MCU
- LED

#### Steps:
- **Event Source**: Timer overflow (Timer1) and implemented some services functions to get interrupt after (n)ms.
  - [Timers_Services.c](SERVICE/Timers_Services.c)
- **Event Handler**: Timer1_OVF_ISR toggles the LED.

![Event_based_system](Documents/Event_base_system.png
 *Figure 4: Timing measurement for multiplication using fixed-point math and floating point math.*


---

The event-based system could be extended to handle additional events such as:

### *. UART Data Reception Event

When the MCU receives data via UART (serial communication), an ISR is triggered, and the MCU processes the incoming data and takes action based on a certain message.

### *. ADC Conversion Complete Event

When the ADC completes an analog-to-digital conversion, the MCU reads the converted value and processes it, taking action when a certain voltage value is reached. This method is commonly used with sensors in environmental control systems, such as turning on a fan when the temperature exceeds a certain threshold.


### 3. Event-Based System on AVR Using Event Loop

This project demonstrates a simple event-based system on an AVR microcontroller using an event loop to handle multiple events and actions. The system consists of 4 buttons (events) and 4 LEDs (actions), with timer interrupts used to periodically update the event queue.

#### Key Components

- **Event Queue**: A queue implemented as an array to store pending events.
- **Event Loop**: Continuously processes events from the queue and triggers corresponding actions.
- **Timer Interrupt**: A timer interrupt periodically polls the buttons (key polling) and adds events to the queue.
- **Event Handling**: Functions to handle each event, such as toggling LEDs or triggering other actions.

#### System Design
![Event_base_system_Diagram.png](Documents/Event_base_system_Diagram.png)

 *Figure 5: Diagram for multi_base_system architecture and its simulation on proteus.*

##### Main Loop:
- The main loop continuously processes events from the event queue. It fetches events, identifies the type of event (which button was pressed), and triggers the appropriate action (toggle corresponding LED).

##### Timer Interrupt:
- A timer interrupt is set up to periodically poll the buttons. If a button is pressed, the event is added to the queue for processing in the main loop.

##### Event Queue:
- The event queue is implemented as a circular array. Each button press generates a unique event, which is stored in the queue for the event loop to process.

#### Code Implementation:
- [Event_Based_program.c](APP/Event_Based_program.c)
- [Event_Based_interface.h](APP/Event_Based_interface.h)

  
---

## Task 4: comming soon !



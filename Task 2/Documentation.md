# UART Loopback with RGB LED Control – Project Documentation

## Step 1: Study the Existing Code

**UART (Universal Asynchronous Receiver-Transmitter)** is a hardware communication protocol used for serial communication between devices. It involves two main lines:
- **TX (Transmit)**
- **RX (Receive)**

### UART Loopback Mechanism
UART loopback is a test configuration where the TX line is directly routed back to the RX line on the same device. This is used to verify UART communication without requiring an external device.

### Code Analysis

#### Port Description
The module includes six ports:
- Three RGB LED outputs: `led_red`, `led_blue`, `led_green`
- UART transmit and receive pins: `uarttx`, `uartrx`
- System clock input: `hw_clk`

#### Key Internal Components

1. **Internal Oscillator (SB_HFOSC)**
   - Generates a high-frequency internal clock.
   - Frequency divided with `CLKHF_DIV = "0b10"`.

2. **Frequency Counter**
   - 28-bit counter named `frequency_counter_i`.
   - Increments on every positive clock edge.

3. **UART Loopback**
   - Directly connects `uartrx` to `uarttx`.
   - Immediately echoes received data.

4. **RGB LED Driver (SB_RGBA_DRV)**
   - Drives RGB channels using PWM.
   - UART input signal controls LED brightness.
   - Current limiting configured (`0b000001`) for each channel.

### UART Input Processing
- Received UART data from `uartrx` is looped back to `uarttx`.
- Same data is used to drive all three RGB LED channels.

### LED Control
- UART signal converted to PWM signals.
- All LEDs behave identically to the input.
- Uses minimum current settings to protect components.

---

## Step 2: Design Documentation

### Block Diagram
![Block Diagram]

### Circuit Diagram
![Circuit diagram]

---

## Step 3: Implementation Guide

### Project Setup
1. Create a folder inside `VSDSquadron_FM`, e.g., `UART_loopback`.
2. Place the provided Verilog files and `Makefile` into that folder.

```bash
cd VSDSquadron_FM
cd UART_loopback

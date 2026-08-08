# UART FPGA Debug Interface

A VHDL UART debug interface for monitoring a traffic-light controller on an FPGA in real time — because debugging an FSM with blinking LEDs only gets you so far.

## Why this project

I already had a working traffic-light controller on FPGA, but no clean way to observe what was happening inside it without going back to the simulator every time. The idea: build a real UART interface that streams the system's live state to a terminal, and can eventually reconfigure it remotely — a classic embedded-systems problem (observability of a real-time system), tackled here entirely in hardware logic.

## What it does

- Full asynchronous serial communication (8 data bits, 1 start bit, 1 stop bit, configurable baud rate)
- Custom FSMs for the UART transmitter, UART receiver, and the controller bridging the two
- Automatic encoding and transmission of the traffic light state on every transition (`Pv Lr`, `Pj Lr`, `Pr Lv`, `Pr Lj`)
- Reception and interpretation of incoming commands to act on the system
- Frame/overrun error detection on the receive side

## Architecture

| Module | Role |
|---|---|
| Traffic light controller | Sequential/combinational logic for the lights (main street / side street) |
| `UARTController` | Translates the light state into bytes to transmit, and incoming commands into actions |
| `UART` | Bit-level serial transmit/receive, parallel registers (RDR/TDR) and shift registers (RSR/TSR), baud rate generation |

FSM diagrams and the full block diagram are in `/docs`.

## Tech stack

- VHDL (Quartus II)
- Altera/Intel Cyclone DE2 FPGA
- ModelSim simulation
- UART / RS-232 protocol

## Validation

Verified through simulation: traffic-light state transitions and TX line activity match the expected behavior (waveforms in `/docs`). Physical testing over standard RS232 didn't succeed (the device wasn't recognized by the computer) — TX/RX activity was confirmed with diagnostic LEDs while a cleaner hardware fix is pending.

## Repo structure

```
├── src/    # .vhd files (UART.vhd, UARTController.vhd, top-level, etc.)
├── sim/    # testbenches + exported waveforms
├── docs/   # FSM diagrams, block diagram
└── README.md
```

# AGV Operator HMI

An interactive operator HMI for the University West **TwinCAT Virtual AGV** (Automatic Guided Vehicle), built for **PLC Task 3 — ATM700**.

The interface lets an operator dispatch the AGV between floor stations and recover from emergency stops **without seeing the vehicle**, faithfully mirroring the IEC 61131-3 state machine in the underlying PLC program.

🔗 **Live demo:** https://gabrieldanho9988-sys.github.io/agv-operator-hmi/

## Features

- **Target selection** — single dropdown (A · B · C · D · H), as required by the spec (no five separate buttons).
- **START / STOP / RESET** illuminated control buttons with a hardware-style emergency stop.
- **Status indicators** — READY, MOVING (blinking), and STOP lamps.
- **Position display** — 7-segment-style readout showing the AGV's actual position only when it is standing still at a station; blank while moving.
- **Live plant floor map** — the AGV follows the guide tracks between stations A, B, C, D and HOME in real time.

## Behaviour (matches the control specification)

| Action | Result |
|--------|--------|
| Select target → START | AGV moves to the chosen station |
| Change target / press Start while moving | Ignored (target is locked during motion) |
| STOP while moving | AGV halts immediately; STOP indicator latches |
| After a stop | New targets are blocked until RESET is pressed |
| RESET | AGV returns to its HOME position, then the system is READY again |
| READY lamp | On only when it is safe to select a target and start |

## How it works

The HMI reproduces the PLC handshake logic: a strobe-based request/answer cycle drives the AGV through dispatch, movement, the full stop sequence, and the reset-to-home recovery. The `FB_AGV_Controller` state machine moves through an idle/ready state, a dispatch handshake, motion, and — on an emergency stop — a complete stop-and-reset-to-home sequence before returning to ready. The on-screen vehicle is animated along a track graph (shortest path between stations), so motion and indicator states stay consistent with the real control flow.

## PLC source & specification

This web HMI is a faithful re-creation of the real Beckhoff TwinCAT project. The original program is included for reference:

- **[`AGV_PLC_project.pdf`](AGV_PLC_project.pdf)** — the complete TwinCAT program: the `FB_AGV_Controller` state machine (idle → dispatch → move → stop/reset handshake), the reusable `AGV` communication function block, the GVL, the target and text lists, and the TwinCAT operator visualization.
- **[`PLC_task_3_ATM700.pdf`](PLC_task_3_ATM700.pdf)** — the original University West (ATM700) assignment specification this design fulfils.

## Run it locally

It is a single self-contained file — no build step, no dependencies.

```bash
# just open it
open index.html        # macOS
# or double-click index.html
```

## Tech

- Plain HTML + SVG, animated with `requestAnimationFrame`
- Finite-state machine modelled on the original Structured Text (ST) controller
- Industrial control-panel visual design

## Author

Gabriel Danho — University West, Department of Engineering Science

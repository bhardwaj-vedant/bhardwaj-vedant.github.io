---
title: "Guidance and Control of UAVs"
collection: portfolio
kind: project
order: 14
excerpt: "Course project (AE700), IIT Bombay, January–March 2026 · Advisor: Prof. S. Kumar<br/>A nonlinear 6-DOF flight simulator built end to end, with an autopilot designed by successive loop closure."
---

**Course:** AE700, IIT Bombay (January – March 2026)<br/>
**Advisor:** Prof. S. Kumar

### Overview
The project built a complete simulation of a fixed-wing aircraft, starting from the nonlinear
six-degree-of-freedom equations of motion, and then designed an autopilot on top of it. The autopilot uses
successive loop closure, a standard technique in which inner loops (attitude) are designed and closed first and
outer loops (altitude, heading, airspeed) are then built around them.

### What I did

- Built a nonlinear **12-state, 6-DOF** flight simulation in **Simulink** and ran staged test cases across the
  full flight model.
- Verified trim convergence to machine precision (**92.47 m/s, 22.4°**), with every state held constant over a
  25 s simulation.
- Checked the dynamics with isolated force and moment inputs; the state responses matched analytical
  predictions. Designed the autopilot by **successive loop closure**.

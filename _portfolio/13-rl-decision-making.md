---
title: "Reinforcement Learning and Decision Making"
collection: portfolio
kind: project
order: 13
excerpt: "Course project (CS747), IIT Bombay, August–November 2025 · Advisor: Prof. Shivaram Kalyanakrishnan<br/>Exact solvers for Markov decision processes, and a competitive Minimax agent for 5×4 Microchess."
meta: "Course Project (CS747) · IIT Bombay · August – November 2025 · Advisor: Prof. Shivaram Kalyanakrishnan"
overview: |
  The project took two complementary views of sequential decision-making. The first part concerns planning with
  a known model: computing optimal policies and value functions for finite Markov decision processes by solving
  the Bellman optimality equations exactly. The second part concerns adversarial search: building an agent that
  plays Microchess, a 5×4 chess variant, against a rational opponent. The difficulty there is searching a game
  tree too large to expand fully, and evaluating the positions where the search has to stop.
highlights:
  - "**Exact MDP solvers:** Howard's Policy Iteration and a Linear Programming formulation for finite MDPs."
  - "**Game-playing agent:** depth-limited **Minimax** search for 5×4 Microchess."
  - "**Evaluation function:** a hand-designed heuristic combining material advantage, piece mobility, and king safety."
---

**Course:** CS747 – Foundations of Intelligent and Learning Agents, IIT Bombay (August – November 2025)<br/>
**Advisor:** Prof. Shivaram Kalyanakrishnan

### Overview
The project had two parts. The first covered *planning with a known model*: computing optimal policies for
finite Markov decision processes with classical exact algorithms. The second covered *adversarial search*:
building an agent that plays Microchess, a small 5×4 chess variant, well against a rational opponent.

### What I did

- Implemented **Howard's Policy Iteration** and a **Linear Programming** formulation to solve finite Markov
  decision processes, and evaluated the resulting optimal policies and value functions.
- Built an autonomous agent for a **5×4 Microchess** environment using depth-limited **Minimax** search to
  play against rational opponents.
- Designed a heuristic evaluation function that weighs material advantage, piece mobility, and king safety.

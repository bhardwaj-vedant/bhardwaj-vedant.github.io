---
title: "Neural Reinforcement Learning for Portfolio Optimisation"
collection: portfolio
kind: project
order: 15
excerpt: "Finance Club, IIT Bombay (FinSearch), July 2025<br/>Deep RL agents (DQN, PPO, A2C, DDPG, TD3, SAC) for sequential portfolio decisions, compared against ARIMA, Prophet, and LSTM baselines. · [Code](https://github.com/theFulminatedHuman/RL-in-Stock-Trading-Optimization)"
meta: "FinSearch · Finance Club, IIT Bombay · July 2025"
code: "https://github.com/theFulminatedHuman/RL-in-Stock-Trading-Optimization"
overview: |
  Portfolio management is naturally a sequential decision problem under uncertainty. At each step an agent
  reallocates capital across assets, and each allocation shapes the opportunities that follow. This project
  surveyed the main families of deep reinforcement learning, value-based methods and actor-critic methods for
  continuous control, by implementing each from scratch. It used a classical control problem as a sanity check,
  then applied continuous-action agents to Indian equity data and compared them with statistical forecasting
  baselines.
highlights:
  - "**Algorithms:** **DQN, PPO, A2C, DDPG, TD3, and SAC**, implemented from scratch in Keras."
  - "**Sanity check:** the inverted pendulum, solved with actor-critic torque control, serves as an analogue of continual portfolio rebalancing."
  - "**Market data:** **DDPG** agents on NIFTY50 (2010–2019), compared with ARIMA, Prophet, and LSTM baselines."
---

**Organisation:** Finance Club, IIT Bombay (FinSearch, 4 weeks, July 2025)<br/>
**Code:** [github.com/theFulminatedHuman/RL-in-Stock-Trading-Optimization](https://github.com/theFulminatedHuman/RL-in-Stock-Trading-Optimization)

### Overview
Portfolio management is a sequential decision problem: at each step the agent reallocates capital across
assets, and each decision affects the opportunities available later. The project surveyed the main families of
deep RL algorithms, both value-based and actor-critic, by implementing each from scratch. It then applied the
continuous-action methods to real Indian equity data.

### What I did

- Implemented **DQN, PPO, A2C, DDPG, TD3, and SAC** from scratch in Keras to compare how well each suits
  sequential decision-making under uncertainty.
- Solved the **inverted pendulum** with actor-critic torque control. Balancing the pendulum is a useful analogy
  for continually rebalancing a portfolio in a changing market.
- Trained **DDPG** agents on **NIFTY50 (2010–2019)** with engineered state features and continuous actions, and
  compared them with ARIMA, Prophet, and LSTM forecasters.

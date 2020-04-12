---
title: "《Reinforcement Learning: An Introduction》阅读笔记"
copyright: true
mathjax: true
top: 1
date: 2020-04-02 21:29:07
categories: ReinforcementLearning
tags:
- rl
keywords:
description:
---

学习RL至今(2020年04月02日21:30:19)，一直没有系统地看过这本被誉为RL界“圣经”的教科书，也没有对从该书中学到的知识点进行整理与记录，本文将记录重读《Reinforcement Learning: An Introduction》这本书时所学到的关键知识点和受到的启发。

<!--more-->

# Introduction

## Chapter 1 Introduction

### Reinforcement Learning

### Examples

### Elements of Reinforcement Learning

### Limitations and Scope

### An Extended Example: Tic-Tac-Toe

### Summary

### Early History of Reinforcement Learning

# Tabular Solution Methods

## Chapter 2 Multi-armed Bandits

### A k-armed Bandit Problem

### Action-value Methods

### The 10-armed Testbed

### Incremental Implementation

### Tracking a Nonstationary Problem

### Optimistic Initial Values

### Upper-Confidence-Bound ActionSelection

### Gradient Bandit Algorithms

### Associative Search(Contextual Bandits)

### Summary

## Chapter 3 Finite Markov Decision Processes

### The Agent-Environment Interface

### Goals and Rewards

### Returns and Episodes

### Unified Nation for Episodic and Continuing Tasks

### Policies and Value Functions

### Optimial Policies and Optimal Value Functions

### Optimality and Approximation

### Summary

## Chapter 4 Dynamic Programming

### Policy Evaluation(Prediction)

### Policy Improvement

### Policy Iteration

### Value Iteration

### Asynchronous Dynamic Programming

### Generalized Policy Iteration

### Efficiency of Dynamic Programming

### Summary

## Chapter 5 Monte Carlo Methods

### Monte Carlo Prediction

### Monte Carlo Estimation of Action Values

### Monte Carlo Control

### Monte Carlo Control without Exploring Starts

### Off-policy Prediction via Improtance Sampling

### Incremental Implementation

### Off-policy Monte Carlo Control

### Discounting-aware Importance Sampling

### Per-decision Importance Sampling

### Summary

## Chapter 6 Temporal-Difference Learning

### TD Prediction

### Advantages of TD Prediction Methods

### Optimality of TD(0)

### Sarsa: On-policy TD Control

### Q-learning: Off-policy TD Control

### Expected Sarsa

### Maximization Bias and Double Learning

### Games, Afterstates, and Other Special Cases

### Summary

## Chapter 7 n-step Bootstrapping

### n-step TD Prediction

### n-step Sarsa

### n-step Off-policy Learning

### Per-decision Methods with Control Variates

### Off-policy Learning Without Importance Sampling: The n-step Tree Backup Algorithm

### A Unifying Algorithm: n-step $Q(\sigma)$

### Summary

## Chapter 8 Planning and Learning with Tabular Methods

### Models and Planning

### Dyna: Integrated Planning, Acting, and Learning

### When the Model Is Wrong

### Prioritized Sweeping

### Expected vs. Sample Updates

### Trajectory Sampling

### Real-time Dynamic Programming

### Planning at Decision Time

### Heuristic Search

### Rollout Algorithms

### Monte Carlo Tree Search

### Summary

# Approximate Solution Methods

## Chapter 9 On-policy Prediction with Approximation

### Value-function Approximation

### The Prediction Objective(VE)

### Stochastic-gradient and Semi-gradient Methods

### Linear Methods

### Feature Construction for Linear Methods

#### Polynomials

#### Fourier Basis

#### Coarse Coding

#### Tile Coding

#### Radial Basis Functions

### Selecting Step-size Parameters Manually

### Nonlinear Function Approximation: Artiﬁcial Neural Networks

### Least-Squares TD

### Memory-based Function Approximation

### Kernel-based Function Approximation

### Looking Deeper at On-policy Learning: Interest and Emphasis

### Summary

## Chapter 10 On-policy Control with Approximation

### Episodic Semi-gradient Control

### Semi-gradient n-step Sarsa

### Average Reward: A New Problem Setting for Continuing Tasks

### Deprecating the Discounted Setting

### Differential Semi-gradient n-step Sarsa

### Summary

## Chapter 11 Off-policy Methods with Approximation

### Semi-gradient Methods

### Examples of Off-policy Divergence

### The Deadly Triad

### Linear Value-function Geometry

### Gradient Descent in the Bellman Error

### The Bellman Error is Not Learnable

### Gradient-TD Methods

### Emphatic-TD Methods

### Reducing Variance

### Summary

## Chapter 12 Eligibility Traces

### The $\lambda$-return

### TD($\lambda$)

### n-step Truncated $\lambda$-return Methods

### Redoing Updates: Online $\lambda$-return Algorithm

### True Online TD($\lambda$)

### Dutch Traces in Monte Carlo Learning

### Sarsa($\lambda$)

### Variable $\lambda$ and $\gamma$

### Off-policy Traces with Control Variates

### Watkins’s Q($\lambda$) to Tree-Backup( $\lambda$)

### Stable Off-policy Methods with Traces

### Implementation Issues

### Conclusions

## Chapter 13 Policy Gradient Methods

### Policy Approximation and its Advantages

### The Policy Gradient Theorem

### REINFORCE: Monte Carlo Policy Gradient

### REINFORCE with Baseline

### Actor–Critic Methods

### Policy Gradient for Continuing Problems

### Policy Parameterization for Continuous Actions

### Summary

# Looking Deeper

## Chapter 14 Psychology

### Prediction and Control

### Classical Conditioning

#### Blocking and Higher-order Conditioning

#### The Rescorla–Wagner Model

#### The TD Model

#### TD Model Simulations

### Instrumental Conditioning

### Delayed Reinforcement

### Cognitive Maps

### Habitual and Goal-directed Behavior

### Summary

## Chapter 15 Neuroscience

### Neuroscience Basics

### Reward Signals, Reinforcement Signals, Values, and Prediction Errors

### The Reward Prediction Error Hypothesis

### Dopamine

### Experimental Support for the Reward Prediction Error Hypothesis

### TD Error/Dopamine Correspondence

### Neural Actor–Critic

### Actor and Critic Learning Rules

### Hedonistic Neurons

### Collective Reinforcement Learning

### Model-based Methods in the Brain

### Addiction

### Summary

## Chapter 16 Applications and Case Studies

### TD-Gammon

### Samuel’s Checkers Player

### Watson’s Daily-Double Wagering

### Optimizing Memory Control

### Human-level Video Game Play

### Mastering the Game of Go

#### AlphaGo

#### AlphaGo Zero

### Personalized Web Services

### Thermal Soaring

## Chapter 17 Frontiers

### General Value Functions and Auxiliary Tasks

### Temporal Abstraction via Options

### Observations and State

### Designing Reward Signals

### Remaining Issues

### The Future of Artiﬁcial Intelligence
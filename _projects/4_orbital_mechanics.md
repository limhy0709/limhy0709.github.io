---
layout: page
title: Lagrange Point Station-Keeping
description: Numerical study of orbital stability in the restricted three-body problem
img: assets/img/orbits.jpg
importance: 4
category: simulation
---

## Motivation

This is where my interest in spaceflight started. The collinear Lagrange points ($$L_1$$, $$L_2$$, $$L_3$$) of the circular restricted three-body problem are equilibrium solutions, but they are **unstable** — a spacecraft placed there diverges without continuous correction. The apparent contradiction between "equilibrium point" and "requires active control" is what first made me think of orbital mechanics as a control problem rather than a geometry problem.

## Contents

- Numerical integration of the circular restricted three-body problem (CR3BP)
- Location and linear stability analysis of the five Lagrange points
- Halo orbit families around $$L_1$$ / $$L_2$$
- Station-keeping $$\Delta v$$ budget under perturbations

## Method

[사용한 도구와 방법. 예: Python + SciPy, 무차원화된 회전 좌표계에서 운동방정식 적분, 야코비 상수를 이용한 수치 검증.]

The equations of motion in the rotating frame:

$$
\ddot{x} - 2\dot{y} = \frac{\partial U}{\partial x}, \quad
\ddot{y} + 2\dot{x} = \frac{\partial U}{\partial y}, \quad
\ddot{z} = \frac{\partial U}{\partial z}
$$

## Results

[궤적 플롯, 안정성 해석 결과. 고유값의 실수부가 양수라는 것을 보여주는 그림이 있으면 좋습니다.]

## Connection to current work

The instability of these points is structurally the same problem I now work on in [thrust vector control](/projects/1_tvc_rocket/): an equilibrium that exists mathematically but cannot be held without active feedback. The scale and hardware differ; the control problem does not.

## Code

[GitHub 저장소 링크]

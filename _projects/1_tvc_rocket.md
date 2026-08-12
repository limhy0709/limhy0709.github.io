---
layout: page
title: Thrust Vector Control Rocket
description: Designing a gimbaled TVC system for active attitude stabilization
img: assets/img/tvc.jpg
importance: 1
category: rocketry
---

**Status:** In progress · Target flight demonstration: [2027]

## Motivation

A rocket is aerodynamically unstable during powered ascent unless it is actively controlled. Thrust vector control addresses this directly: by gimbaling the engine, the thrust vector itself becomes the control input. This is the problem I find most interesting in launch vehicle engineering — not producing thrust, but pointing it.

## Objectives

- Design and fabricate a two-axis gimbal mount for [엔진 종류 / 모터 규격]
- Implement an attitude estimation pipeline from IMU measurements
- Develop and tune a closed-loop controller for pitch and yaw
- Demonstrate stable vertical flight and controlled recovery

## Approach

**Plant modeling.** Deriving the rigid-body attitude dynamics of the vehicle under gimbaled thrust, linearized about the vertical ascent condition.

**Control design.** Starting from a PID structure for the inner attitude loop, with [LQR / cascaded control] as the target once the plant model is validated.

**Hardware.** Two-axis gimbal actuated by [서보 모델], flight computer based on [보드명], attitude estimation via [IMU 모델] with a complementary/Kalman filter.

**Validation.** Static gimbal bench testing → hardware-in-the-loop simulation → tethered hop test → free flight.

## Current progress

- [ ] Gimbal mechanism CAD and structural analysis
- [ ] Actuator selection and bench characterization
- [ ] 6-DOF flight simulation environment
- [ ] Controller implementation and HIL testing
- [ ] Flight test

## Notes

[진행하면서 배운 점, 설계 판단, 실패 사례를 여기에 계속 기록하세요. 대학원 지원 시 이 로그 자체가 강력한 증거가 됩니다.]

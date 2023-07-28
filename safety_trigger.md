# Vehicle Safety Metrics for Triggering Recordings

## Introduction

This document outlines two key safety metrics used for triggering a recording when a vehicle is in a "dangerous longitudinal state." The dangerous state is defined based on two metrics to assess the collision state of the lanes:

1. Required Longitudinal Acceleration (ALongReq)
2. Time To Collision (TTC)

## Metric 1: Required Longitudinal Acceleration (ALongReq)

Required Longitudinal Acceleration (ALongReq) is a metric that measures the maximum longitudinal backward acceleration required, on average, by one actor ($A_1$) to avoid a collision with another actor ($A_2$) in the future. This can be formalized as follows:

$$a_{\text{long, req}}(A_1, A_2, t) = \max {a_{1, \text{long}} \le 0 \mid \forall , \tilde{t} \ge 0: d(p_1(t + \tilde{t}), p_2(t + \tilde{t})) > 0}$$

In simpler terms, it calculates the maximum acceleration (negative) needed by A_1 to avoid a collision with A_2 in the future, assuming that A_1 and A_2 continue their current paths and velocities.

For constant acceleration scenarios, the concept of ALongReq is also known as Deceleration Rate To Avoid Crash (DRAC). When considering constant acceleration, the ALongReq can be calculated as follows:

$$a_{\text{long, req}}(A_1, A_2, t) = \min \left( a_{2, \text{long}} + \frac{(v_{1, \text{long}}(t) - v_{2, \text{long}}(t))^2}{2d(p_1(t), p_2(t))}, 0 \right)$$

Where:
- $a_{1, \text{long}}$ and $a_{2, \text{long}}$ are the longitudinal accelerations of actors $A_1$ and $A_2$, respectively.
- $v_{1, \text{long}}(t)$ and $v_{2, \text{long}}(t)$ are the longitudinal velocities of $A_1$ and $A_2$ at time $t$, respectively.
- $d(p_1(t), p_2(t))$ is the distance between $A_1$ and $A_2$ at time $t$.


## Metric 2: Time To Collision (TTC)

Time To Collision (TTC) is the second risk metric used to trigger a recording when a collision is likely.
It calculates the time required for two vehicles ($A_1$ and $A_2$) to collide if they continue on their present speed and path. The TTC is defined as:

The time to collision $\(t_{\mathrm{TTC}}\)$ is defined as follows:

$$
t_{\mathrm{TTC}} = \text{sol}_t\left[ \tilde{p}_x(t) = 0 \right]
$$

When calculating the time to collision, the relative acceleration is often assumed to be constant. With a constant acceleration decision model of the vehicle's motion, the solution to equation above reduces to solving:

$$0 = \tilde{p}{x, 0} + \tilde{v}{x, 0} t + \frac{\tilde{a}_{x, 0} t^2}{2}$$

The solution for $t_{\mathrm{TTC}}$ is given by:



Thus, $t_{\mathrm{TTC}}$ is given by the smallest positive solution. If $\tilde{v}_{x, 0}^2 - 2 \tilde{p}_{x, 0}\tilde{a}_{x, 0} < 0$ or $(\tilde{v}_{x, 0} \geq 0 and $\tilde{a}_{x, 0} \geq 0)$, no solution exists. This means that the velocity and acceleration are such that no collision occurs.

Please note that for some cases where the relative velocity or acceleration values are such that no collision is expected, no solution exists for the TTC calculation.
In some cases the TTC is negative if the preceding vehicle's speed is higher than that of the following vehicle. In this case, there is no crash risk.

## Conclusion

Both Required Longitudinal Acceleration (ALongReq) and Time To Collision (TTC) are essential safety metrics used to determine if a vehicle is in a dangerous state with respect to potential collisions.
By monitoring these metrics and comparing them to predefined thresholds, it becomes possible to trigger recordings and take appropriate safety measures when necessary.

## References

- Jansson, J. (2005). Collision Avoidance Theory: With application to automotive collision mitigation (Doctoral dissertation, Linköping University Electronic Press).
- Lin, Yuanfei, and Matthias Althoff. "CommonRoad-CriMe: A toolbox for criticality measures of autonomous vehicles." 2023 IEEE Intelligent Vehicles Symposium (IV). 2023.
- https://criticality-metrics.readthedocs.io/

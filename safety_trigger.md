# Vehicle Safety Metrics for Triggering Recordings

## Introduction
Coucou
This document outlines two key safety metrics used for triggering a recording when a vehicle is in a "dangerous longitudinal state." The dangerous state is defined based on two metrics to assess the collision state of the lanes:

1. Required Longitudinal Acceleration (ALongReq)
2. Time To Collision (TTC)

## Metric 1: Required Longitudinal Acceleration (ALongReq)

Required Longitudinal Acceleration (ALongReq) is a metric that measures the maximum longitudinal backward acceleration required, on average, by one actor ($A_1$) to avoid a collision with another actor ($A_2$) in the future. This comes down to measuring the longitudinal acceleration required to bring the relative velocity to zero at the time of the collision. This can be formalized as follows:

$`
a_{\text{long, req}}(A_1, A_2, t)=\text{sol}_{a_{\text{long, req}}}\left[
\left(\tilde{p}_x(t) \quad \tilde{v}_x(t)\right)^T= \left(0 \quad 0\right)^T\right]
`$

In simpler terms, it calculates the maximum acceleration (negative) needed by $A_1$ to avoid a collision with $A_2$ in the future, assuming that $A_1$ and $A_2$ continue their current paths and velocities.
$`\text{sol}_{a_{\text{long, req}}}\{\cdots\}`$ denotes the solution with respect to the host vehicle acceleration where $`\tilde{p}_x(t) \geq 0`$ for all $`t`$. With the assumption of constant acceleration the solution to is given by solving the equation system :

$`\eqalign{
0&=\tilde{v}_{x, 0}+\tilde{a}_{\text{long, req}} t \\
0&=\tilde{p}_{x, 0}+\tilde{v}_{x, 0} t+\frac{\tilde{a}_{\text{long, req}} t^2}{2}
}`$

This yields the constant acceleration required to avoid a collision, according to:

$`a_{\text{long, req}}(A_1, A_2, t) = \min \left( a_{2, \text{long}} - \frac{(v_{1, \text{long}}(t) - v_{2, \text{long}}(t))^2}{2d(p_1(t), p_2(t))}, 0 \right)`$

Where:
- $a_{1, \text{long}}$ and $a_{2, \text{long}}$ are the longitudinal accelerations of actors $A_1$ and $A_2$, respectively.
- $v_{1, \text{long}}(t)$ and $v_{2, \text{long}}(t)$ are the longitudinal velocities of $A_1$ and $A_2$ at time $t$, respectively.
- $d(p_1(t), p_2(t))$ is the distance between $`A_1`$ and $`A_2`$ at time $t$.

## Metric 2: Time To Collision (TTC)

Time To Collision (TTC) is the second risk metric used to trigger a recording when a collision is likely.
It calculates the time required for two vehicles ($A_1$ and $A_2$) to collide if they continue on their present speed and path. The TTC is defined as:

The time to collision $\(t_{\mathrm{TTC}}\)$ is defined as follows:

$`
t_{\mathrm{TTC}} = \text{sol}_t\left[ \tilde{p}_x(t) = 0 \right]
`$

When calculating the time to collision, the relative acceleration is often assumed to be constant. With a constant acceleration decision model of the vehicle's motion, the solution to equation above reduces to solving:
$`0 = \tilde{p}_{x, 0} + \tilde{v}_{x, 0} t + \frac{\tilde{a}_{x, 0} t^2}{2}`$

where $`\tilde{p}_{x, 0}`$ is the relative distance, $`\tilde{v}_{x, 0}`$ is the relative velocity and $`\tilde{a}_{x, 0}`$ the relative acceleration.
The solution for $`t_{\mathrm{TTC}}`$ is given by:

$`\eqalign{ t_{\mathrm{TTC}} 
&=-\frac{\tilde{p}_{x, 0}}{\tilde{v}_{x, 0}},\quad if \quad \tilde{v}_{x, 0}<0 \quad \text{and} \quad \tilde{a}_{x, 0}=0, \\
&=-\frac{\tilde{v}_{x, 0}}{\tilde{a}_{x, 0}}-\frac{\sqrt{\tilde{v}_{x, 0}^2-2 \tilde{p}_{x, 0} \tilde{a}_{x, 0}}}{\tilde{a}_{x, 0}},\quad if \quad  \tilde{v}_{x, 0}<0 \quad \text{and} \quad \tilde{a}_{x, 0} \neq 0\\
&=-\frac{\tilde{v}_{x, 0}}{\tilde{a}_{x, 0}}+\frac{\sqrt{\tilde{v}_{x, 0}^2-2 \tilde{p}_{x, 0} \tilde{a}_{x, 0}}}{\tilde{a}_{x, 0}},\quad if \quad \tilde{v}_{x, 0} \geq 0 \quad \text{and} \quad \tilde{a}_{x, 0}<0 \\
&=\text{Undefined}, \quad if \quad \tilde{v}_{x, 0} \geq 0 \quad \text{and} \quad \tilde{a}_{x, 0} \geq 0 \\ 
&=\text{Undefined}, \quad if \quad \tilde{v}_{x, 0}^2-2 \tilde{p}_{x, 0} \tilde{a}_{x, 0} < 0 
}`$

Thus, $t_{\mathrm{TTC}}$ is given by the smallest positive solution. 
If $`\tilde{v}_{x, 0}^2 - 2 \tilde{p}_{x, 0}\tilde{a}_{x, 0} < 0`$ or $`(\tilde{v}_{x, 0} \geq 0`$ and $`\tilde{a}_{x, 0} \geq 0)`$, no solution exists. This means that the velocity and acceleration are such that no collision occurs.

Please note that for some cases, in this formalisation of TTC, the value is negative if the preceding vehicle's speed is higher than that of the following vehicle. In this case, there is no crash risk.

Note: For comparison the classical definition of the TTC is also computed (i.e. $`\frac{\tilde{p}_{x, 0}}{|\tilde{v}_{x, 0}|}`$)  

## Conclusion

Both Required Longitudinal Acceleration (ALongReq) and Time To Collision (TTC) are essential safety metrics used to determine if a vehicle is in a dangerous state with respect to potential collisions.
By monitoring these metrics and comparing them to predefined thresholds, it becomes possible to trigger recordings and take appropriate safety measures when necessary.

## References

- Jansson, J. (2005). Collision Avoidance Theory: With application to automotive collision mitigation (Doctoral dissertation, Linköping University Electronic Press).
- Lin, Yuanfei, and Matthias Althoff. "CommonRoad-CriMe: A toolbox for criticality measures of autonomous vehicles." 2023 IEEE Intelligent Vehicles Symposium (IV). 2023.
- https://criticality-metrics.readthedocs.io/

- test Z

#######################################################################
To summarize the provided Python code into equations, we'll extract the main calculations and control logic expressed in mathematical terms. The code primarily deals with longitudinal control for an autonomous vehicle, focusing on maintaining safe distances, managing speeds, and optimizing for smooth acceleration and braking. Here are the main equations and functions translated from the code:

### Parameters and Constants
- \( G \): Gravitational constant
- \( N = 12 \): Number of prediction steps
- \( \text{MAX\_T} = 20.0 \): Maximum prediction time
- \( T_{\text{IDXs}} \): Array of time indices over the prediction horizon
- \( T_{\text{DIFFs}} \): Differences in \( T_{\text{IDXs}} \)
- Various cost constants for different states and control inputs

### Functions
1. **Coasting Acceleration**:
   \[
   a_{\text{coast}}(v_{\text{ego}}, \alpha, \text{friction}, \text{drag}, c_0) = \sin(\alpha)G + \cos(\alpha)G \cdot \text{friction} + \text{drag} \cdot v_{\text{ego}}^2 + c_0 \cdot v_{\text{ego}}
   \]

2. **Distance Offset**:
   \[
   \text{dist\_offset}(v_{\text{ego}}, v_{\text{lead}}, a) = \frac{v_{\text{ego}}^2 - v_{\text{lead}}^2}{2a} - \left(\frac{v_{\text{ego}} - v_{\text{lead}}}{a}\right) \cdot v_{\text{lead}}
   \]

3. **Maximum Time Follow Offset**:
   \[
   \text{max\_t\_follow\_offset}(v, a_{\text{coast}}) = \frac{a_{\text{coast}} \cdot t_{\text{max}}^2}{v + 1.0} \quad \text{where } t_{\text{max}} = 10.0
   \]

4. **Static Distance**:
   \[
   \text{static\_distance}(v_{\text{ego}}, t_{\text{dist}}, \text{stop\_dist}) = v_{\text{ego}} \cdot t_{\text{dist}} + \text{stop\_dist}
   \]

### Model Dynamics
- State vector: \( \mathbf{x} = [x_{\text{ego}}, v_{\text{ego}}, a_{\text{ego}}] \)
- Control input: \( \mathbf{u} = [j_{\text{ego}}] \)
- Dynamics model: 
  \[
  \mathbf{\dot{x}} = \begin{bmatrix} v_{\text{ego}} \\ a_{\text{ego}} \\ j_{\text{ego}} \end{bmatrix}
  \]

### Cost Function
The cost function is designed to minimize the deviation from desired states and control inputs:
\[
\text{cost} = \sum_{i=0}^{N-1} \left[ \frac{(x_{\text{obstacle}} - x_{\text{ego}} - \text{desired\_follow\_distance})}{v_{\text{ego}} + 10} \right]^2 + \left[ \frac{a_{\text{ego}} + a_{\text{coast}}}{v_{\text{ego}} + 10} \right]^2 + 20(a_{\text{ego}} - \text{prev\_a})^2 + j_{\text{ego}}^2
\]

### Constraints
1. **Speed and Acceleration Limits**:
   \[
   \begin{cases}
   v_{\text{ego}} \\
   a_{\text{ego}} - a_{\min} \\
   a_{\max} - a_{\text{ego}} \\
   \frac{(x_{\text{obstacle}} - x_{\text{ego}} - \text{start\_braking\_dist})}{v_{\text{ego}} + 10} \\
   \text{decel}^3 + \frac{a_{\text{ego}} + a_{\text{coast}}}{\sqrt{(a_{\text{ego}} + a_{\text{coast}})^2 + 1}} \\
   \frac{(x_{\text{obstacle}} - x_{\text{ego}} - \text{stop\_distance})}{v_{\text{ego}} + 10} \\
   a_{\text{ego}} \cdot v_{\text{lead\_relation}}
   \end{cases}
   \]

2. **Maximum Deceleration**:
   \[
   \text{decel} = \frac{a_{\text{ego}} + a_{\text{coast}}}{-a_{\min\_limit} - \text{MPC\_A\_COAST\_MAX\_OFFSET} - a_{\text{coast}}}
   \]

### Extrapolation
1. **Lead Vehicle**:
   \[
   \text{lead\_xv}(x_{\text{lead}}, v_{\text{lead}}, a_{\text{lead}}, a_{\text{lead\_tau}}) = \left[ \text{cumsum}(T_{\text{DIFFs}} \cdot v_{\text{lead\_traj}}), \text{cumsum}(T_{\text{DIFFs}} \cdot a_{\text{lead\_traj}}) \right]
   \]

2. **Ego Vehicle**:
   \[
   \text{ego\_traj}(v_{\text{ego}}, a_{\min}) = \left[ \text{cumsum}(T_{\text{DIFFs}} \cdot v_{\text{ego\_traj}}), \text{cumsum}(T_{\text{DIFFs}} \cdot a_{\text{ego\_traj}}) \right]
   \]

### Combining and Selection
The obstacle selection and source determination use a combination of the above distances and velocities to select the appropriate source (cruise, lead, curvature) and compute the desired trajectories for the ego vehicle.

This provides a comprehensive overview of the mathematical formulations and logical steps involved in the given Python code for longitudinal control of an autonomous vehicle.

# Stanley Control

## Definitions
- **Pose**: Describes the current position and orientation of the vehicle.
  - $(x, y, \theta)$: where  $x$ and $y$ are coordinates, and $\theta$ is the heading angle.
- **Speed** $(v)$: The current velocity of the vehicle.
- **Yaw Rate** $(\dot{\theta})$: The rate of change of the heading angle.

## Main Variables
- **Trajectory Error**:
  - $e_{\text{lat}}$: Lateral error (distance from the path).
  - $e_{\text{ang}}$: Angular error (difference between the desired heading and current heading).

- **Steering Command** $\delta$: The angle to steer.
- **Velocity Command** $v_{\text{cmd}}$: The velocity command issued.

---

## 1. Expected Yaw Rate
The expected yaw rate $\dot{\theta}_{\text{expected}}$, is calculated from the headings of two consecutive points on the path $(pt_1$ and $pt_2)$:

```math
\dot{\theta}_{\text{expected}} = \frac{\Delta \theta_{\text{path}}}{\frac{d_{\text{curv}}}{v}}
```

Where:
- $\Delta \theta_{\text{path}}$ = $\text{angleShortestDist}(pt1.hdg, pt2.hdg)$
- $d_{\text{curv}} = pt_2.curvilinear - pt_1.curvilinear$
- $v$: current speed

---

## 2. Steering Computation

The total steering command $\delta\$ is the sum of several components:

```math
\delta = \delta_{\text{lat}} + \delta_{\text{yaw\_damp}} + \delta_{\text{hdg\_yaw}} + \delta_{\text{integrator}}
```

Each component is described below:

### 2.1 Lateral Error Correction

```math
\delta_{\text{lat}} = -\tan^{-1} \left( \frac{k_{\text{lat}} \cdot e_{\text{lat}}}{\max(k_{\text{soft}}, v)} \right)
```

Where:
- $k_{\text{lat}}$: Lateral correction coefficient.
- $k_{\text{soft}}$: Speed softening coefficient to prevent high steering at low speeds.
- $e_{\text{lat}}$: Lateral error.

### 2.2 Yaw Rate Damping

```math
\delta_{\text{yaw\_damp}} = k_{\text{yaw\_rate}} \cdot (\dot{\theta} - \dot{\theta}_{\text{expected}})
```

Where:
- $k_{\text{yawRate}}$: Yaw rate damping coefficient.
- $\dot{\theta}$: Current yaw rate.
- $\dot{\theta}_{\text{expected}}$: Expected yaw rate (calculated above).

### 2.3 Heading and Yaw Rate Correction

```math
\delta_{\text{hdg\_yaw}} = e_{\text{ang}} + k_{\text{yaw}} \cdot v \cdot \dot{\theta}_{\text{expected}}
```

Where:
- $k_{\text{yaw}}$: Heading error correction coefficient.
- $e_{\text{ang}}$: Angular error.

### 2.4 Steering Integrator

```math
\delta_{\text{integrator}} = \text{integrator}(t, e_{\text{lat}}, 0)
```

Where the steering integrator computes a correction term based on the lateral error over time.

---

## 3. Steering Limit

The steering angle is limited based on speed to avoid instability:

```math
\delta_{\text{limit}} = \min\left( \arcsin\left( \frac{v_{\text{lat\_max}}}{v} \right), \delta_{\text{max}} \right)
```

Where:
- $v_{\text{latMax}}$: Maximum allowable lateral velocity.
- $v$: Current speed.
- $\delta_{\text{max}}$: Maximum allowable steering angle.

---

## 4. Final Commands

### 4.1 Limited Velocity Command

```math
v_{\text{cmd}} = \text{clamp}(v_{\text{trajectory}}, v_{\text{min}}, v_{\text{max}})
```

Where:
- $v_{\text{trajectory}}$: Desired linear speed from the trajectory.
- $v_{\text{min}}$, $v_{\text{max}}$: Minimum and maximum velocity limits.

### 4.2 Limited Steering Command

```math
\delta_{\text{cmd}} = \text{clamp}(\delta, -\delta_{\text{limit}}, \delta_{\text{limit}})
```

Where:
- $\delta_{\text{limit}}$: Steering limit from the speed-dependent limit.

---

## State Transitions

The vehicle state transitions are based on errors:

- **Stop**: If the curvilinear distance error is below the threshold:
  ```math
  |d_{\text{curvilinear}}| \leq \text{arrival\_curvilinear\_threshold}
  ```

- **ErrorAngular**: If the angular error exceeds the threshold:
  ```math
  |e_{\text{ang}}| > e_{\text{ang\_max}}
  ```

- **ErrorLateral**: If the lateral error exceeds the threshold:
  ```math
  |e_{\text{lat}}| > e_{\text{lat\_max}}
  ```

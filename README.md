# CS272_CustomNarrow_Env
narrow-safe-v0 Environment
--------------------------

A custom Highway-env based scenario modeling safe navigation through a narrow urban street with parked vehicles and potential oncoming traffic. The environment emphasizes safety, smoothness, and realistic overtaking behavior under constrained road geometry.

1. Overview
-----------
`narrow-safe-v0` simulates a two-lane, narrow roadway where:
- The right lane contains parked vehicles blocking the path.
- The left lane may contain oncoming traffic.
- The agent must safely overtake obstacles, maintain lane discipline, and reach the goal without collisions.
- This version is the “safe” variant, preventing unfair penalties during brief unavoidable blockages.

2. Objective
------------
The agent must:
- Drive forward toward the goal.
- Avoid collisions with parked or moving vehicles.
- Maintain safe headway.
- Perform smooth and necessary lane changes.
- Avoid oscillations or stalling.
- Complete the scenario safely and efficiently.

3. Action Space (Discrete)
--------------------------
0 → Maintain speed  
1 → Accelerate  
2 → Decelerate / Brake  
3 → Lane change left  
4 → Lane change right  

Illegal lane changes have no effect unless an actual collision occurs.

4. Observation Space
---------------------
A numerical feature vector including:
- Ego vehicle speed, lane index, and position.
- Distances to parked vehicles.
- Distances and relative velocities of lead/oncoming vehicles.
- Lane-change feasibility flags.
- Distances to road boundaries.

A SafeObs wrapper:
- Removes NaNs/Infs,
- Clips extreme values to ±1e6.

5. Reward Function
-------------------
Rewards and penalties:
- + Forward progress
- + Safe headway (>1s)
- – Unsafe headway (<1s)
- – Collisions
- – Excessive lane changes
- – Abrupt maneuvers
- Neutral penalty for unavoidable temporary blockage

6. Termination Conditions
--------------------------
- Success (reaches the end)
- Collision
- Timeout (max steps)

7. Constraints and Caveats
---------------------------
- Narrow lanes restrict maneuverability.
- Parked vehicles force overtaking.
- Oncoming vehicles add dynamic hazard.
- Brief “no-free-lane” cases do not penalize the agent.
- Driving off-road is not allowed.

8. Usage
---------
Registration:
import narrow_safe_env

Create env:
env = gym.make("narrow-safe-v0")

Render:
env = gym.make("narrow-safe-v0", render_mode="human")

Loop:
obs, info = env.reset()
while not done:
    action = env.action_space.sample()
    obs, reward, done, truncated, info = env.step(action)

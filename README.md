
# Linear flocking on NetLogo

<p align="center">
  <span>English</span> |
  <a href="README.es-ES.md">Español</a>
</p>

Multi-agent system implemented in NetLogo that simulates a flock flight following a linear formation.

The implemented algorithm determines on each tick how far an agent should advance and in which direction.

With proper calibration, the agents follow a linear formation around a lead agent.

<p align="center">
<img src="images/flocking.gif" alt="Demo" width="250"/>
</p>

# Algorithm

For each tick, the agent with the lowest Id is chosen as the leader.

If the agent is not the leader, it searches for the nearest agent within a radius and calculates, for each rule, the displacement vector (relative to the agent) that would make it comply with that rule. In this way the vectors $\vec{R1}$, $\vec{R2}$, $\vec{R3}$ and $\vec{R4}$ are obtained. Then a weighted average of these vectors ($\vec{goal}$) is made. The direction of $\vec{goal}$ indicates the direction in which the agent should move and its magnitude indicates how strongly it should make the move (or how far it is from satisfying the rules). Then the degrees that the agent should rotate so that its direction matches $\vec{goal}$ are calculated.

To avoid erratic behavior, before applying this rotation, the number of degrees is clamped to a maximum value. Once the agent points to the correct direction the agent advances a distance that is calculated as a minimum velocity (in distance/tick) + $||\vec{goal}||$. Finally, if it is time to take a capture of the terrain, the agent will do so.

If the agent is leading, it will move independently with a rotation and speed that can be controlled from the NetLogo interface.

This algorithm is inspired by this [paper](https://www.sciencedirect.com/science/article/abs/pii/S1084804517302606).

<p align="center">
<img src="images/pseudocode.png" alt="Pseudocode" width="350"/>
</p>

## Target vectors

The target vectors of each rule are calculated as follows:

- To compute $\vec{R1}$ we first obtain the vector from the nearest agent to our position ($\vec{v}$). If $\lVert \vec{v} \rVert$ is less than the minimum distance, then $\vec{R1}$ will have the direction of $\vec{v}$ and its magnitude, if normalized magnitudes are not used, will be the difference between the minimum distance and $\lVert \vec{v} \rVert$. If $\lVert \vec{v} \rVert$ is greater than or equal to the minimum distance, no action should be taken and $\vec{R1}$ will be $(0,0)$.

- $\vec{R2}$ has the direction the agent should point towards so that its rotation is the same as the average rotation of its neighbors. If normalized magnitudes are not used, the magnitude of $\vec{R2}$ will be the ratio between the number of degrees to rotate and a constant value.

- $\vec{R3}$ has the direction from the agent to the average position of the neighboring agents. If normalized vectors are not used, its magnitude will be the distance from the agent to that mean position.

- $\vec{R4}$ has the direction from the agent to the projection of the agent's position on the infinite line crossing the leading agent. If normalized vectors are not used, its magnitude will be the distance from the agent to that projection.

## UI

The following parameters can be modified from the NetLogo UI:

- Number of agents in the simulation.

- Maximum distance that each agent can see.

- Maximum rotation of an agent.

- Minimum and maximum speed of the agents.

- Direction and velocity of the leading agent.

- Weight of each target vector in the weighted average ($\vec{goal}$).

- Normalization of target vectors.

- Influence of $\lVert\vec{goal}\rVert$ on velocity.

- Minimum distance (rule R1).

From the NetLogo interface it is possible to remove any agent or remove the current lead agent to check that the algorithm is fault resistant and to observe how the agent fleet is reorganized to fill the gap left by a removed agent or how a new leader is choosen if necessary.

It is also possible to enable/disable and visualize the monitoring of the agents on the terrain by marking the terrain cells with a grey level that depends on how many times it has been sampled by an agent. 

The following figure shows a terrain being scanned by the agents.

<p align="center">
<img src="images/netlogo-scan.png" alt="Scan" width="250"/>
</p>

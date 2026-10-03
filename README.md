# Rock-Paper-Scissors

An agent-based model (ABM), built in [NetLogo](https://ccl.northwestern.edu/netlogo/), that turns rock, paper, scissors into a predator-prey simulation. Three teams of agents wander a shared world. When two of them collide, the loser turns into the winner's type. The model is a final project for LIS 4930. It asks how two "unfair" settings change which team ends up on top:

1. **Starting population:** what happens when the scissors team starts out smaller or larger than the others?
2. **Vision radius:** what happens when scissors can *see* nearby agents and chase their prey or run from their predator?

## Repository contents

| File | Description |
| --- | --- |
| `Final-Project.nlogox` | The NetLogo model: code, interface and BehaviorSpace experiments |
| `LIS 4930 - ODD.pdf` | Model description using the ODD (Overview, Design concepts, Details) protocol |
| `LIS 4930 - Analysis Report.pdf` | Research questions, methodology, results and conclusions |
| `LIS 4930 - Rock, Paper, Scissors.pdf` | Presentation slides |

## Download and run

### 1. Install NetLogo

The model was built with **NetLogo 7.0.3** and saved in the `.nlogox` format. That format needs **NetLogo 7.0 or newer**; NetLogo 6.x can't open it.

1. Go to the [NetLogo download page](https://ccl.northwestern.edu/netlogo/download.shtml).
2. Download the installer for your operating system (Windows, macOS or Linux) and install it.

### 2. Get the project

**Option A: clone with Git**

```bash
git clone https://github.com/KaylenTon/Rock-Paper-Scissors.git
cd Rock-Paper-Scissors
```

**Option B: download a ZIP.** On the GitHub page, click **Code → Download ZIP**, then extract the ZIP.

### 3. Open and run the model

1. Launch NetLogo, choose **File → Open…**, and select `Final-Project.nlogox`. Double-clicking the file also works if NetLogo is set as the default program for it.
2. On the **Interface** tab, set the sliders (see [Interface controls](#interface-controls)).
3. Click **set up** to place the agents.
4. Click **go** to start the simulation. Click **go** again to pause it.
5. Watch the world view and the **Population Counts** plot. The run stops by itself once one team has converted every agent, or when it reaches 1500 ticks.

To run again, click **set up** and then **go**.

## How the model works

### Agents and environment

There are three types of turtle agents:

| Team | Color | Beats | Loses to |
| --- | --- | --- | --- |
| Rock | Gray | Scissors | Paper |
| Paper | White | Rock | Scissors |
| Scissors | Red | Paper | Rock |

- The world runs from **-20 to 20** on both axes, with the origin at the center.
- The world **wraps** both horizontally and vertically, so an agent that leaves one edge comes back on the opposite edge.
- All agents have the same speed and size.
- Time moves in discrete ticks.

### Procedures

- **Movement:** By default, each agent moves forward one step per tick. Agents start at random positions facing random directions.
- **Scissors vision** (only when `scissors-vision-on?` is on): each scissors agent looks within its vision radius.
  - If it sees a **rock** (predator), it turns away and flees.
  - If it sees **paper** (prey) and no rock, it faces the paper and chases it.
- **Interaction:** when two agents of different types are within 1 patch of each other, the loser converts to the winner's type, changing both its type and its color:
  - Rock + Scissors → Scissors becomes Rock
  - Scissors + Paper → Paper becomes Scissors
  - Paper + Rock → Rock becomes Paper
- **Tracking:** after every tick, the model counts the agents on each team and plots the counts.
- **Check winner:** the run ends when one team makes up the whole population. If no team takes over within **1500 ticks**, the run ends as a `timeout`.

### Interface controls

| Control | Type | Range | Default | Purpose |
| --- | --- | --- | --- | --- |
| `set up` | Button | – | – | Clears the world and spawns the agents |
| `go` | Button (forever) | – | – | Runs or pauses the simulation |
| `rock-start` | Slider | 1–50 | 20 | Starting number of rocks |
| `paper-start` | Slider | 1–50 | 20 | Starting number of papers |
| `scissors-start` | Slider | 1–50 | 20 | Starting number of scissors |
| `scissors-vision-on?` | Switch | on / off | off | Turns scissors vision on or off |
| `scissors-vision` | Slider | 0–30, steps of 5 | 0 | Scissors vision radius, in patches |
| Population Counts | Plot | – | – | Size of each team over time |

## Experiments

Both experiments are saved as **BehaviorSpace** experiments in the model file. To run one, open **Tools → BehaviorSpace** in NetLogo. Each experiment records `winner-type`, `ticks`, `rock-count`, `paper-count` and `scissors-count`.

| | Experiment A: starting populations | Experiment B: vision radius |
| --- | --- | --- |
| Rock / paper start | 20 each | 30 each |
| Scissors start | 10, 20, 30, 40, 50 | 30 |
| Scissors vision | Off | On, radius 0, 10, 20, 30 |
| Runs per condition | 10 | 10 |

> **Note:** The saved experiments hold the settings from the *last* condition that was run. Experiment A is set to `scissors-start = 50`, and Experiment B is set to `scissors-vision = 30`. To reproduce every condition, edit the experiment and change the value, or list several values, for example `["scissors-start" 10 20 30 40 50]`. Experiment A's saved exit condition stops at 1000 ticks; the `go` procedure itself times out at 1500.

I tallied the winners from the BehaviorSpace output and charted them in R.

## Results

### RQ1: Scissors starting population

Wins out of 10 runs (rock and paper start at 20, vision off):

| Scissors start | Rock | Paper | Scissors | None (timeout) |
| --- | --- | --- | --- | --- |
| 10 | **6** | 1 | 3 | 0 |
| 20 | 2 | 2 | **6** | 0 |
| 30 | **4** | **4** | 1 | 1 |
| 40 | 3 | 2 | **4** | 1 |
| 50 | 3 | **5** | 2 | 1 |

- **Few scissors:** rock dominates.
- **Middle range:** the results are mixed and unstable. Scissors won most often at 20, the "fair" setting where every team starts with the same number.
- **Many scissors:** paper starts to win more often.

**Takeaway:** a bigger scissors team does *not* guarantee that scissors wins. Extra scissors eat up the paper, and paper is rock's only predator, so rock is left free to grow. In a cyclic competitive system, growing one team can end up strengthening its predator instead of itself.

### RQ2: Scissors vision radius

Wins out of 10 runs (all teams start at 30, vision on):

| Vision radius | Rock | Paper | Scissors | None (timeout) |
| --- | --- | --- | --- | --- |
| 0 | 2 | **5** | 3 | 0 |
| 10 | **7** | 0 | 2 | 1 |
| 20 | 2 | 3 | **4** | 1 |
| 30 | 1 | 0 | **9** | 0 |

- **0:** no strategy, so there is no consistent winner. Paper winning 5 of 10 is most likely random variation.
- **10:** scissors wipe out the paper, but the rocks are spread across the map and are hard to avoid with a short radius. With no paper left to check rock, rock takes over.
- **20:** a more balanced result, and scissors win most often.
- **30:** scissors win heavily. They move with intent: they keep turning and redirecting to avoid rock and target paper. Once most of the paper is gone, paper and rock fight it out while the scissors wait on the sidelines, and then the scissors take over whichever team is left.

**Takeaway:** vision is a big advantage. At high radii, scissors stop behaving randomly and start acting strategically, which breaks the natural rock-paper-scissors cycle. In informal testing outside the recorded experiments, high-vision scissors usually won even when they started with fewer agents.

## Future improvements

- Run more simulations per condition.
- Test wider parameter ranges.
- Use smaller increments when changing parameters.

## References

- Wilensky, U. (1999). *NetLogo* [Computer software]. Center for Connected Learning and Computer-Based Modeling, Northwestern University. http://ccl.northwestern.edu/netlogo/
- Wilensky, U. (1997). *NetLogo Wolf Sheep Predation model* [Computer software]. Center for Connected Learning and Computer-Based Modeling, Northwestern University. http://ccl.northwestern.edu/netlogo/models/WolfSheepPredation

The Wolf Sheep Predation model from the NetLogo Models Library inspired this project's predator-prey design.

## Author

Kaylen Ton, LIS 4930 final project

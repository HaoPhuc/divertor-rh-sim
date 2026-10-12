# divertor-rh-sim

Simulated remote handling of a tokamak divertor cassette. A robot arm in MuJoCo removes a cassette from its slot without touching the surrounding structure, and an automated agent loop tunes the motion run by run.

## Why

The divertor takes the highest heat and particle loads in a tokamak, so its components wear out and need replacing. By then the vessel is activated and no one can go inside, so the swap has to be done by remote handling. ITER's divertor, for example, is built from 54 cassettes that are exchanged this way. This project is a small-scale version of that problem: plan and control a removal that is collision-free, fast, and gentle on the arm.

## Approach

- **Simulation:** MuJoCo, with a Franka Panda arm from MuJoCo Menagerie. The cassette starts as a simple box in a slot.
- **Planner and controller:** written by hand. The arm follows waypoints under joint control.
- **Scoring:** any contact with the slot walls fails the run. Successful runs are scored on minimum clearance, completion time and peak joint torque. The box position is randomized by a few mm each run to mimic misalignment.
- **Agent loop:** an agent changes one parameter in `experiments/params.yaml`, runs a batch of randomized episodes, and keeps the change only if the mean score improves. Every run is logged to `experiments/results.csv`. The agent cannot edit the scoring or the planner code.

## Structure

```
models/scene.xml         arm, slot and cassette
src/planner.py           waypoints for the removal
src/controller.py        joint control
src/run.py               runs one episode and returns metrics
src/score.py             scoring (locked)
experiments/params.yaml  tunable parameters
experiments/results.csv  run log
tests/
AGENT.md                 rules for the agent loop
```

## Running

```
pip install -r requirements.txt
python src/run.py --params experiments/params.yaml
```

## Roadmap

1. Scene and one scripted removal that works
2. Scoring, randomization and the agent loop
3. Cassette-like geometry and a port opening instead of a box
4. Learned control (RL) compared against the tuned baseline

## Status

Work in progress, currently at step 1.

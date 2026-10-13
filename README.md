# absorption-instrument

Interactive companion to the KAUST–Aramco Edge AI poster. It shows how a self-adapting anomaly detector can slowly learn a developing fault as "normal", and a way to detect and undo that.

**Live demo:** https://msh3l0r-design.github.io/absorption-instrument/

> **Status:** companion material for a poster. All results come from simulation (see Limitations). The experiment code and raw data are not in this repository.

## What it shows

Anomaly detectors on industrial equipment usually keep updating their model of "normal" so they can follow ageing and drift. If a fault develops slowly *while* the detector is updating, the detector can absorb the fault into its model of normal and stop alarming on it.

- **`index.html`, Part 1 (absorption).** Slide the fraction of faulty data the detector learns from and watch its alarm strength for that fault fall, while alarm strength for faults it never saw stays high.
- **`index.html`, Part 2 (Fault 3).** One fault is almost invisible to the detector once plant-wide drift is present. A projection step that removes the drift direction, estimated from other machines, restores detection. The detector itself is unchanged.
- **`pair.html` (frozen reference).** Keep a copy of the detector frozen before adaptation and compare it with the live copy. Disagreement between the two flags a fault the live copy has absorbed. The same frozen copy gives the direction to subtract to "unlearn" it, with no retraining and no labels.

## What is measured and what is not

Process model: Tennessee Eastman Process (a standard simulation benchmark). Each experiment was run with 3 random seeds.

| Quantity | Value | Status in the demo |
|---|---|---|
| Alarm strength for the learned fault, 0% to 50% contaminated data | 0.97 to 0.70 | Measured (3-seed mean) at 0, 5, 15, 30 and 50%. **Slider positions in between are linear interpolation.** |
| Alarm strength for never-learned faults, same range | 0.99 to 0.90 | Same as above |
| Fault 3 detection, without vs with projection | 0 to 83 runs in 100 (85 in 100 with no drift) | Measured; result written down before the run |
| False-alarm rate with projection, on held-out machines | 11 in 100 (plausible range 6 to 19) | Measured on machines not used during development |
| Time to flag a re-emerged fault (frozen-vs-live disagreement) | about 9 minutes | Measured, for the family of faults that shift sensor readings |
| Detection AUC of a blinded detector after repair | 0.32 to 0.79 (retraining from scratch: 0.69) | **Only the two end values are measured; the slider is linear between them.** |

**Schematic, not data:** the scatter points and humps in `index.html`, and the time-series in `pair.html` Part 2, are synthetic drawings that illustrate the mechanism.

## Limitations

- Simulation only. Not yet validated on a real plant.
- 3 seeds per experiment.
- The frozen-copy method works for faults that shift sensor readings up or down. For faults spread thinly across many sensors, four approaches were tried against success criteria written down beforehand, and all four failed. Those cases are handled by other methods on the poster.
- The repair needs the detector's *own* frozen copy. A copy from a neighbouring machine does not work, and on an undamaged detector the repair does nothing.

## Running locally

The demo is static HTML with no build step. Open `index.html`, or run `python -m http.server` in this folder.

## Authors

Mishal Alotaishan, Ruslan Zhagypar, Nour Kouzayha, Tareq Al-Naffouri. Information Science Lab, KAUST, and UCLA Samueli. Hardware from Qualcomm.

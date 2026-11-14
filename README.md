# Quantum Satellite Tasking — QAOA for Disaster Response

An end-to-end Qiskit satellite-tasking prototype with simulator benchmarking and real IBM Quantum Qiskit hardware validation.


## Problem

I study satellite observation selection for disaster-response planning. Each
candidate task has a satellite, target, observation window, and illustrative
priority. The scheduler maximizes total priority while avoiding overlapping use
of a satellite or ground station and enforcing an at-most-one-observation-per-target
policy.

The reference scenario contains three satellites and eight observation
opportunities. Four additional generated scenarios vary priorities, timing, and
resource assignments. Battery constraints and realistic orbital propagation are
outside the current benchmark.

Eight binary decisions give 256 possible schedules. The search space grows
exponentially under exhaustive enumeration; this project does not establish a
limit on classical solvers.


## Method

1. Encode task selection using binary variables.
2. Represent pairwise conflicts directly as quadratic penalties.
3. Convert the QUBO to an Ising Hamiltonian using Qiskit.
4. Optimize QAOA parameters locally with COBYLA and `StatevectorSampler`.
5. Evaluate fixed optimized circuits on IBM `ibm_fez` using the client-side
   Sampler V2 interface.
6. Decode measurements and independently validate priority and feasibility.
7. Separately demonstrate scripted emergency-request replanning with an exact
   classical solver.


   ## Results

| Experiment | Configuration | Finding |
|---         |---            |---      |
| Four-task simulation | Small conflict-only benchmark | Earlier recorded experiment found priority 11 in 10/10 seeds |
| Five-scenario simulation | Eight tasks each, depth 2, five seeds per scenario | Optimum observed in 20/25 final sampling runs |
| Four-task hardware | Depth 1, 256 shots | Optimum in 33/256 shots: **12.89%**, versus **6.25%** uniform |
| Five-scenario hardware | Depth 1, 1,024 shots per scenario | Optimum observed on all five instances; optimal frequency exceeded uniform on three |
| Classical greedy | Same five eight-task scenarios | Matched every exact optimum |

The four-task hardware run had approximately 2.1 times the uniform
optimal-sampling frequency. Ideal-circuit and
hardware comparisons use matching parameters; the earlier depth-2 simulator
benchmark is a separate experiment.

## Contribution

My contribution is a reproducible satellite-tasking prototype that combines
conflict-based modeling, Qiskit QAOA, classical references, hardware execution,
and explicit schedule validation.

For the original four-task example, removing battery constraints verified to be
redundant preserves the feasible schedules and reduces the encoding from ten
qubits to four.

## Limitations

- Generated observation windows and illustrative priorities.
- No battery,separate downlink model.
- Hardware evaluated locally optimized circuits
- One hardware evaluation per benchmark instance; 

## Reproducibility

| Job | Workload | Charged QPU time |
|---|---|---:|
| `dbs9lbok09pc73avthe0` | One eight-task circuit, 256 shots | 2 seconds |
| `dbs9pmejjaos73ajua20` | One four-task circuit, 256 shots | 2 seconds |
| `hardware_results/five_scenarios_depth1_seed0/hardware_job.json` | Five eight-task circuits, 1,024 shots each | 4 seconds |

Backend: `ibm_fez`. All raw counts, manifests, ISA circuits, and usage metrics
are under `hardware_results/`.

**Versions:** qiskit 2.6.2, qiskit-algorithms 0.4.0, qiskit-optimization 0.7.0,
qiskit-ibm-runtime 0.52.1

## Files

| File | Purpose |
|------|---------|
| `slackfree.ipynb` | Canonical benchmark — slack-free formulation, QAOA success |
| `experiments.ipynb` | Five-scenario simulator results, replanning demo |
| `hardware_validation.ipynb` | IBM hardware jobs and comparison tables |
| `begin.ipynb` | Exploratory — diagnostic history of the benchmark-drift bug and VarQITE failure |
| `hardware_results/` | Raw IBM job data, counts, circuits |
| `scenario_report.json` | Original eight-task scenario + replanning |
| `multi_scenario_report.json` | Five scenarios × five seeds full metrics |
| `multi_scenario_summary.csv` | Summary table |

## That's it 

I have demonstrated an end-to-end Qiskit satellite-tasking prototype with simulator
benchmarking and real quantum hardware validation. The experiments establish
implementation feasibility while exposing variability and the strength of
classical baselines.

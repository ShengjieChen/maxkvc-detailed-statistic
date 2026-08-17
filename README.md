# Instance-wise computational results — Max *k*-VC

Detailed, instance-wise computational results for the paper
*A Polyhedral Study of the Maximum k-Vertex Cover Problem*. The column headers use the
same notation as the manuscript tables, so each file is the per-instance version of the
corresponding summary table. All runs use **CPLEX 20.1.0** in single-threaded mode with a
**time limit of 3600 seconds**.

## Solving configurations

| Label | Meaning |
|-------|---------|
| `CPX`  | Default CPLEX on the integer programming formulation (baseline). |
| `OC`   | CPLEX + the proposed **odd cycle** inequalities. |
| `CL`   | CPLEX + the proposed **clique** inequalities (full family). |
| `GS`   | CPLEX + the proposed **generalized star** inequalities. |
| `OCG`  | CPLEX + all three families together. |
| `CL'`  | CPLEX + the **restricted** clique subclass of Galluccio and Nobili (`c ≤ k+1`). |

## Column symbols

Counts (`#N`, `#C`) are integers. For every other numeric cell, `0` means the
quantity is exactly zero (it did not occur at all), `<0.1` means it is positive but too
small to show at one decimal, and everything else is the value itself.

| Symbol   | Meaning |
|----------|---------|
| `T`      | Solve time in CPU seconds; if the run reaches the time limit, the cell instead shows the end gap (in %) in parentheses. |
| `EG`     | End gap (%) on a run that reached the time limit; reported inside the parentheses of `T`. |
| `#N`     | Number of branch-and-bound nodes plus one (a root-only solve reads `1`). |
| `GI`     | Root-node gap improvement (%): the fraction of the initial LP gap closed at the root by the added cuts. |
| `#C`     | Number of cuts added. |
| `#C/R`   | Average number of cuts added per separation round (total cuts divided by the number of separation callbacks). |
| `ST`     | Cut-separation CPU time (seconds). |
| `c̄`      | Average size `c` of the cliques inducing the added clique inequalities. |
| `λ̄`      | Average value of `λ` over the added clique inequalities. |
| `λ=1`    | Percentage of the added clique inequalities with `λ = 1`. |
| `c>k+1`  | Percentage of the added clique inequalities induced by cliques with `c > k+1`. |

## Files

**`pace_instancewise.csv`** compares the default CPLEX (`CPX`) with the odd cycle (`OC`),
clique (`CL`), generalized star (`GS`), and combined (`OCG`) configurations on the 1200
Max *k*-VC instances built from the PACE-2019 graphs, under both unit and non-unit edge
weights and budgets ranging from 20% to 70% of the number of vertices. Each configuration
occupies one block of columns. Under `OC`, `CL` and `GS` the `#C` and `ST` columns are the
cuts and the separation time of that family on its own; under `OCG` the `#C(OC)`, `#C(CL)`
and `#C(GS)` columns are the cuts of the three families inside the combined configuration
and `ST` is their total separation time.

**`er_clique_instancewise.csv`** compares the full clique family (`CL`) with the restricted
Galluccio–Nobili subclass (`CL'`) on 60 dense Erdős–Rényi instances, with 80 or 100
vertices, edge probability 0.7 or 0.8, and small budgets *k* = 8, 10, 12 (five seeds each).
The full clique family solves every instance in this test set; the restricted subclass leaves one unsolved.

**`bipartite_instancewise.csv`** compares the default CPLEX (`CPX`) with the generalized
star configuration (`GS`) on 75 bipartite Max *k*-VC instances, built on a base of 100
vertices with edge probability *p* ∈ {0.70, 0.80, 0.90} and budgets
*k* ∈ {130, 140, 150, 160, 170} (five seeds each). The last column `#C/R` gives the average number of
generalized star cuts added per separation round.

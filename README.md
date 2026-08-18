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

Counts (`#N`, `#C`) are integers.  For every other numeric cell, `0` means the quantity
is exactly zero (it did not occur at all), `<0.1` means it is positive but too small to
show at one decimal, `TL` means the run stopped at the time limit, `NA` means the quantity
is not defined for that run, and everything else is the value itself.  **No cell is ever
parenthesised or negative**, so the files can be opened directly in a spreadsheet.

| Symbol   | Meaning |
|----------|---------|
| `T`      | Solve time in CPU seconds on a run that proved optimality; `TL` on a run that stopped at the time limit -- its CPU clock (3591.6 s and the like) would read as if the instance had been solved in that time. |
| `EG`     | End gap `(DB - PB)/DB` in % of a run that reached the time limit; `NA` on a run that proved optimality. `EG == NA` is therefore an exact "solved" flag. |
| `#N`     | Number of branch-and-bound nodes plus one, the quantity the summary tables average. `#N = 1` means the search never left the root; whether the root was *solved* or merely *timed out* there is told by `EG`. |
| `GI`     | Root-node gap improvement (%): the share of the initial LP gap closed at the root by the added cuts, `GI = (z_LP - z_ROOT)/(z_LP - z_IP) * 100`, where `z_LP` is the LP relaxation before any cut, `z_ROOT` the root bound after the cuts, and `z_IP` the best incumbent of the compared configurations.  See the two notes below. |
| `#C`     | Number of cuts added. |
| `#C/R`   | Average number of cuts added per separation round (total cuts divided by the number of separation callbacks). |
| `ST`     | Cut-separation CPU time (seconds). |
| `c̄`      | Average size `c` of the cliques inducing the added clique inequalities. |
| `λ̄`      | Average value of `λ` over the added clique inequalities. |
| `λ=1`    | Percentage of the added clique inequalities with `λ = 1`. |
| `c>k+1`  | Percentage of the added clique inequalities induced by cliques with `c > k+1`. |

**`GI` when the initial LP is already optimal.**  If `z_LP = z_IP` the denominator is zero:
there is no root gap to close, and `GI` is reported as `100`.

**`GI` on a run that stopped inside the root node.**  `z_ROOT` is normally the tightest
bound *observed* at a cut-separation callback.  On a run that reached the time limit before
leaving the root (`EG != NA` and `#N = 1`) the separation loop never converged, so that
observed value can lag behind what the solver had actually proved; for those runs `z_ROOT`
is therefore the **dual bound the run ended with**, which with no node processed is a root
bound.  On 12 of the 208 such runs in `pace_instancewise.csv` the final bound is still
exactly the initial LP bound, so those rows read `GI = 0`: by the time the run stopped it
had closed none of the root gap.  Together with `EG != NA` and `#N = 1` on the same row that
is an unambiguous statement, and no published table number depends on it.

## Reproducing the summary tables of the paper

Every summary table is the shifted geometric mean over the rows of these files,
`prod(v_i + 1)^(1/L) - 1`, with shift `1` applied to the value as printed -- including the
percentage columns, so 5% enters as 5+1.  Two conventions have to be respected:

* `T = TL` enters the average as the time limit, 3600 s;
* the few `GI` cells that are `NA` -- instances that no configuration solved, so that no
  bound to measure against is known -- are left out of the average; none of them falls
  inside a table bucket, so no published number depends on this.

`pace_instancewise.csv` and `bipartite_instancewise.csv` use the root-gap convention of the
tables they back: an instance that is *solved* at the root closes 100% of its gap by
definition.  `er_clique_instancewise.csv` uses the plain formula above, clamped to
`[0,100]`, because that is what its own table prints.

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
The `c>k+1` column closes the `CL` block: by construction the restricted subclass `CL'` never
adds such an inequality, so the column exists for `CL` only.
The full clique family solves every instance in this test set; the restricted subclass leaves one unsolved.

**`bipartite_instancewise.csv`** compares the default CPLEX (`CPX`) with the generalized
star configuration (`GS`) on 75 bipartite Max *k*-VC instances, built on a base of 100
vertices with edge probability *p* ∈ {0.70, 0.80, 0.90} and budgets
*k* ∈ {130, 140, 150, 160, 170} (five seeds each). The last column `#C/R` gives the average number of
generalized star cuts added per separation round.

## Regenerating and checking these files

    python3 experiments/table_scripts/mkvcp_makecsv.py --root <raw results> \
            --out csv_for_github --verify

`--verify` re-reads what it just wrote and checks that no cell is parenthesised, negative
or otherwise unparsable, that `#N`, `EG` and `GI` are mutually consistent on every row,
that every cell reproduces from the raw solver output, and that the summary tables of the
paper can be recomputed from these files.


# Finite element experiments for planar Ornstein–Uhlenbeck problems

This repository contains the code and recorded results for the manuscript "Explicit coercive formulation for two-dimensional Ornstein–Uhlenbeck Dirichlet problems and its finite element approximation".

The experiments compare the original formulation $\mathcal B$ and the weighted formulation $\mathcal E$ on the same finite element meshes, using the same sparse LU solver.

## Running the notebook

Everything needed to run the experiments is included in [2D_ou_fem_all_benchmarks.ipynb](2D_ou_fem_all_benchmarks.ipynb).

### Google Colab

Download the notebook and upload it to [Google Colab](https://colab.research.google.com/). Start a fresh CPU runtime and select `Runtime → Run all`. A GPU is not needed. Missing numerical packages are installed automatically.

The last cell provides a link to a ZIP file containing the results. To download it directly in Colab, set `DOWNLOAD_ARCHIVE = True` in that cell and run the cell again.

### On your computer

Install Python 3 and open a terminal in the folder containing the notebook. Then run:

```bash
python -m pip install jupyterlab numpy scipy matplotlib threadpoolctl
jupyter lab 2D_ou_fem_all_benchmarks.ipynb
```

You can also use VS Code with Microsoft's Python and Jupyter extensions. Open the notebook, select the Python environment containing these packages, and run the cells in order.

## Run settings

The settings are in the first code cell. The defaults run the full experiment and the additional validation checks:

```python
RUN_NEW_EXPERIMENT = True
FULL_VALIDATION = True
QUICK_MODE = False
```

For a short test, leave `RUN_NEW_EXPERIMENT = True` and set `QUICK_MODE = True`. This uses $n=8,16$ and one timed run per configuration.

To make the tables and figures from the recorded data, set `RUN_NEW_EXPERIMENT = False`. This does not repeat the finite element solves or timing measurements. The recorded data were obtained in a separate Linux CPU environment.

## Experiments

We solve $-Lu=f$ on $U=(-1,1)^2$ with zero Dirichlet boundary conditions, where

$$
Lu=\frac12\operatorname{tr}(A\nabla^2u)+\langle Gx,\nabla u\rangle.
$$

All cases use the exact solution $u_\star=(1-x_1^2)(1-x_2^2)$. The right-hand side is $f=-Lu_\star$ for each choice of $A$ and $G$. In the table, matrices are written as lists of rows.

| Case | A | G |
| --- | --- | --- |
| Positive trace, coercive original form | `[[2, 1], [1, 2]]` | `[[3, 4], [1, 3]]` |
| Negative trace, coercive original form | `[[2, 1], [1, 1]]` | `[[0, 1], [-2, -3]]` |
| Noncoercive original form | `[[1, 0], [0, 2]]` | $k$ times `[[-1, 1], [-1, -2]]` |

The noncoercive family uses $k\in\{4,8,12,16,20\}$.

Both formulations use continuous piecewise affine elements. Each coordinate interval is divided into $n$ equal parts. Each square is then split along its southwest-to-northeast diagonal. The values of $n$ are:

```python
SIZES = [8, 16, 32, 64, 128, 256, 512]
```

At $n=512$, there are 524,288 triangles and 261,121 interior unknowns. In all plots, $n$ increases from left to right.

The reported errors are measured in the unweighted $L^2$ and full $H^{1,2}$ norms. The full $H^{1,2}$ norm includes both the function and gradient terms. Relative errors are obtained by dividing by the corresponding exact-solution norm. They are reported as ratios, so $10^{-2}$ means $1\%$.

Both systems are solved with SciPy's `splu`, using COLAMD ordering and one numerical thread. With the default settings, each configuration has one warm-up run followed by seven timed runs. The order of the two methods alternates, and the reported time is the median of the seven measurements.

Each timed run includes coefficient preparation, matrix and load-vector assembly, a fresh LU factorization, and the solve. Mesh generation, error evaluation, validation, and plotting are excluded. Execution times will depend on the computer used.

## Results

Results are saved under `ou_fem_public_reproduction/`. Each run creates its own folder, named `fresh_run_<timestamp>/` for new computations or `recorded_run_<timestamp>/` for recorded-data replay.

The ZIP file contains the computational code, error tables, individual timing measurements, environment information, validation reports, and eleven PDF figures with PNG previews. New computations also save the finest-mesh nodal solutions. Numerical LaTeX tables are saved in `tables/`. These files contain `tabular` environments only.

The validation routines check the coefficient identities, matrix assembly, relative errors, and timing summaries. A fresh run with the default settings and `FULL_VALIDATION = True` also checks the finest-mesh results with higher quadrature accuracy and alternative solver settings. These additional checks are performed for the two coercive examples and the noncoercive case with `k=20`. Their time is excluded from the reported solution times.

## Citation

If you use this code or the numerical results, please cite the accompanying manuscript and state which repository release or commit you used.

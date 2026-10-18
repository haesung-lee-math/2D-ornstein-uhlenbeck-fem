# Finite element experiments for planar Ornstein–Uhlenbeck problems

Code for the manuscript *Explicit coercive formulation for two-dimensional Ornstein–Uhlenbeck Dirichlet problems and its finite element approximation*.

The experiments compare the original formulation $\mathcal B$ and the weighted formulation $\mathcal E$ using the same finite element spaces and sparse LU solver.

## Running the notebook

All code and recorded data are included in [`2D_ou_fem_all_benchmarks.ipynb`](2D_ou_fem_all_benchmarks.ipynb).

### Google Colab

Download the notebook, upload it to [Google Colab](https://colab.research.google.com/), and select `Runtime → Run all`. Use a fresh CPU runtime. The notebook installs any missing numerical packages and creates a ZIP file containing the results. The final cell provides a download link. Set `DOWNLOAD_ARCHIVE = True` there to start a Colab browser download.

### On your computer

Install Python 3, then run these commands in a terminal:

```bash
python -m pip install jupyterlab numpy scipy matplotlib threadpoolctl
jupyter lab 2D_ou_fem_all_benchmarks.ipynb
```

You can also open the notebook in VS Code with Microsoft's Python and Jupyter extensions. Select your Python environment as the notebook kernel and run all cells.

## Choosing a run

The first code cell contains the settings. By default, the notebook repeats the full experiment:

```python
RUN_NEW_EXPERIMENT = True
FULL_VALIDATION = True
QUICK_MODE = False
```

For a short test, keep `RUN_NEW_EXPERIMENT = True` and set `QUICK_MODE = True`. This uses only $n=8,16$ and one timed run per configuration.

To redraw the figures and regenerate the tables from the recorded results, set `RUN_NEW_EXPERIMENT = False`. This mode uses the embedded data without repeating the solves or timing measurements. The recorded data come from a separate Linux CPU run.

## Experiments

We solve $-Lu=f$ on $U=(-1,1)^2$ with zero Dirichlet boundary conditions, where

$$
Lu=\frac12\operatorname{tr}(A\nabla^2u)+\langle Gx,\nabla u\rangle.
$$

The exact solution is $u_\star=(1-x_1^2)(1-x_2^2)$, and $f=-Lu_\star$ is computed for each choice of coefficients. Matrices below are written as lists of rows.

| Case | A | G |
| --- | --- | --- |
| Positive trace, coercive original form | `[[2, 1], [1, 2]]` | `[[3, 4], [1, 3]]` |
| Negative trace, coercive original form | `[[2, 1], [1, 1]]` | `[[0, 1], [-2, -3]]` |
| Noncoercive original form | `[[1, 0], [0, 2]]` | `k * [[-1, 1], [-1, -2]]` |

The last family uses $k\in\{4,8,12,16,20\}$.

Both methods use continuous piecewise affine elements. Each coordinate interval is divided into $n$ equal parts, and each square is split along its southwest-to-northeast diagonal. The mesh sizes are

```python
SIZES = [8, 16, 32, 64, 128, 256, 512]
```

At $n=512$, the mesh has 524,288 triangles and 261,121 interior unknowns. The plots use $n$ on the horizontal axis.

We report absolute and relative errors in the unweighted $L^2$ and full $H^{1,2}$ norms, together with total solution times. Relative errors are ratios, so $10^{-2}$ corresponds to $1\%$.

Both systems use SciPy's `splu` with COLAMD ordering and one numerical thread. Each configuration has one warm-up run and seven timed runs, with the order of the two methods alternating. We report the median total time, including coefficient preparation, matrix and load assembly, and a fresh LU factorization and solve. Mesh generation, error evaluation, validation, and plotting are excluded. Timings depend on the computer used.

## Results

Results are saved under `ou_fem_512_reproduction/`. Fresh computations go into a timestamped folder, while replay uses `recorded_run/`.

The output ZIP contains error tables, raw timing measurements, environment information, validation reports, and eleven PDF figures with PNG previews. Fresh computations also save the finest-mesh nodal solutions. Full-protocol runs generate LaTeX sections from the included manuscript templates.

Validation checks cover coefficient identities, assembly, relative-error normalization, and timing summaries. With `FULL_VALIDATION = True`, a fresh full run also checks the finest-mesh results using higher quadrature accuracy and alternative solver settings.

## Citation

Please cite the accompanying manuscript when using these experiments and identify the repository release or commit used.

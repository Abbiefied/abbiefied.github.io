# abbiefied.github.io
Personal Portfolio

My personal website and blog, built with [Quarto](https://quarto.org) and published with GitHub Pages at <https://abbiefied.github.io>. It includes two computational blog posts, one in Python and one in R, each with its own pinned environment so the whole site can be rebuilt from scratch.

## What to install first

| Tool | Version I used | Get it from |
|---|---|---|
| Quarto | 1.10.18 | <https://quarto.org/docs/get-started/> |
| uv | 0.12.5 | <https://docs.astral.sh/uv/getting-started/installation/> |
| R | 4.6.1 | <https://cran.r-project.org/> |
| Git | any recent version | <https://git-scm.com/downloads> |

You do not need to install Python or renv yourself:

- **uv** downloads Python 3.14 (pinned in `.python-version`) the first time you sync.
- **renv** installs itself the first time R starts in this folder, via `.Rprofile`.

Check that everything is on your PATH:

```bash
quarto --version
uv --version
Rscript --version
```

On Windows, run these commands in Git Bash. On macOS and Linux, use your normal terminal. If you use PowerShell instead, type `R.exe` rather than `R`, because `R` is a built-in PowerShell command.

## Build the site
These steps were tested on Windows with Git Bash, starting from a fresh clone.

Run every command below in a terminal, from the **top level of the repository** (the folder that contains `_quarto.yml`). Quarto has to be started from here so that R finds `.Rprofile` and switches renv on.

**1. Clone the repository**

```bash
git clone https://github.com/abbiefied/abbiefied.github.io.git
cd abbiefied.github.io
```

**2. Create the Python environment** from `pyproject.toml` and `uv.lock`

```bash
uv sync
```

This creates a `.venv/` folder with the exact package versions in `uv.lock`, including Jupyter, which Quarto uses to run Python code.

**3. Restore the R environment** from `renv.lock`

```bash
Rscript -e "renv::restore(prompt = FALSE)"
```

The first time R starts here, renv installs itself, then installs every package listed in `renv.lock` into `renv/library/`. This can take a few minutes.

If you prefer to work inside R, start R from the top level of the repository and run:

```r
renv::restore()
```

**4. Render the site**

```bash
uv run quarto render
```

`uv run` makes sure Quarto uses the Python in `.venv`. All three blog posts run their code during this step.
 
Steps 2 and 3 must both be done before rendering. The R and Python post (`posts/r-meets-python/`) needs the R packages from `renv.lock`, including reticulate, and it runs its Python code with the `.venv` from step 2. It finds `.venv` itself, on Windows, macOS and Linux, so there is nothing to configure.

## Where the built site goes

The rendered site is written to `docs/`, which is the folder GitHub Pages publishes from.

To view it locally, the easiest option is:

```bash
uv run quarto preview
```

This opens the site in your browser. Alternatively, you can serve the built files directly:

```bash
uv run python -m http.server 8000 --directory docs
```

Then open <http://localhost:8000>.

## Data

Both datasets ship inside packages, so no data files are committed to this repository, and rendering does not download any data.

| Post | Dataset | Comes with | Licence |
|---|---|---|---|
| `posts/diabetes-progression/` (Python) | [Diabetes dataset](https://scikit-learn.org/stable/datasets/toy_dataset.html#diabetes-dataset), Efron et al. (2004) | scikit-learn | BSD-3-Clause |
| `posts/diamond-price-paradox/` (R) | [`diamonds`](https://ggplot2.tidyverse.org/reference/diamonds.html) | ggplot2 | MIT |

You need a network connection only for steps 2 and 3, to download packages from PyPI and CRAN. After that, the site builds offline.

## Notes for macOS and Linux
 
The commands above work unchanged in the macOS Terminal and on Linux.
 
- **Use R 4.6** if you can. renv installs the exact package versions in `renv.lock`, and ready-made binaries are most likely to be available for the R version I used. With a different R version, some packages may need to be compiled from source.
- **macOS:** if `renv::restore()` fails with a compiler error, install Apple's command-line developer tools, then run step 3 again:
```bash
  xcode-select --install
```
 
- **Linux:** CRAN provides R packages as source code for Linux, so `renv::restore()` compiles them and can take several minutes. You may need your distribution's build tools first (for example, `sudo apt install build-essential` on Ubuntu).

## Troubleshooting

If Python chunks run with the wrong Python, clear Quarto's cache and render again:

```bash
rm -r .quarto
uv run quarto render
```

If an R chunk fails with `there is no package called ...`, re-run step 3.
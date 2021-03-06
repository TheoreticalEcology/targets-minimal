
# `targets` package minimal example

[![Launch RStudio
Cloud](https://img.shields.io/badge/RStudio-Cloud-blue)](https://rstudio.cloud/project/1430691)

This repository is an example data analysis workflow with
[`targets`](https://github.com/wlandau/targets). The pipeline reads the
data from a file, preprocesses it, visualizes it, and fits a regression
model.

## How to access

You can try out this example project as long as you have a browser and
an internet connection. [Click
here](https://rstudio.cloud/project/1430691) to navigate your browser to
an RStudio Cloud instance. Alternatively, you can clone or download this
code repository and install the R packages [listed
here](https://github.com/wlandau/targets-minimal/blob/03835c2aa4679dcf3f28c623a06d7505b18bee17/DESCRIPTION#L25-L30).

## How to run

In the R console, call the
[`tar_make()`](https://wlandau.github.io/targets/reference/tar_make.html)
function to run the pipeline. Then, call `tar_read(hist)` to retrieve
the histogram. Experiment with [other
functions](https://wlandau.github.io/targets/reference/index.html) such
as
[`tar_visnetwork()`](https://wlandau.github.io/targets/reference/tar_visnetwork.html)
to learn how they work.

## File structure

The files in this example are organized as follows.

``` r
├── _targets.R
├── R/
├──── functions.R
├── data/
├──── raw_data.csv
└── index.Rmd
```

| File                                                                                          | Purpose                                                                                                                                                                                                                                                                                            |
| --------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`_targets.R`](https://github.com/wlandau/targets-minimal/blob/main/_targets.R)               | The special R script that declares the [`targets`](https://github.com/wlandau/targets) pipeline. See `tar_script()` for details.                                                                                                                                                                   |
| [`R/functions.R`](https://github.com/wlandau/targets-minimal/blob/main/R/functions.R)         | An R script with user-defined functions. Unlike [`_targets.R`](https://github.com/wlandau/targets-minimal/blob/main/_targets.R), there is nothing special about the name or location of this script. In fact, for larger projects, it is good practice to partition functions into multiple files. |
| [`data/raw_data.csv`](https://github.com/wlandau/targets-minimal/blob/main/data/raw_data.csv) | The raw `airquality` dataset.                                                                                                                                                                                                                                                                      |

[`index.Rmd`](https://github.com/wlandau/targets-minimal/blob/main/index.Rmd):
an R Markdown report that reruns in the pipeline whenever the histogram
of ozone changes
([details](https://books.ropensci.org/targets/files.html#literate-programming)).

## Continuous deployment

For every GitHub push on main branch, the `targets` pipeline runs on
[GitHub
Actions](https://github.com/wlandau/targets-minimal/actions/workflows/run.yaml).
All you need is to define a `.github/workflows/run.yaml` file that
caches the `_targets/` data store and runs the pipeline.

``` yaml
- name: Cache targets data store
  uses: actions/cache@v2
  with:
    path: _targets
    key: ${{ runner.os }}-targets-${{ hashFiles('_targets/meta/meta') }}
    restore-keys: ${{ runner.os }}-targets-

- name: Run targets pipeline
  run: targets::tar_make()
  shell: Rscript {0}
```

Each subsequent run restores the previous cache and skips targets that
are already up to date.

![](./man/figures/uptodate.png)

Read [this
explanation](https://docs.github.com/en/actions/guides/caching-dependencies-to-speed-up-workflows#usage-limits-and-eviction-policy)
for usage restrictions and limitations of GitHub Actions caches.

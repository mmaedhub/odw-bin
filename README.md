# odw-bin

An `R` package that generates optimal categorical experimental designs under a
linear mixed model.

Precompiled binary packages.

## Overview

`odw` seeks an optimal experimental design based on the A-value criterion with
respect to a specified objective (treatment) factor under a general linear
model. The model is defined in three main R formula arguments to the _odw()_
function that specify the terms in the _fixed_, _random_ and _residual_
components of the model. The _odw()_ call is of a functional style, where
variance structures and model constructors are applied to terms in the formulae
using special functions, for example, _ar1(x)_ specifies a first order
autoregressive model for the factor _x_. 

## Initial state

`odw` requires:

* An **initial** data frame containing the design (eg, blocking), treatment
  (objective), and explanatory factors appearing in the model. It may be an
  advantage to permute the levels of the objective factor, respecting
  resolvability constraints, prior to the design search. The initial data frame
  is provided via the _data_ argument to odw().
* **Pre-specified** values for all random parameters. Default values are
  provided and if necessary can be replaced using the action of the
  _start.values_ argument. For example, the following sequence alters
  the pre-specified values for the _Variety_ variance component and the
  spatial correlation parameters for _Column_ and _Row_.

    ```R
    library(odw)
    data(rc400)
    sv <- odw(fixed = ~ 1, random = ~ Variety+Rep+Column+Row,
                permute = ~ Variety, swap = ~ Rep, search='random',
                residual = ~ ar1(Column):ar1(Row),
                data = rc400, maxit = 1000, start.values = TRUE)
    sv$vparameters.table$Value[c(1, 6, 7)] <- c(0.2, 0.3, 0.4)
    rc400.od <- odw(fixed = ~ 1, random = ~ Variety+Rep+Column+Row,
                    permute = ~ Variety, swap = ~ Rep, search = 'random',
                    residual = ~ ar1(Column):ar1(Row),
                    G.param = sv, R.param = sv,
                    data = rc400, maxit = 1000)
    ```

## Design search

The search for an optimal categorical design is a discrete optimisation problem, involving
the interchange of pairs of experimental units; governed in _odw()_ by the
_search_ and _maxit_ arguments. These set the search method (_tabu_, for example)
and duration, respectively. The optimisation seeks to minimise the A-value of
the design, that is, the average pairwise variance of elementary contrasts among
the levels of the objective factor. The default search method is _random_, the following
call will execute 1 tabu (with a random walk component) loop in the search for an
optimal design:

```R
    library(odw)
    data(rc400)
    rc400.od <- odw(fixed = ~ 1, random = ~ Variety+Rep+Column+Row,
                    permute = ~ Variety, swap = ~ Rep, search = 'tabu+rw',
                    residual = ~ ar1(Column):ar1(Row),
                    data = rc400, maxit = 1)
```


## The return object

On completion, the `odw` object contains a _design_ component that is the
optimal permutation of the initial data frame with respect to the A-value of the
objective factor, and a _permutation_ component being the (final) sort vector
applied to the rows of the initial data frame. The above `odw` object contains:

```R
> names(rc400.od)
[1] "permutation" "criterion"   "design"      "call"        "formulae"   
[6] "mf"          "R.param"     "G.param"
```

## Methods

The package provides _summary_, _plot_ and _update_ methods for _odw_ objects,
and a companion _plot.df()_ function to visualise a design represented in a data frame.
Images of the final design in _rc400.od_ from the above, and that of the initial
layout with the factor _Rep_ overlayed is rendered by:

```R
plot(rc400.od, theme = ~ Rep)
plot.df(layout = ~ Column + Row, theme = ~ Rep, data = rc400)
```

Permute factor _incidence_ and _concurrence_ tables are returned by the summary
method:

```R
rc400.sum <- summary(rc400.od)
> names(rc400.sum)
[1] "criterion"   "incidence"   "is.binary"   "concurrence"
```

# Installation

Compiled `odw` packages for

- macOS (Apple Silicon),
- macOS (Intel),
- Linux (x86_64), and
- Windows (x86_64)

systems are
under the [releases](https://github.com/mmaedhub/odw-bin/releases) tab.

For macOS on Apple Silicon (for example) install from the system command line with

```shellscript
> R CMD INSTALL odw_2.2.8_Darwin_arm64.tgz
```

or at the `R` console using

```R
> install.packages("odw_2.2.8_Darwin_arm64.tgz", repos = NULL)
```

or interactively from within `R-gui`, `Rstudio` or `R.app`.

## Dependencies

Installing an `R` package from a local binary file will not automatically resolve or download
missing dependencies.

`odw` depends on

- data.table (>= 1.16.0),
- Matrix,
- methods,
- Rdpack

and _suggests_

- rlang,
- ggplot2,
- grid,
- sp,
- igraph,
- sf,
- BiocStyle,
- knitr,
- rmarkdown
 
 for non-core functionality.

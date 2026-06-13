## Submission summary

This is a maintenance + feature update of **twinsvm** (0.0.1 on CRAN -> 0.0.3).

* Bug fix: the animation test for `morph_boundary()` now skips when the
  suggested package `gganimate` is not installed, so `R CMD check` passes on
  systems checked without Suggests (the previous release errored there).
* New feature: `resample_accuracy()` evaluates `tsvm()` and `svms()` over
  repeated stratified random holdout splits and reports the mean test accuracy
  with its between-split standard deviation and standard error, plus `print`
  and `plot` methods.

## Test environments

* Local: Windows 11, R 4.6.0 -- `R CMD check --as-cran`

(Not yet checked on r-devel / macOS / Linux; see note below.)

## R CMD check results

0 errors | 0 warnings | 1 note

* checking CRAN incoming feasibility ... NOTE
  Maintainer: 'Shamika Tissera <nimeshshamika@gmail.com>'
  Days since last update: 4

  This NOTE reports only the maintainer address and the short interval since
  the previous release. The update is submitted this soon because 0.0.1 fails
  `R CMD check` on machines that check without the suggested packages; 0.0.3
  fixes that.

All examples, tests (120 testthat assertions) and vignettes run cleanly.

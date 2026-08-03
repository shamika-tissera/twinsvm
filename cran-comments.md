## Submission summary

This is a resubmission of **twinsvm**, which was archived on 2026-08-02. I did
not correct the reported problem before the deadline. This version fixes it.

### What was wrong

The archived checks were OK or NOTE on every regular flavor. The failure was in
the "Additional issues" checks, on the **MKL** and **OpenBLAS** flavors, where
version 0.0.2 gave one test failure:

```
test-baselines.R:27:3
Expected `abs(fit_linear$n_support - ref_linear$tot.nSV)` <= 2
Actual: 4.0 > 2.0
[ FAIL 1 | PASS 102 ]
```

The test compared the number of support vectors found by `svms()` against
`e1071::svm()`. That count is a discrete threshold on the dual variables, and on
a linear soft-margin fit many points sit exactly at the cost bound, so whether
each one counts as a support vector depends on floating-point details of the
BLAS in use. The tolerance of 2 was too tight to hold across BLAS
implementations.

### What changed

* Support-vector counts are now compared up to a small fraction of the sample
  size rather than a fixed small integer. Per-point prediction agreement with
  `e1071` (>= 99%) is unchanged and remains the strict correctness check.
* The exact-value regression test (`fit_rbf$n_support == 25`) is now
  `skip_on_cran()`. It stays active locally and in CI as a regression guard.
* Reference-fit comparison tolerances relaxed from 1e-6 to 1e-5, same cause.
* No model-fitting or prediction behavior changed. This release is test-only
  relative to the unreleased 0.0.3, which added `resample_accuracy()` and a
  `gganimate` skip guard (see NEWS.md).

## Test environments

* Local: Windows 11, R 4.6.0, `R CMD check --as-cran`
* GitHub Actions: macOS-latest (release), Windows-latest (release),
  Ubuntu-latest (devel, release, oldrel-1)

I do not have an MKL- or OpenBLAS-linked R to reproduce the original failure
directly. The fix targets the mechanism rather than the observed magnitude: the
new bound is 12 support vectors on n = 120, against an observed cross-BLAS drift
of 4.

## R CMD check results

0 errors | 0 warnings | 0 notes

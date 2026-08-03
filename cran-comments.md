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
* GitHub Actions: macOS-latest (release), Windows-latest (release and devel),
  Ubuntu-latest (devel, release, oldrel-1)
* GitHub Actions: Ubuntu-latest (release) with R linked against OpenBLAS

The last of those was added for this release, so that the flavor behind the
archival is exercised before submission rather than after. It passes.

The new bound is 12 support vectors on n = 120, against an observed cross-BLAS
drift of 4.

## Note on the 2026-08-03 pre-test

The Windows pre-test of this tarball reported a second NOTE, which I expect to
recur:

    * checking compiled code ... NOTE
    Error in ccE(lines, flags = new_flags, include = include) :
      'cc' is not on the path
    Execution halted

This is an abort inside `tools:::ccE`, which opens with

    if (Sys.which("cc") == "") stop("'cc' is not on the path")

and then runs `cc -E`. The compiler name is hardcoded, with no fallback to
`gcc` and no use of `CC` from Makeconf. Rtools provides `gcc` and ships no `cc`.

The CI job `windows-latest (devel, no cc on PATH)` exists to demonstrate this.
GitHub's Windows runners carry a `cc.exe` at `C:\mingw64\bin`; that job renames
it and changes nothing else. On R-devel r90339, same package, same R:

| `cc.exe` | `checking compiled code` |
| -------- | ------------------------ |
| present  | OK                       |
| renamed  | NOTE, `'cc' is not on the path` |

The error also reproduces in a bare R session with twinsvm never loaded, by
calling `tools:::getFunAPI()` directly.

`src/` in 0.0.4 is byte-for-byte identical to `src/` in 0.0.2, which was OK on
r-devel-windows-x86_64. No C++ source, header, or Makevars file changed. No
change to this package can prevent the NOTE.

## R CMD check results

0 errors | 0 warnings | 1 note

* checking CRAN incoming feasibility ... NOTE
  Maintainer: 'Shamika Tissera <nimeshshamika@gmail.com>'
  New submission
  Package was archived on CRAN
  CRAN repository db overrides:
    X-CRAN-Comment: Archived on 2026-08-02 as issues were not corrected
      in time.

  This NOTE reports the archival described above and is expected for a
  resubmission.

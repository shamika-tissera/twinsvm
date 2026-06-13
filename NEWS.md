# twinsvm 0.0.3

* New `resample_accuracy()` evaluates `tsvm()` and `svms()` over repeated
  stratified random holdout splits and reports the mean test accuracy together
  with its between-split standard deviation and standard error, so method
  comparisons can be judged against sampling noise. Splits are drawn once and
  shared across models for a paired comparison. Adds `print` and `plot` (boxplot)
  methods for the returned `resample_accuracy` object.
* The animation test for `morph_boundary()` now skips cleanly when the suggested
  `gganimate` package is absent, so `R CMD check` passes without Suggests
  installed. No model-fitting or prediction behavior changed.

# twinsvm 0.0.2

* Fixed cross-platform test portability on macOS by avoiding bitwise equality
  checks for generated floating-point data.
* No model-fitting or prediction behavior changed.

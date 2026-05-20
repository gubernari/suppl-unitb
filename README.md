# Supplementary material for *Unitarity bounds and form-factor predictions for B-meson decays*

This repository collects the supplementary material for the companion paper *Unitarity bounds and form-factor predictions for B-meson decays*. It contains EOS analysis files, precomputed posterior samples and posterior predictions, PDF plots, and Mathematica notebooks for the three combined form-factor analyses carried out with the GG parametrization implemented in EOS under the label `G2026`.

The final fits stored here are:

- `G4-bu-LCSR+LQCD+UB` for `B->pi`, `B_s->K`, and `B_s->K^*`;
- `G4-bs-LCSR+LQCD+UB` for `B->K`, `B->K^*`, and `B_s->phi`;
- `G3-bc-LQCD+UB` for `B->D`, `B_s->D_s`, `B->D^*`, and `B_s->D_s^*`.

Here `G4` and `G3` denote the GG truncation orders used in the final fits.

## Repository contents

- [`bu-analysis.yaml`](bu-analysis.yaml): EOS analysis file for the combined `b->u` fit. Prediction sets cover `B->pi`, `B_s->K`, `B_s->K^*`, and the corresponding saturation observables.
- [`bs-analysis.yaml`](bs-analysis.yaml): EOS analysis file for the combined `b->s` fit. Prediction sets cover `B->K`, `B->K^*`, `B_s->phi`, and the corresponding saturation observables.
- [`bc-analysis.yaml`](bc-analysis.yaml): EOS analysis file for the combined `b->c` fit. Prediction sets cover `B->D`, `B_s->D_s`, `B->D^*`, `B_s->D_s^*`, the saturation observables, the `B->D^*` ratios `R0`, `R1`, `R2`, and LFU ratios.
- [`data/`](data/): precomputed EOS outputs. Each posterior directory contains `nested/` (dynamic nested-sampling output), `samples/` (importance samples), `mode-EOS/` (mode-finding output), `pred-*/` (posterior predictions), and `plots/` (corner plots).
- [`figures/`](figures/): PDF figures for the form-factor and saturation plots.
- [`mathematica_notebook/`](mathematica_notebook/): Mathematica notebook and auxiliary `.wl` files used for cross-checks and for quick usage of the posterior samples.

## Running the analyses with EOS

These YAML files are standard EOS analysis files and require an EOS installation that includes the `G2026` form-factor implementation. The included data files were produced with EOS `1.0.20`. The EOS documentation is available at <https://eoshep.org/doc/>.

From this directory, set the base directory so EOS writes to the local `data/` tree:

```bash
cd /path/to/suppl-unitb
export EOS_BASE_DIRECTORY="$PWD"
```

To inspect an analysis file:

```bash
eos-analysis list-posteriors -f bu-analysis.yaml
eos-analysis list-predictions -f bu-analysis.yaml
```

To rerun the `b->u` posterior and reproduce the standard outputs:

```bash
eos-analysis sample-nested -f bu-analysis.yaml G4-bu-LCSR+LQCD+UB
eos-analysis find-mode -f bu-analysis.yaml G4-bu-LCSR+LQCD+UB -S -L EOS
eos-analysis corner-plot -f bu-analysis.yaml G4-bu-LCSR+LQCD+UB
eos-analysis predict-observables -f bu-analysis.yaml G4-bu-LCSR+LQCD+UB Bpi-f+
```

The `sample-nested` step is computationally demanding. In my production runs, it required on the order of `100 GB` of RAM and used `96` CPU cores.

The commands above populate `data/G4-bu-LCSR+LQCD+UB/` with `nested/`, `samples/`, `mode-EOS/`, `pred-*`, and `plots/`.

To generate every prediction set defined in a file:

```bash
for pred in $(eos-analysis list-predictions -f bu-analysis.yaml); do
    eos-analysis predict-observables -f bu-analysis.yaml G4-bu-LCSR+LQCD+UB "$pred"
done
```

The `b->s` and `b->c` analyses are completely analogous; just replace the file and posterior names with `bs-analysis.yaml` / `G4-bs-LCSR+LQCD+UB` or `bc-analysis.yaml` / `G3-bc-LQCD+UB`.

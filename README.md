# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Research code accompanying the paper *"Hand Gesture Recognition with Surface Electromyogram: an
End-to-End System with Transformer-Encoder Architecture."* It trains a CNN + Time2Vec + Transformer-encoder
network to classify 15 hand gestures from 12-channel surface EMG, using the **NinaPro DB2** dataset
(40 subjects, 2000 Hz). This is a notebook-only research repo — there is no application, package, build,
lint, or test suite. "Running" means executing Jupyter notebooks.

## Working-tree layout vs. README/git (read this first)

The repo is mid-reorganization, so three views disagree. **Trust the working tree**, described here:

- `model_code.ipynb`, `preprocess_code.ipynb`, `graphs_code.ipynb`, `signal_visual_code.ipynb` — the four
  active notebooks, at the repo root (currently untracked).
- `db2_sb10/*.mat` — raw NinaPro DB2 subject-10 recordings (`S10_E1/E2/E3_A1.mat`), the *input* to
  preprocessing. Gitignored, present locally only.

`git ls-files` still shows an **older tracked structure** (`codes/`, `DB2/v1..v3/`, `report/`, `presentation/`)
that has been deleted from disk but not committed. `README.md` describes yet another layout including a
`demo/` directory that **does not exist** here (no `demo/`, no `requirements.txt`). When README and reality
conflict, the working tree wins; don't follow README paths blindly.

## The two-stage pipeline and its data contract

The notebooks form a producer→consumer pair that communicate **only through intermediate `.mat` files** —
there are no Python imports between them.

1. **`preprocess_code.ipynb`** — reads a *raw* DB2 `.mat` (native schema: `emg (N,12)`, `restimulus`,
   `rerepetition`, `stimulus`, …) and writes an *intermediate* `.mat`. Steps (via `nina_funcs`): per-channel
   z-score `normalise` (statistics from **training reps only**), Butterworth band-pass `filter_data`
   (20–500 Hz, order 4), 50 Hz `notch_filter` (Q=5), then `windowing` (win_len 300, stride 200) and one-hot
   `get_categorical`. Fixed experiment constants: `gestures=[1..9,12..17]` (15 classes),
   `train_reps=[1,2,4,6]`, `test_reps=[3,5]`.

2. **`model_code.ipynb`** — loads the intermediate `.mat`, builds/trains/evaluates the Transformer, writes an
   `.xlsx` of per-subject metrics, confusion-matrix PNGs, and a predictions `.mat`.

**Intermediate-file contract** (the interface both notebooks depend on): keys `train_data`, `train_labels`,
`test_data`, `test_labels`; data arrays shaped `(num_windows, 300, 12)`; labels one-hot. Preprocessing names
outputs `S{subject}_E1_A1_300_200_N.mat`; the model loader expects that name. Any change to window length,
class list, or label encoding must be mirrored on both sides or training breaks silently.

`graphs_code.ipynb` and `signal_visual_code.ipynb` are standalone figure generators (results plots and
preprocessing/FFT visualizations) — they have hardcoded numbers and paths and are not part of the pipeline.

## Model architecture (`model_code.ipynb`)

`build_model`: `Conv1D(64, k=8, relu, l2 bias) → MaxPooling1D(pool=17, stride=9) → Dropout → BatchNorm →
Time2Vec → transformer_encoder × N → GlobalAveragePooling1D → Dense(mlp_units) → Dense(15, softmax)`.
`Time2Vec` and `transformer_encoder` (multi-head attention + 1×1-conv feed-forward, residual + LayerNorm)
are custom cells defined in the notebook. It is **not** a pure Transformer — the CNN front-end is load-bearing.

## Critical gotchas

- **Hardcoded absolute paths everywhere**, from the original authors' machines (`E:\expirement_4\...`,
  `E:/DB2/`, `C:\Users\PC\Desktop\adamw\...`, `/Users/PC/Desktop/DB2_500/`). Every notebook needs its input
  and output paths rewritten before it will run here. There is no config file or CLI — paths are inline literals.
- **`model_code.ipynb`'s main loop is `for i in range(7, 8)`** — it processes subject 7 only, not all 40.
  Widen the range to run a full sweep.
- **The committed `model_code.ipynb` hyperparameters are an experimental config, not the paper's best.**
  It uses `Dropout(0.5)`/`mlp_dropout=0.6`/`dropout=0.5` and `Adam(lr=0.001)`. The README/paper report the
  *best* config as dropouts 0.25/0.4/0.3 and `lr=1e-4` (→ 128,536 params, 82.98% avg test accuracy). Reconcile
  before quoting numbers or reproducing results.
- **Data is gitignored**: `.mat`, `.h5`, `.pkl` are excluded (see `.gitignore`), so trained models and
  datasets never enter version control.

## Dependencies

No lockfile. Core: `tensorflow`/`keras`, `nina_funcs` (`pip install nina_funcs` — the DB2 preprocessing
helpers), `scipy`, `numpy`, `pandas`, `scikit-learn`, `mlxtend` (confusion-matrix plotting), `xlsxwriter`
(results export), `matplotlib`, `seaborn`, `pywt`. The raw NinaPro DB2 dataset is access-restricted and must
be obtained separately; only subject 10 is present locally under `db2_sb10/`.

## Running

There is no `make`/`npm`/`pytest`. Execute notebooks interactively (`jupyter lab`) or headless:

```bash
python -m nbconvert --to notebook --execute --inplace preprocess_code.ipynb   # after fixing paths
python -m nbconvert --to notebook --execute --inplace model_code.ipynb
```

Run `preprocess_code.ipynb` before `model_code.ipynb` — the latter consumes the former's `.mat` output.

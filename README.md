# Singing Onset Detection

This project finds **where each sung note starts** (the onset) and **where it ends** (the offset) in a recording of a singing voice.

It was my internship project.
 
- **Internship supervisor:** [Behnam Faghih](https://www.maynoothuniversity.ie/people/behnam-faghih) (Maynooth University)
- **Supervisor at Politecnico di Torino:** [Cristina Rottondi](https://www.linkedin.com/in/cristina-rottondi-b727042a2)

---

## The idea in short

1. We built a model that finds note starts in singing.
2. We trained it on **VocalSet** (singers doing vocal exercises). It works well there.
3. Then we tested it on other singing styles: **flamenco (TONAS)**, **simple songs (SVNote1)** and **children's songs (CSD)**.
4. On a new style, the model gets much worse.
5. But if we give it **just a few songs** of the new style, most of the drop goes away.

**Main finding:** finding note starts depends a lot on the singing style. A model works best on the styles it has heard.

---

## The model

- **Input:** the audio, turned into a Mel-spectrogram (a picture of the sound).
- **Model:** a small CNN followed by a 3-layer bidirectional GRU (a "CRNN").
- **Output:** for every 10 ms of audio, the model says how likely a note starts there, and how likely a note ends there.
- **Score:** onset F1 (higher is better, 1.0 is perfect). A predicted note start counts as correct if it is within **±50 ms** of the true one. This is the standard window in this field (used by mir_eval and MIREX). Some notebooks also show **±20 ms**, which is stricter.

---

## Main results

All numbers are onset F1 at ±50 ms, the average of 3 runs (3 seeds).

**1. The VocalSet model on new styles (no training on them)**

| Tested on | F1 |
|---|---|
| VocalSet | 0.926 |
| TONAS (flamenco) | 0.430 |
| SVNote1 (simple songs) | 0.697 |
| CSD (children's songs) | 0.770 |

**2. Adding a few songs of the new style**

The model starts from the VocalSet model, then trains a bit more on the new style.

| Dataset | No new songs | 3 new songs | All training songs |
|---|---|---|---|
| TONAS | 0.430 | 0.739 | 0.786 (48 songs) |
| SVNote1 | 0.697 | 0.944 | 0.978 (18 songs) |
| CSD | 0.770 | 0.867 | 0.891 (75 songs) |

For SVNote1, three people labeled the same songs. They agree with each other at 0.957, so that is about the best a model can do there.

**3. Other tests (all in the notebooks)**

- **Train on one dataset, test on all four:** every model is best on its own dataset and drops on the others.
- **All datasets together:** one model trained on all four is almost as good as four separate models.
- **Leave one out:** a model that never heard a style drops a lot on it.
- **Input types:** Mel-spectrogram, spectrogram and MFCC give about the same results. Raw audio is the weakest. None of them fixes the drop on new styles.

---

## The notebooks

All code is in the `codes` folder. The notebooks are in the order we made them. They are uploaded **as they were run**, with their outputs.

### Part 1: building the model (VocalSet)

These are single runs, scored at ±20 ms.

| # | Notebook | What it does | Onset F1 |
|---|---|---|---|
| 00 | onet-v2-ext4 | First working model (the baseline) | 0.831 |
| 01 | onset-v2-ext1 | Same model, label version 1 | 0.795 |
| 02 | onet-v2-ext2 | Same model, label version 2 | 0.777 |
| 03 | onet-v2-ext3 | Same model, label version 3 | 0.796 |
| 04 | onset-v2-ext4-flatlabel | Flat labels instead of soft labels | 0.765 |
| 05 | ext4-biggercrnn | Bigger model | 0.832 |
| 06 | ext4-biggercrnn-v2 | Even bigger model | 0.848 |
| 07 | onset-ext4-lstm | LSTM instead of GRU | 0.838 |
| 08 | onset-ext4-attention | GRU + attention | 0.832 |
| 09 | ext4-biggercrnn-augment | Bigger model + volume and noise changes | 0.851 |
| 10 | ext4-biggercrnn-augment-pitch | + pitch shift | 0.851 |
| 11 | ext4-biggercrnn-augment-specaug | + pitch shift + SpecAugment | 0.826 |
| 12 | onset-v2-ext4-biggercrnn-3seeds | **Final model**, trained 3 times. Saves the weights all later notebooks use | 0.834 ± 0.012 (0.926 at ±50 ms) |

### Part 2: other singing styles

| # | Notebook | What it does |
|---|---|---|
| 13 | tonas | VocalSet model on TONAS: why it drops, fine-tuning, forgetting and mixing |
| 14 | onset-v3-tonas-ftreplay-curve | TONAS: adding 0 to 36 songs, step by step |
| 15 | onset-v3-tonas-zeroshot-vs-finetune | TONAS: no training vs a few songs vs starting from zero |
| 16 | onset-v3-svnote-zeroshot-vs-finetune | Same test for SVNote1, plus how much the 3 labelers agree |
| 17 | csd-discovery | Cleans CSD and makes the 109 files we used (runs on a normal computer) |
| 18 | onset-v3-csd-zeroshot-vs-finetune | Same test for CSD |
| 19 | onset-v3-cross-dataset-4x4 | Train on each dataset, test on all four |
| 20 | onset-v3-all-together-and-leave-one-out | One model on all datasets, and leave one dataset out |

### Part 3: input types

| # | Notebook | What it does |
|---|---|---|
| 21 | onset-v4-input-spectrogram | Mel-spectrogram vs spectrogram |
| 22 | onset-v4-input-mfcc-raw | MFCC and raw audio |

---

## How to run

- The notebooks were made for **Kaggle**, with a **T4 GPU**.
- Most of them read the data from a private Kaggle dataset: `/kaggle/input/datasets/alirahmatpour/vocalset-onset`. To run them, put the data in your own folder and change that path.
- Notebooks 13 to 22 need the saved weights from notebook 12 (`biggerCRNN_ext4_seed0.pt`, `seed1.pt`, `seed2.pt`).
- Notebook 17 runs on a normal computer, not on Kaggle.
- Main libraries: PyTorch, torchaudio, NumPy, pandas, matplotlib.

The data and the saved weights are **not** in this repository. See [DATA.md](DATA.md) for the datasets and where to get them.

---

## Good to know

- **Part 1 notebooks were run once each, with no fixed random seed.** If you run them again, the numbers will change a little. From notebook 12 on, every result is the average of 3 runs.
- **Part 1 uses ±20 ms, Parts 2 and 3 use ±50 ms** as the main score.
- The numbers in this README are the ones saved inside the notebooks.

---

## License

See [LICENSE](LICENSE).

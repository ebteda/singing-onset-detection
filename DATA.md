# Data

The datasets are **not** in this repository. This page says what each one is and where to get it.

---

## 1. VocalSet + Annotated-VocalSet

**What it is:** 20 professional singers (9 men, 11 women) doing vocal exercises: scales, arpeggios and long notes, with many singing techniques. About 10 hours of audio.

The original VocalSet has no note labels. The labels (where each note starts and ends) come from **Annotated-VocalSet**. It has 4 label versions. We used version 4 ("ext4").

**How we used it:**
- We removed 147 files whose labels did not match the audio. That left **2,537 files**.
- The test singers are **female4** and **male6**.
- Notebooks 00 to 12 train on it. All later notebooks also test on it.

**Download:**
- Audio: [VocalSet on Zenodo](https://doi.org/10.5281/zenodo.1193957) (license: CC BY 4.0)
- Labels: [Annotated-VocalSet on Zenodo](https://zenodo.org/records/7061507)

---

## 2. TONAS (flamenco)

**What it is:** 72 short recordings of **flamenco** singing with no instruments, in 3 styles (Deblas, Martinete 1, Martinete 2). The notes were labeled by hand.

**How we used it:** we split it by singer, so no singer is in both training and testing. Used in notebooks 13 to 22.

**Download:** [TONAS on Zenodo](https://zenodo.org/records/1290722)
Access is on request. More info: [TONAS page at MTG-UPF](https://www.upf.edu/web/mtg/tonas).

---

## 3. SVNote1 (simple songs)

**What it is:** 27 recordings of simple songs by 9 singers (6 men, 3 women). **Three different people** labeled every song. That lets us measure how much humans agree with each other.

**How we used it:** we split it by singer. We train on the average of the 3 labels, and test against each person's labels. Used in notebooks 16 to 22.

**Download:** there is no public download. To get it, please contact [Behnam Faghih](https://www.maynoothuniversity.ie/people/behnam-faghih).

---

## 4. CSD, Children's Song Dataset

**What it is:** 100 children's songs (50 English, 50 Korean), sung by one professional female singer. Each song is recorded in 2 keys, so there are 200 files.

**How we used it:**
- Many of the original Korean labels follow the **sheet music**, not the real singing. So for Korean songs we used a newer, more accurate set of labels (the re-annotation).
- For English songs, we kept only the files whose labels follow the real singing.
- That gives **109 files**. **Notebook 17** makes these 109 files from the original download.
- We split it by song, so both keys of a song are always on the same side.
- Used in notebooks 18 to 22.

**Download:**
- Audio and original labels: [CSD on Zenodo](https://zenodo.org/record/4785016) (license: CC BY-NC-SA 4.0)
- Re-annotated Korean labels: [CSD re-annotation on GitHub](https://github.com/seyong92/CSD_reannotation)

---

## Also used once: Erkomaishvili (Georgian chant)

Notebook 05 has a short extra test on this dataset. It is not part of the main results.

**Download:** [Erkomaishvili Dataset at AudioLabs Erlangen](https://www.audiolabs-erlangen.de/resources/MIR/2019-GeorgianMusic-Erkomaishvili)

---

## Saved weights

Notebooks 13 to 22 use the 3 saved models from notebook 12 (`biggerCRNN_ext4_seed0.pt`, `seed1.pt`, `seed2.pt`). They are not in this repository. You can make them again by running notebook 12, or ask the author.

---

## Folder layout on Kaggle

The notebooks read the data from a private Kaggle dataset. If you want to run them, put your data in the same layout and change the path at the top of each notebook.

```
vocalset-onset/
├── kaggle_upload/kaggle_upload/audio/      VocalSet audio (one folder per singer)
├── annot_ext4_upload/annot_ext4_upload/annot/   Annotated-VocalSet labels, version 4
├── tonas_upload/                           TONAS audio + .notes.Corrected files
├── svnote_upload/
│   ├── audio/
│   └── annot/dooyong | gwangseok | sanghee/     the 3 labelers
├── csd_upload/
│   ├── audio/        the 109 clean files
│   ├── annot/        labels as start,end,pitch
│   └── index.csv
├── erko_upload/                            Erkomaishvili (notebook 05 only)
└── weights/                                the 3 saved models from notebook 12
```

# Forced alignment with the Montreal Forced Aligner (Python)

This module shows how to use the open-source [Montreal Forced Aligner (MFA)](https://montreal-forced-aligner.readthedocs.io/) to automatically align speech with its transcription at the **word** and **phoneme** level. The result is time-aligned annotation that is useful for phonetics, prosody and multimodal communication research.

Everything runs from one notebook: [`montreal_forced_aligner_python.ipynb`](montreal_forced_aligner_python.ipynb).

## What the pipeline does

1. **Normalises** each audio file (16 kHz, mono, 16-bit WAV, peak-normalised).
2. **Detects speech** (WebRTC VAD) and trims the recording to the main spoken part.
3. **Writes a matching `.txt` transcript** next to the trimmed audio.
4. **Runs MFA** (`mfa align_one`) with the acoustic model and dictionary you choose.
5. **Shifts the timings back** so they match the original, untrimmed audio.
6. **Exports** TextGrids and CSV tables with word and phone timings.
7. **Visualises** the alignment (waveform, spectrogram, pitch) with Parselmouth.

## Installation

MFA is easiest to install through Conda Forge, which pulls in Kaldi, Kalpy and OpenFST for you.

**1. Clone the repository**

```bash
git clone https://github.com/ShuguangSheng/forced_aligner_python.git
cd forced_aligner_python
```

**2. Create and activate the environment** (Python 3.10)

```bash
conda create -n aligner -c conda-forge python=3.10 montreal-forced-aligner -y
conda activate aligner
```

Check that it works:

```bash
python -V        # should show Python 3.10.x
mfa version      # should print the MFA version, e.g. 3.3.x
```

**3. Install the Python packages** (versions are pinned in `requirements.txt`)

```bash
pip install -r requirements.txt
```

**4. Download the MFA models**

The acoustic model and the dictionary must use the **same phoneset** (e.g. ARPAbet).

```bash
# English (US, ARPAbet)
mfa model download acoustic english_us_arpa
mfa model download dictionary english_us_arpa

# Dutch
mfa model download acoustic dutch_cv
mfa model download dictionary dutch_cv
```

Check the installation:

```bash
mfa model list installed
mfa model inspect acoustic english_us_arpa
mfa model inspect dictionary english_us_arpa
```

## Preparing your data

| Folder | Content |
|---|---|
| `audio/` | Your recordings (WAV or MP3), e.g. `audio1_1_english.wav` |
| `transcripts/` | A CSV with the columns `id` and `transcript` (one row per recording) |

Example `transcripts/transcripts_english.csv`:

```csv
id,transcript
audio_1_1_english,he didn't feel like working
audio_1_2_english,she did not find keys in the bag
```

**Important:** the N-th transcript must belong to the N-th audio file (files are sorted by name). Adjust the glob patterns in Section 4 of the notebook (e.g. `*_english.wav`, `*_english.csv`) and the transcript column name if yours differ.

## Running the module

1. Open `montreal_forced_aligner_python.ipynb` in Jupyter or VS Code and select the `aligner` environment as kernel.
2. Run **Section 1** to import the libraries and create the working folders.
3. Run **Sections 2 and 3** to define the preprocessing helpers and `perform_full_forced_alignment()`.
4. In **Section 4**, point the input folders at your data and choose the model, for example `mfa_model_name="english_us_arpa"` or `"dutch_cv"`, then run the alignment.
5. Run **Section 5** to plot the alignments over the audio.

## Outputs

| Folder | Content |
|---|---|
| `aligned_output/` | `<file>_aligned.TextGrid` (word and phone tiers, timed on the original audio), plus `all_word_alignments<timestamp><model>.csv` and `all_phone_alignments<timestamp><model>.csv` |
| `normalized_audio/` | Normalised audio (intermediate) |
| `trimmed_audio/` | Trimmed audio and transcript `.txt` files (intermediate) |
| `textgrid/mfa_raw_output/` | Raw MFA TextGrids (intermediate) |

The CSV tables contain `file_id`, the word or phone, `start_time_seconds`, `end_time_seconds` and `duration_seconds`.

## Troubleshooting

- `mfa: command not found`: activate the `aligner` environment before starting Jupyter.
- Alignment fails for a file: make sure the dictionary and acoustic model share the same phoneset and that the transcript matches the language of the recording.
- Wrong transcript for a recording: check that audio files and CSV rows are in the same order.

## Licence

This module is released under the open licence [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): you are free to share and adapt it, provided you give appropriate credit. Third-party material (e.g., the [Montreal Forced Aligner](https://montreal-forced-aligner.readthedocs.io/)) remains subject to its original licence.

See [`LICENSE`](LICENSE) for the full legal text.

## Related resources

- [Montreal Forced Aligner documentation](https://montreal-forced-aligner.readthedocs.io/en/latest/installation.html)
- [EnvisionBox Project](https://www.envisionbox.org)

[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on006136-blue)](https://doi.org/10.82901/nemar.on006136)

# OWM-Dataset

## Description
The dataset contains processed intracranial EEG recordings from frontal (LMFG, RMFG) and temporal (LMTG, RMTG) areas of 13 subjects (epilepsy patients) while they performed a load-3 object working memory task. Please see the associated publication (Paper): https://doi.org/10.1016/j.neuroimage.2026.121718

## Data structure

### Included trials
The dataset includes trials which were used for the final analyses (i.e., after artifact rejection; see the Methods section of the Paper for a full description of preprocessing procedures). Note that since some artifact rejection procedures were performed at the single-trial level, trial indexes are not matched across channels even for the same subject (i.e., trial 1 of channel 1 may not correspond to trial 1 of channel 2) and have to be read separately. The sourcedata/ folder contains per-subject trial indexes for trial matching.

### Trial structure
Each trial is 6498 ms long (1000 ms of fixation, 1500 ms of encoding and 3998 ms of delay). 

### Storage format
To comply with the .edf format, trials for every channel were concatenated into a single one-dimensional array. Due to a different number of trials across channels, each array was padded with 0s on the right, ensuring the same data length for all channels within a subject. The total number of concatenated trials per channel and the padding length are recorded in the "_channels.tsv" file for each subject.

### Performance
The sourcedata/ folder contains performance results for every subject. Rows of each table correspond to trials (the order matches LFP recordings). Columns represent whether the subject selected the presented stimuli during the search period (0 = no, 1 = yes).

## Reading the data and replicating the results
The Paper repository (https://github.com/V-Marco/FT-bursting-WM) includes a Python function for reading the data, performing trial matching, appending performance information, and representing the recordings as a 2D table. The repository also includes examples on replicating the main figures. 

## Channel names follow the data headers (18 Oct 2026)

In 14 recordings the channel list of channels.tsv did not equal the data-file header (names, order or count), so `mne_bids.read_raw_bids` stopped with 'Channel mismatch' (or 'Duplicate channel names'); EEGDash renamed the data channels by position to the channels.tsv names, which gives wrong labels wherever the two lists are not aligned. Since 18 Oct 2026 every channels.tsv lists exactly the header channels, in header order, with the header names (MNE names: duplicated header labels numbered -0, -1).

Rows were matched to header channels BY NAME, never by position (exact; then case/whitespace; then the recorder prefix 'EEG '/'POL ', '_' and zero padding; then a trailing '-<reference>' token). Matched rows keep all their columns (type, units, status, ...); 143 rows took the header name.

The data headers of this dataset name the channels by contact number only (e.g. `26`), while channels.tsv and electrodes.tsv used region-prefixed names (e.g. `LMTG-26`). The channels.tsv name is now the header name and the former name is kept in the new column `contact_name` (described in the root channels.json); it equals the electrodes.tsv `name`, so positions can be attached with `raw.rename_channels(dict(zip(ch['name'], ch['contact_name'])))`.

## Electrode locations (19 Oct 2026)

Sub-MU3 electrodes.tsv -999 sentinel -> n/a. Evidence: One row of sub-MU3_electrodes.tsv used -999 as a missing-value sentinel in x/y/z, which MNE read as -999 mm; it is now n/a. No other value changed.

This dataset mirrors OpenNeuro; the same change belongs upstream.

# CMRM Datasets — Experimental Data Management

## 1. Overview

The CMRM Dataset Module defines the data organization and management framework for the Cranial Mechanical Resonance Mapping (CMRM) project.

The project uses multi-sensor acceleration measurements collected from an experimental mechanical headform or suitable non-human phantom.

The dataset framework is designed to support:

* Raw sensor data collection.
* Multi-channel measurement storage.
* Experimental metadata management.
* Signal-quality validation.
* Processed signal storage.
* Frequency-domain analysis.
* Mechanical response characterization.
* Repeatability studies.
* Future machine-learning research.

The dataset structure must preserve the relationship between the original measurements, experimental conditions, processing operations, and generated results.

**Important:** CMRM datasets are experimental mechanical measurements. They must not be presented as clinically validated human diagnostic datasets.

## 2. Dataset Objectives

The primary objectives are:

1. Establish a consistent dataset structure.
2. Preserve original sensor measurements.
3. Record experimental conditions.
4. Identify individual sensor channels.
5. Maintain accurate timestamps.
6. Separate synthetic and experimental data.
7. Document preprocessing operations.
8. Support reproducible signal analysis.
9. Enable future machine-learning experimentation.
10. Maintain dataset integrity and traceability.

## 3. Dataset Architecture

The proposed dataset pipeline is:

```text
             EXPERIMENTAL HARDWARE
                      |
                      |
              ESP32 DATA ACQUISITION
                      |
                      |
             FOUR ADXL345 SENSORS
                      |
                      |
                RAW DATASET
                      |
                      |
              DATA VALIDATION
                      |
                      |
             PREPROCESSING PIPELINE
                      |
                      |
             PROCESSED DATASET
                      |
                      |
             FEATURE EXTRACTION
                      |
                      |
             MECHANICAL RESPONSE
                   ANALYSIS
                      |
                      |
             MRP DATA GENERATION
                      |
                      |
             RESEARCH AND AI DATASETS
```

The dataset architecture should retain raw measurements even after processing.

## 4. Dataset Categories

CMRM data should be separated into clearly identified categories.

### 4.1 Raw Experimental Dataset

Contains measurements collected directly from the hardware.

Typical contents:

* Sensor timestamps.
* X-axis acceleration.
* Y-axis acceleration.
* Z-axis acceleration.
* Sensor identification.
* Acquisition configuration.
* Experiment identification.
* Hardware status.

Raw data must not be silently overwritten or modified during preprocessing.

### 4.2 Processed Dataset

Contains measurements generated through documented signal-processing operations.

Examples:

* Baseline-corrected signals.
* Filtered signals.
* Aligned measurements.
* Frequency spectra.
* Extracted signal features.

Each processed dataset should identify the source raw dataset and processing configuration.

### 4.3 Synthetic Dataset

Contains artificially generated signals for software development and algorithm validation.

Potential applications:

* FFT validation.
* Frequency detection.
* Filtering experiments.
* Noise analysis.
* Signal-quality testing.
* Machine-learning pipeline development.

Synthetic data must always be labelled as synthetic.

It must never be represented as real sensor measurements.

### 4.4 Mechanical Phantom Dataset

Contains measurements collected from controlled non-human experimental mechanical models.

Each experiment should document:

* Phantom identification.
* Mechanical configuration.
* Sensor mounting positions.
* Excitation configuration.
* Environmental conditions, where relevant.
* Acquisition settings.
* Experimental observations.

These datasets should not be treated as human clinical datasets.

## 5. Proposed Directory Structure

```text
datasets/
│
├── README.md
│
├── sample_data/
│   │
│   ├── synthetic/
│   │   ├── sample_signal.csv
│   │   └── synthetic_metadata.json
│   │
│   ├── experimental/
│   │   └── README.md
│   │
│   └── processed/
│       └── README.md
│
├── raw/
│   └── .gitkeep
│
├── processed/
│   └── .gitkeep
│
├── metadata/
│   └── dataset_schema.json
│
└── documentation/
    ├── data_collection.md
    ├── data_validation.md
    └── dataset_versioning.md
```

This is the proposed organization. Large datasets and private experimental measurements should not automatically be committed to the public Git repository.

## 6. Sensor Data Format

Each measurement should contain sufficient information to identify its origin and interpret its values.

Recommended CSV structure:

| Column        | Description                  |
| ------------- | ---------------------------- |
| experiment_id | Unique experiment identifier |
| timestamp_ms  | Measurement timestamp        |
| sensor_id     | Sensor identifier            |
| channel       | TCA9548A channel             |
| ax            | X-axis acceleration          |
| ay            | Y-axis acceleration          |
| az            | Z-axis acceleration          |
| unit          | Acceleration unit            |
| quality       | Measurement quality status   |

Example CSV:

```csv
experiment_id,timestamp_ms,sensor_id,channel,ax,ay,az,unit,quality
CMRM_EXP_001,1000,S1,0,0.12,0.08,0.95,m/s2,valid
CMRM_EXP_001,1001,S1,0,0.14,0.07,0.96,m/s2,valid
CMRM_EXP_001,1002,S1,0,0.13,0.09,0.94,m/s2,valid
CMRM_EXP_001,1003,S1,0,0.15,0.08,0.95,m/s2,valid
```

These values are illustrative and are not actual experimental measurements.

## 7. Experimental Metadata

Every experiment should have a metadata file.

Example:

```json
{
  "experiment_id": "CMRM_EXP_001",
  "experiment_name": "Mechanical Phantom Vibration Test",
  "dataset_type": "experimental",
  "created_at": "2026-11-05T10:00:00",
  "phantom_id": "HF_001",
  "hardware": {
    "controller": "ESP32",
    "accelerometer": "ADXL345",
    "sensor_count": 4,
    "multiplexer": "TCA9548A"
  },
  "sensor_configuration": {
    "S1": "Left temporal",
    "S2": "Right temporal",
    "S3": "Frontal",
    "S4": "Occipital"
  },
  "acquisition": {
    "sampling_rate_hz": 1000,
    "acceleration_unit": "m/s2"
  },
  "excitation": {
    "method": "Mechanical vibration",
    "frequency_range_hz": null
  },
  "status": "recorded"
}
```

Metadata values must reflect the actual experimental setup.

Do not use an example sampling frequency or sensor configuration as if it were verified hardware information.

## 8. Data Collection Protocol

A consistent data collection procedure is essential for reproducible experiments.

### Step 1 — Prepare the Experimental Setup

* Verify the ESP32 controller.
* Verify the ADXL345 sensors.
* Verify the TCA9548A multiplexer.
* Check sensor mounting.
* Confirm power stability.
* Inspect the excitation system.
* Confirm that the mechanical phantom is securely positioned.

### Step 2 — Configure the Experiment

Record:

* Experiment ID.
* Phantom ID.
* Sensor locations.
* Sampling configuration.
* Excitation settings.
* Acquisition duration.
* Date and time.

### Step 3 — Verify Sensor Communication

Check that all expected sensors respond.

Confirm that each sensor is associated with the correct multiplexer channel.

### Step 4 — Perform Baseline Acquisition

Acquire measurements without the intended mechanical excitation.

This helps characterize the baseline vibration and background noise.

### Step 5 — Perform Controlled Acquisition

Apply the defined experimental excitation and record the sensor measurements.

Record the actual excitation reference wherever possible.

### Step 6 — Save Raw Measurements

Save the original data with timestamps and metadata.

### Step 7 — Validate the Dataset

Check:

* Missing samples.
* Invalid values.
* Timestamp irregularities.
* Sensor availability.
* Measurement saturation.
* Acquisition interruptions.

### Step 8 — Process the Dataset

Perform documented preprocessing and signal analysis.

### Step 9 — Generate Results

Generate feature records, visualizations, and MRP outputs.

## 9. Data Quality Standards

Dataset quality should be assessed before analysis.

| Quality Parameter     | Validation                                  |
| --------------------- | ------------------------------------------- |
| Completeness          | Check for missing measurements              |
| Validity              | Check numerical values                      |
| Timestamp Integrity   | Verify measurement timing                   |
| Sampling Consistency  | Check actual sampling intervals             |
| Sensor Identification | Verify channel mapping                      |
| Unit Consistency      | Confirm acceleration units                  |
| Signal Saturation     | Identify clipped measurements               |
| Noise                 | Evaluate background and unwanted components |
| Repeatability         | Compare repeated experiments                |
| Metadata Completeness | Verify experiment information               |

A dataset should not be marked valid merely because a CSV file was successfully generated.

## 10. Data Labelling Strategy

Consistent labels are necessary for dataset organization and future analysis.

Suggested labels:

| Label        | Description                            |
| ------------ | -------------------------------------- |
| synthetic    | Artificially generated signal          |
| experimental | Measurement from experimental hardware |
| phantom      | Measurement from mechanical phantom    |
| raw          | Original acquired measurement          |
| processed    | Preprocessed measurement               |
| validated    | Passed defined quality checks          |
| warning      | Contains documented limitations        |
| invalid      | Failed required validation             |
| incomplete   | Missing required measurements          |

These labels describe data provenance and quality. They are not medical classifications.

## 11. Feature Dataset

The feature dataset stores numerical characteristics extracted from the raw or processed measurements.

Proposed structure:

| Feature            | Description                            |
| ------------------ | -------------------------------------- |
| rms_acceleration   | RMS acceleration                       |
| peak_acceleration  | Maximum absolute acceleration          |
| peak_to_peak       | Peak-to-peak acceleration              |
| dominant_frequency | Strongest selected spectral component  |
| spectral_energy    | Energy within a defined frequency band |
| frf_magnitude      | Frequency-response magnitude           |
| frf_phase          | Frequency-response phase               |
| coherence          | Input-output coherence                 |
| symmetry_index     | Left-right response comparison         |
| quality_status     | Measurement quality                    |

Feature values must include their units and processing configuration where applicable.

## 12. Machine-Learning Dataset Preparation

Future CMRM research may investigate machine-learning techniques for experimental signal characterization.

Potential research applications:

* Signal-quality classification.
* Noise and artifact detection.
* Spectral-pattern clustering.
* Experimental condition classification.
* Mechanical response pattern recognition.
* Anomaly detection.

### 12.1 Feature Preparation

Potential input features include:

* RMS acceleration.
* Peak acceleration.
* Dominant frequency.
* Spectral energy.
* Frequency-response magnitude.
* Sensor-wise response differences.
* Repeatability measurements.

### 12.2 Dataset Splitting

When training machine-learning models, avoid placing measurements from the same experimental session or physical phantom into both training and testing sets when this could cause data leakage.

Prefer splitting by independent experiment, phantom, or acquisition session according to the research objective.

### 12.3 Model Evaluation

Potential evaluation metrics:

* Accuracy.
* Precision.
* Recall.
* F1-score.
* Confusion matrix.
* ROC-AUC, where appropriate.

For regression tasks:

* Mean Absolute Error.
* Mean Squared Error.
* Root Mean Squared Error.
* R² score.

The evaluation metric must match the actual research problem.

### 12.4 Research Integrity

Do not create artificial labels that imply a medical condition.

Synthetic and experimental datasets must remain distinguishable.

Machine-learning outputs should not be described as clinically meaningful without appropriate validation.

## 13. Dataset Versioning

Every dataset release should have a version identifier.

Example:

```text
CMRM-DATASET-v0.1.0
CMRM-DATASET-v0.2.0
CMRM-DATASET-v1.0.0
```

Recommended versioning:

* MAJOR: Significant structural or schema changes.
* MINOR: New compatible data categories or features.
* PATCH: Corrections to metadata or documentation.

Each dataset version should document:

* Dataset identifier.
* Version.
* Collection date.
* Data source.
* Hardware configuration.
* Processing version.
* Known limitations.
* Changes from previous versions.

## 14. Data Integrity and Backup

The dataset system should preserve the integrity of experimental measurements.

Recommended practices:

* Keep original raw datasets read-only after acquisition.
* Maintain separate processed datasets.
* Use checksums to detect accidental file changes.
* Back up important experimental records.
* Maintain experiment identifiers.
* Document data transformations.
* Avoid duplicate or ambiguous filenames.
* Record dataset version information.

SHA-256 is a suitable checksum algorithm for detecting accidental file changes.

## 15. GitHub Dataset Management

The public GitHub repository should contain only datasets that are appropriate for public distribution.

Recommended public contents:

* Small synthetic demonstration signals.
* Example metadata files.
* Dataset schemas.
* Data collection documentation.
* Data validation scripts.
* Example processed outputs.

Avoid committing:

* Large raw experimental datasets without a clear reason.
* Private or identifiable participant information.
* Credentials or API keys.
* Unreleased research data.
* Files containing sensitive information.

For large datasets, consider appropriate external storage or dataset hosting with documented access and licensing arrangements.

## 16. Dataset Documentation Requirements

Every publicly released dataset should include:

1. Dataset description.
2. Data provenance.
3. Collection methodology.
4. Hardware configuration.
5. Sensor arrangement.
6. Data format.
7. Units.
8. Sampling configuration.
9. Preprocessing details.
10. Quality-control procedure.
11. Known limitations.
12. Licensing and access conditions.

A dataset should not be released without enough information for another researcher to understand its origin and limitations.

## 17. Current Development Status

| Component                      | Status              |
| ------------------------------ | ------------------- |
| Dataset architecture           | Proposed            |
| Raw data schema                | Proposed            |
| Experimental metadata schema   | Proposed            |
| Synthetic sample data          | To be created       |
| Hardware experimental datasets | To be collected     |
| Data validation pipeline       | To be implemented   |
| Feature dataset                | To be implemented   |
| Dataset versioning             | Proposed            |
| Machine-learning dataset       | Future development  |
| Public dataset release         | Not yet established |

Update this table as actual data collection and implementation progress.

## 18. Future Development

Potential future improvements include:

* Automated dataset validation.
* Automatic metadata generation.
* Experiment-to-dataset traceability.
* Dataset visualization tools.
* Advanced data-quality scoring.
* Automated feature extraction.
* Reproducible dataset preparation.
* Machine-learning benchmark datasets.
* Public research dataset releases.

## 19. Disclaimer

CMRM datasets are intended for experimental mechanical vibration research and biomedical engineering development.

They are not clinically validated datasets and must not be used to establish medical diagnoses or clinical conclusions.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Module:** Dataset Management
**Version:** 0.1.0
**Status:** Proposed Dataset Architecture

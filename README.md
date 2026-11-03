# NeuroSense-X

Experimental multi-sensor vibration measurement and analysis platform
(ESP32 + TCA9548A + 3x MPU6050, optional bone-conduction excitation).
**Research prototype. Not a clinical or diagnostic device. No physiological claim is supported by anything in this repo.**

## Status (honest)

| Component | State |
|---|---|
| `neurosense/processing.py` (resampling, time/spectral features, Welch, coherence, H1 FRF, delay) | Implemented; **verified on synthetic signals with known answers** |
| `neurosense/protocol.py` (wire format, NaN for failed reads, malformed-line counting) | Implemented and tested |
| `neurosense/simulate.py` (sequential-sampling-aware simulator, always tagged `SIMULATED`) | Implemented and tested |
| `neurosense/mrp.py` (Mechanical Resonance Profile with raw-data SHA-256, version, validation status) | Implemented; tested on simulated data only |
| `firmware/neurosense_x/neurosense_x.ino` | Written; **not compiled, not run on hardware** |
| Serial capture, backend/API, database, dashboard, MRM plots, experiment manager, AI | **Not started** |
| Excitation (PAM8403 + transducer) | **Not started**; see safety notes |

Run: `pip install -r requirements.txt && python -m pytest -q` (21 tests).
Simulated demo data: `python -m neurosense.simulate`.

## Hardware limits found in the design (read before experiments)

1. **Transducer vs. sensor bandwidth.** The transducer is rated 300 Hz–19 kHz. The MPU6050 accelerometer
   has at most ~260 Hz bandwidth (DLPF off) and 1 kHz internal rate, so Nyquist is <= 500 Hz.
   Realistic firmware here runs at 250 Hz (Nyquist 125 Hz). **The current sensors cannot measure the
   transducer's response.** Minimum justified fix: a wider-band accelerometer (e.g. ADXL345 on SPI, or an analog
   MEMS part such as ADXL1002 read by a fast ADC) plus a measured reference of the drive signal.
2. **Aliasing.** Reading a 260 Hz-bandwidth sensor at 250 Hz folds 125–260 Hz content into the band.
   Firmware therefore defaults to DLPF_CFG=2 (~94 Hz); this attenuates amplitudes near its corner, so
   do not trust absolute amplitude above ~60 Hz without calibration against a reference.
3. **Not simultaneous.** One I2C bus + mux means sensors are read sequentially (~0.5 ms apart is an estimate;
   measure it). Each sample carries its own timestamp; processing resamples onto a common grid.
   Phase/delay below ~1 ms is not meaningful.
4. **Throughput.** 3 sensors x 250 Hz x ~70 B/line is ~52 kB/s at 921600 baud (limit ~92 kB/s). Watch
   `# missed_cycles` in the status lines. Do not raise the rate without re-checking.
5. **Excitation drive.** The PAM8403 needs an analog audio input; ESP32 I2S is digital and cannot connect to it.
   Use the ESP32 DAC (GPIO25/26) through a series resistor/divider and a DC-blocking capacitor, or an I2S DAC,
   starting at very low level and monitoring temperature. Connect the transducer across one BTL output pair
   (e.g. L+ to L-), never to ground. The transducer's mechanical output must be measured, not inferred.
6. **Output-only spectrum is not an FRF.** `frf_h1` needs a *measured* input. Without it, spectral peaks
   can be fixture, cable or enclosure resonances.

## Wire protocol

Header lines `# key=value`, then `S,seq,t_us,sensor,valid,err,ax_g,ay_g,az_g,gx_dps,gy_dps,gz_dps,temp_c`.
Failed read: `valid=0`, `err!=0`, empty values (parsed as NaN). err: 1 mux, 2 read, 3 not initialised, 4 WHO_AM_I mismatch.
Commands to the ESP32: `x` pause, `s` resume, `r` re-detect sensors.

## Supported scientific claims right now

Only: the processing code returns correct frequency, RMS, coherence, delay and FRF values for synthetic signals
with known truth. Nothing about physical hardware or cranial structures has been established.

## Next experiments (in order)

1. Compile/flash; confirm `sensor_ready=1,1,1`; record `missed_cycles` and achieved rate over 10 min.
2. Static noise floor per sensor and per axis; compare the three sensors on the same rigid plate.
3. Known-frequency shaker/phone-vibration test below 100 Hz to check amplitude/frequency recovery.
4. Mounting repeatability: remount and repeat 10x; report spread of RMS and dominant frequency.
5. Only then: wider-band sensor, measured excitation reference, FRF on a benchtop phantom.
Human experiments require excitation characterization, a safety review and institutional ethics approval first.

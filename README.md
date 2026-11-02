# Format profiles for TextField numeric and date fields

Connects FormatKit, TemporalKit, SettingsKit and FieldKit with TextField-based masked fields. The numeric engines,
temporal values and settings stores remain independently usable.

`FormattingPreferences(store)` exposes `Store` (the underlying transactional
Settings engine), `Profile()` (a detached resolved FormatProfile), and `Snapshot()`
(an immutable settings envelope capturing resolved separators and grouping).
Pass snapshots to FieldKit EditSession/CommitField. Persist requested preferences
with `Store.Export()`; snapshots freeze system conventions for one edit.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add formatcontrols --git https://github.com/BuoyBay/formatcontrols.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.
BPM also resolves `formatkit`, `temporalkit`, `settingskit`, `fieldkit`, `moneykit`, `maskkit`, `timezonekit`.

## API entrypoints

- `FormattingPreferences(store)`
- `Store`
- `Profile()`
- `Snapshot()`
- `Store.Export()`

## Build and API reference

Source is in `modules/`, with native/runtime inputs where required. Use the
[package guide](docs/API.md) for backend-specific build prerequisites, ownership
rules and supported targets. Rebuilding a native backend requires that target's
compiler and SDK; sealing an archive alone does not rebuild the backend.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)

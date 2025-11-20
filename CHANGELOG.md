# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- **Barcode Configuration: Panel Display on Specimen Labels** (2025-11-20)
  - Added "Show panel" checkbox option in Barcode Configuration page under
    Specimen optional fields
  - Panel names are now displayed on specimen barcode labels when enabled
  - Panels are deduplicated when multiple tests belong to the same panel
  - Panels take priority over individual tests when both options are checked
  - Implementation includes:
    - Backend: `SPECIMEN_FIELD_PANELS` property, `panelsCheck` form field,
      service persistence
    - Frontend: Checkbox UI in `/MasterListsPage#barcodeConfiguration`
    - Label Generation: `SpecimenLabel.java` updated to extract and display
      panel data
    - Internationalization: English ("Panels") and French ("Panneaux")
      translations
  - Configuration is persisted in `site_information` table
  - Default value set to `false` to maintain backward compatibility

### Changed

### Deprecated

### Removed

### Fixed

### Security

---

## Historical Changes

For changes prior to this changelog, please refer to the Git commit history.

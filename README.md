<h1>
  <img src="./logo.png" width="42" align="center">
  OMIXIS
</h1>

**OMIXIS** is a local graphical platform for LC-MS/MS untargeted metabolomics data processing. It integrates peak detection, peak-quality assessment, feature alignment, and feature-guided MS/MS identification in one reproducible workflow.

## Overview

Untargeted LC-MS/MS analysis often requires separate tools for feature detection, alignment, MS/MS-spectrum extraction, and compound annotation. OMIXIS keeps these steps in one local desktop workflow: raw mzML files are processed into an aligned quantitation feature table, experimental MS/MS spectra are linked to the corresponding features by precursor m/z and retention time, and matched spectra are annotated with MS-FINDER.

All processing is performed locally. No raw data need to be uploaded to an external service.

## Workflow

![OMIXIS workflow](./workflow.png)

## Key features

- **Integrated quantitation pipeline**
  Combines MS-Picker, MS-Point, and MS-Aligner to produce an aligned feature-level quantitation table.

- **Feature-guided MS/MS identification**
  Converts experimental MS/MS spectra to MSP files, matches them to aligned features using precursor m/z and retention time, and submits only matched spectra to MS-FINDER.

- **Feature-level identification output**
  Returns the highest-scoring MS-FINDER annotation to the original quantitation feature table, including compound name, score, InChIKey, and matched MSP filename.

- **Optional authentic-standard evidence**
  Supports an authentic-standard list containing m/z, retention time, and molecular formula. The list is used for MSI Level 1/2 evidence classification; it is not required for MS-FINDER annotation.

- **Polarity-aware processing**
  Positive and negative ion data are processed separately. Mixed-polarity mzML input is rejected.

- **Local execution and reproducibility**
  Run parameters and identification configuration are saved with each result folder.

## Workflow modules

1. [**MS-Picker**](https://github.com/ICMOL/MS-Picker) detects chromatographic features from LC-MS data.
2. [**MS-Point**](https://github.com/ICMOL/MS-Point) evaluates peak quality.
3. [**MS-Aligner**](https://github.com/ICMOL/MS-Aligner) aligns features across samples and produces the quantitation feature table.
4. **Identification** links aligned features to experimental MS/MS spectra and uses MS-FINDER for formula and structure annotation.

## Identification documentation

- [Identification workflow](docs/identification.md)
- [MS-FINDER method guide](docs/msfinder-method-guide.md)
- [Standard-list format and MSI evidence](docs/standard-list-format.md)

## Example files

- [Generic positive-ion MS-FINDER method](examples/MSFINDER_positive_method_generic.txt)
- [Generic negative-ion MS-FINDER method](examples/MSFINDER_negative_method_generic.txt)
- [Standard-list example](examples/standard_list_example.txt)

## Scope

OMIXIS reports computational annotations and their supporting evidence. A high MS-FINDER score or MSI Level 2 result is not equivalent to confirmation by an authentic standard. Use an authentic-standard comparison under matched analytical conditions when a confirmed identification is required.

# MS-FINDER method parameter guide

This guide applies to **Untargeted Compound ID V2 in OMIXIS 2.0** and the bundled **MS-FINDER v3.61** console workflow. It explains the generic positive- and negative-ion method examples, OMIXIS-specific feature-to-spectrum matching settings, MS-FINDER formula and structure parameters, database selection, spectral-library search, batch settings, validation, and troubleshooting.

> **Scope boundary:** `RetentionTimeTolerance` (or `RTTolerance`) is an OMIXIS feature-to-spectrum matching parameter, not a native MS-FINDER setting. OMIXIS removes it when creating `MSFINDER_method_effective.txt`. All other supported settings are passed to MS-FINDER.

## Quick conclusions

- Feature-to-MSP matching is controlled by `Ms1Tolerance`, `MassToleranceType`, and `RetentionTimeTolerance`.
- `Ms2Tolerance` and `RelativeAbundanceCutOff` affect downstream MS/MS fragment matching, not the initial feature-to-MSP match.
- Wider mass tolerances, more databases, higher candidate limits, and deeper fragmentation trees normally increase both candidate count and run time.
- Use separate positive- and negative-ion method files. OMIXIS verifies polarity from mzML MS/MS metadata and rejects mixed-polarity input.
- Generic method files are starting templates, not universal instrument-calibration files. Validate tolerances with known compounds or QC data acquired under the same analytical conditions.

## 1. How OMIXIS uses the method file

An MS-FINDER method is a plain-text `key=value` file. Blank lines are ignored. Lines beginning with `#` or `;` are comments. Preserve the parameter spelling used by MS-FINDER; write Boolean values consistently as `True` or `False`.

### 1.1 Execution flow

1. OMIXIS reads `Ms1Tolerance`, `MassToleranceType`, and the custom `RetentionTimeTolerance` or `RTTolerance` setting.
2. For every aligned feature, OMIXIS compares the central feature m/z and `RT(min.)` with experimental MS/MS spectra converted to MSP.
3. Only spectra matching both precursor m/z and RT are written to the MS-FINDER input folder.
4. OMIXIS creates `MSFINDER_method_effective.txt`, removes the custom RT setting, and passes the remaining method to MS-FINDER.
5. MS-FINDER performs spectral-library search, molecular-formula prediction, and structure search according to the enabled settings.
6. OMIXIS writes feature-level TSV results for the integrated Result Viewer.

The matching rules are:

```text
|MS2 precursor m/z - feature m/z| <= allowed MS1 tolerance
|MS2 scan start time - feature RT| <= RetentionTimeTolerance
```

When `MassToleranceType=Ppm`, the allowed Da difference is:

```text
feature m/z * Ms1Tolerance * 10^-6
```

### 1.2 Do not confuse the three parameter groups

| Purpose | Key settings | Processing stage |
|---|---|---|
| Feature-to-MSP matching | `Ms1Tolerance`, `MassToleranceType`, `RetentionTimeTolerance` | OMIXIS; determines which MSP spectra are submitted |
| Molecular-formula candidates | `Ms1Tolerance`, isotope and element rules, `FormulaMaximumReportNumber` | MS-FINDER Formula finder |
| MS/MS and structure candidates | `Ms2Tolerance`, `RelativeAbundanceCutOff`, `TreeDepth`, database settings | MS-FINDER spectral/structure search |

### 1.3 OMIXIS-specific RT settings

| Setting | Type/unit | Meaning | Guidance |
|---|---|---|---|
| `RetentionTimeTolerance` | Number; min | Maximum difference between central feature RT and MS2 scan start time | Recommended explicit setting; if omitted, OMIXIS uses `0.02 min` (1.2 s) |
| `RTTolerance` | Number; min | Alias of `RetentionTimeTolerance`; case, spaces, and underscores are normalized | Use one name only to avoid ambiguity |

`Start RT (optional)` and `End RT (optional)` are feature-table columns, not method parameters. They are retained in the output, but current matching uses the central feature RT plus or minus `RetentionTimeTolerance`.

### 1.4 Required values and format validation

- `Ms1Tolerance` must be a positive finite number.
- `MassToleranceType` must resolve to `Da` or `Ppm`. OMIXIS also recognizes equivalent forms such as `Dalton`, `Daltons`, and `PartsPerMillion`, but shared examples should use `Da` or `Ppm`.
- If present, `RetentionTimeTolerance` or `RTTolerance` must be a positive finite number in minutes.
- `MinesAllways` and `PubChemAllways` use the spelling present in MS-FINDER v3.61. Do not change `Allways` to `Always`.
- When a user-defined structure or spectral database is enabled, the configured file must exist and be readable by the current Windows user.

## 2. Formula finder parameters

Formula finder generates and ranks molecular-formula candidates from precursor m/z, isotope evidence, and element rules. These settings change candidate space, score, and run time.

| Setting | Type/unit | Meaning | Guidance |
|---|---|---|---|
| `LewisAndSeniorCheck` | Boolean | Applies Lewis/Senior valence rules to reject chemically implausible formulas | Normally `True` |
| `Ms1Tolerance` | Number; Da/ppm | MS1 mass tolerance for formula generation; OMIXIS also uses it for feature-to-MSP precursor matching | Choose from measured instrument error and calibration quality |
| `IsotopicAbundanceTolerance` | Number; internal scoring scale | Sigma/tolerance used by Gaussian isotope-abundance scoring | Not Da or ppm; values such as `0.005` and `20` behave very differently and require validation |
| `MassToleranceType` | `Da` or `Ppm` | Unit of `Ms1Tolerance` | ppm produces an m/z-dependent Da window; Da is fixed |
| `CommonRange` | Boolean | Uses the most common and strictest elemental-ratio range | Enable only one of Common/Extended/Extreme |
| `ExtendedRange` | Boolean | Uses a wider elemental-ratio range | May recover atypical formulas but increases candidates |
| `ExtremeRange` | Boolean | Uses the widest elemental-ratio range | Reserve for unusual chemical spaces or exploratory work |
| `ElementProbabilityCheck` | Boolean | Applies Seven Golden Rules-type element-count/probability heuristics | Normally `True`; disabling expands candidate space |
| `Ocheck` | Boolean | Allows oxygen-containing candidates | `False` excludes all oxygen-containing formulas |
| `Ncheck` | Boolean | Allows nitrogen-containing candidates | Set according to expected chemistry |
| `Pcheck` | Boolean | Allows phosphorus-containing candidates | Often required for phosphates and phospholipids |
| `Scheck` | Boolean | Allows sulfur-containing candidates | Often required for sulfur metabolites |
| `Fcheck` | Boolean | Allows fluorine-containing candidates | Usually relevant to drugs, environmental compounds, or fluorinated analytes |
| `ClCheck` | Boolean | Allows chlorine-containing candidates | Consider chlorine isotope patterns |
| `BrCheck` | Boolean | Allows bromine-containing candidates | Consider bromine isotope patterns |
| `Icheck` | Boolean | Allows iodine-containing candidates | Enable only when iodine is expected |
| `SiCheck` | Boolean | Allows silicon-containing candidates | Commonly relevant to GC derivatization or organosilicon compounds |
| `IsTmsMeoxDerivative` | Boolean | Treats the query as a TMS/MeOX derivative for EI/GC-MS | `False` for the supplied LC-ESI-MS/MS methods |
| `MinimumTmsCount` | Integer | Minimum TMS groups required in derivative candidates | Used only when `IsTmsMeoxDerivative=True` |
| `MinimumMeoxCount` | Integer | Minimum MeOX groups required in derivative candidates | Used only when `IsTmsMeoxDerivative=True` |
| `FormulaMaximumReportNumber` | Positive integer | Maximum formula candidates retained/reported per query | Higher values reduce truncation risk but increase downstream structure search |

The professor-supplied source guide documented large differences among earlier example methods, including `Ms1Tolerance=0.15 Da` versus `0.13 Da` and `IsotopicAbundanceTolerance=0.005` versus `20`. The current public generic methods use `Ms1Tolerance=0.1 Da`, but the same warning remains: do not adopt a value merely because of a filename. Validate it against the actual instrument and known samples.

## 3. Structure finder parameters

Structure finder uses formula candidates, selected structure databases, and in-silico fragmentation to explain and rank experimental MS/MS fragments.

| Setting | Type/unit | Meaning | Guidance |
|---|---|---|---|
| `TreeDepth` | Non-negative integer | Maximum in-silico fragmentation-tree depth | Deeper searches are generally slower; supplied methods use `2` |
| `Ms2Tolerance` | Number; method mass-tolerance scale | Mass tolerance for matching experimental fragments to reference/predicted fragments | Does not control the initial OMIXIS feature-to-MSP match |
| `RelativeAbundanceCutOff` | Number; relative intensity % | Uses product ions above this fraction of the base peak | Raising it removes weak peaks/noise but may remove diagnostic low-intensity fragments |
| `StructureMaximumReportNumber` | Positive integer | Maximum structure candidates reported per query | Supplied generic methods use `5`; this is not the number of rows in the final feature-level TSV |
| `IsUseEiFragmentDB` | Boolean | Uses the internal EI fragment-ion library | Consider for GC-EI; supplied LC-ESI-MS/MS methods use `False` |

### 3.1 `Ms1Tolerance` versus `Ms2Tolerance`

| Setting | Compared objects | Additional OMIXIS role | Typical effect when too wide |
|---|---|---|---|
| `Ms1Tolerance` | Precursor ion versus accurate formula mass | Also widens feature-to-MSP precursor matching | More MSP matches and more formula candidates |
| `Ms2Tolerance` | Experimental fragments versus reference/predicted fragments | None | More fragments appear matched and structural discrimination decreases |

## 4. Data-source parameters

These settings determine where structure candidates are obtained. MS-FINDER distinguishes bundled local databases, optional MINEs/PubChem online retrieval, and user-defined structure databases.

### 4.1 MINEs and PubChem online-retrieval strategies

| Setting | Meaning | Guidance |
|---|---|---|
| `MinesNeverUse` | Do not use MINEs | Mutually exclusive with the other two `Mines*` options |
| `MinesOnlyUseForNecessary` | Use MINEs only when selected local databases have no candidate | Balances speed and candidate supplementation |
| `MinesAllways` | Always add MINEs candidates | May increase candidates and run time; retain the original spelling |
| `PubChemNeverUse` | Do not perform additional online PubChem retrieval | Separate from the local `PubChem=True/False` switch |
| `PubChemOnlyUseForNecessary` | Retrieve from PubChem only when local databases have no candidate | Requires network/service availability and may affect long-term reproducibility |
| `PubChemAllways` | Always retrieve additional PubChem candidates | Broadest coverage and potentially longest run time; retain the original spelling |

Select exactly one strategy in each three-option group. For reproducible local processing, the supplied generic methods use:

```text
MinesNeverUse=True
MinesOnlyUseForNecessary=False
MinesAllways=False
PubChemNeverUse=True
PubChemOnlyUseForNecessary=False
PubChemAllways=False
```

`PubChem=True` in the local-database section may still use PubChem-related resources bundled with the installed MS-FINDER version; it does not enable online retrieval by itself.

### 4.2 Bundled local structure databases

All switches are Boolean. `True` includes the source in candidate retrieval. Enabling more sources can improve coverage but also increases candidates, memory use, run time, and structural ambiguity. Actual contents depend on the resources distributed with MS-FINDER v3.61.

| Setting | Database/source | Main scope |
|---|---|---|
| `HMDB` | Human Metabolome Database | Human metabolites |
| `YMDB` | Yeast Metabolome Database | Yeast metabolites |
| `PubChem` | Bundled PubChem-related structures | Broad chemical structures; separate from online strategy |
| `SMPDB` | Small Molecule Pathway Database | Small molecules and pathways |
| `UNPD` | Universal Natural Products Database | Natural products |
| `ChEBI` | Chemical Entities of Biological Interest | Biologically relevant chemical entities |
| `PlantCyc` | PlantCyc | Plant pathways and metabolites |
| `KNApSAcK` | KNApSAcK | Species-metabolite and natural-product information |
| `BMDB` | MS-FINDER bundled BMDB source | Content depends on the v3.61 resource package |
| `FooDB` | FooDB | Food constituents and food compounds |
| `ECMDB` | E. coli Metabolome Database | E. coli metabolites |
| `DrugBank` | DrugBank | Drugs and related structures |
| `T3DB` | Toxin and Toxin Target Database | Toxicants and toxin-related compounds |
| `STOFF` | STOFF-IDENT | Natural products and specialized metabolites |
| `NANPDB` | Northern African Natural Products Database | Northern African natural products |

Broad exploratory work may enable all bundled local databases. Focused studies should select sources relevant to the sample type and document the choice.

### 4.3 User-defined structure database

| Setting | Type | Meaning | Guidance |
|---|---|---|---|
| `IsUserDefinedDB` | Boolean | Includes a user-defined structure database | A valid MS-FINDER-compatible file is required when `True` |
| `UserDefinedDbFilePath` | File path | Location of the custom structure database | Leave empty when disabled; avoid sharing machine-specific absolute paths |

## 5. Spectral-database search parameters

MS-FINDER can perform spectral-library search and formula/structure in-silico search in the same run. When both are enabled, spectral-library results receive priority in structure ranking according to the MS-FINDER documentation.

| Setting | Type | Meaning | Guidance |
|---|---|---|---|
| `IsRunSpectralDbSearch` | Boolean | Runs experimental/predicted spectral-library search | If `False`, the spectral-library selection settings produce no spectral-search result |
| `IsRunInSilicoFragmenterSearch` | Boolean | Runs formula prediction and in-silico structure interpretation | May be enabled together with spectral search |
| `IsPrecursorOrientedSearch` | Boolean | Prefilters spectral records by precursor m/z and `Ms1Tolerance` | Normally `True` for LC-MS/MS; MS-FINDER guidance recommends disabling for EI search |
| `IsUseInternalExperimentalSpectralDb` | Boolean | Uses the experimental EI/MSMS library bundled in MS-FINDER Resources | Can improve matches to reference spectra |
| `IsUseInSilicoSpectralDbForLipids` | Boolean | Uses the bundled lipid in-silico library (LipidBlast in the official tutorial) | Consider for lipidomics; usually `False` for non-lipid studies |
| `IsUseUserDefinedSpectralDb` | Boolean | Searches a user-provided NIST MSP library | Requires a valid path when `True` |
| `UserDefinedSpectralDbFilePath` | File path | Location of the custom MSP library | Positive and negative ion libraries are usually separate |
| `SolventType` | Enumerated text | Solvent/adduct condition for spectral search | Must match the mobile-phase and ion/adduct conditions; supplied methods use `CH3COONH4` |
| `MassRangeMin` | Number; m/z | Minimum spectral-search mass range | Supplied methods use `50` |
| `MassRangeMax` | Number; m/z | Maximum spectral-search mass range | Supplied methods use `1250`; ensure the actual acquisition range is covered |

Example using a user library:

```text
IsRunSpectralDbSearch=True
IsUseInternalExperimentalSpectralDb=True
IsUseUserDefinedSpectralDb=True
UserDefinedSpectralDbFilePath=D:\libraries\my_positive_library.msp
```

Without a user library:

```text
IsUseUserDefinedSpectralDb=False
UserDefinedSpectralDbFilePath=
```

Never leave `IsUseUserDefinedSpectralDb=True` with an empty, missing, unreadable, or wrong-polarity MSP path. Do not distribute a method containing an absolute path that exists only on one computer.

## 6. Batch-job parameters

These settings control the MS-FINDER console batch stages. OMIXIS processes accumulated MSP files in batch mode. Keep the validated combination unless intentionally debugging an individual stage or performing a performance experiment.

| Setting | Type | Meaning | Guidance |
|---|---|---|---|
| `AllProcess` | Boolean | Master switch for the complete batch process | Supplied methods use `True` |
| `FormulaFinder` | Boolean | Runs formula search in batch mode | Normally required before structure search |
| `StructureFinder` | Boolean | Runs structure search/ranking in batch mode | May be disabled when only formula output is required |
| `TryTopNMolecularFormulaSearch` | Positive integer | Sends the top N formulas into structure search | Higher values can improve recall but substantially increase run time; current generic methods use `3` |

## 7. Practical parameter-adjustment order

1. Use well-calibrated known compounds or QC samples to estimate the observed MS1 and MS2 mass-error distributions, then choose `Ms1Tolerance`, `MassToleranceType`, and `Ms2Tolerance`.
2. Measure chromatographic RT drift in the same data. `RetentionTimeTolerance` must cover plausible drift without matching many distinct co-eluting peaks at the same m/z.
3. Confirm that `RelativeAbundanceCutOff` does not remove important low-intensity diagnostic ions.
4. Select elements, databases, and spectral libraries for the research domain. Start focused and broaden gradually so changes remain interpretable.
5. Adjust `FormulaMaximumReportNumber`, `StructureMaximumReportNumber`, and `TryTopNMolecularFormulaSearch` last to balance recall and run time.

### 7.1 Troubleshooting directions

| Observation | Check first | Possible adjustment |
|---|---|---|
| Almost no MSP matches a feature | m/z unit, feature RT, RT unit, ion mode | Confirm `MassToleranceType`; verify RT is in minutes; widen only from measured error evidence |
| Too many MSP spectra match each feature | `Ms1Tolerance`, `RetentionTimeTolerance` | Narrow tolerances; inspect duplicated/co-eluting precursors |
| Too many formula candidates | `Ms1Tolerance`, range rules, element switches | Use a justified ppm/Da window, prefer `CommonRange`, disable impossible elements |
| Structure search is very slow | Number of databases, `TreeDepth`, `TryTopN`, candidate limits | Reduce databases and candidate limits; validate with `TreeDepth=2` first |
| Weak diagnostic ions are ignored | `RelativeAbundanceCutOff` | Lower the threshold while monitoring noise and false positives |
| Custom MSP library has no matches | Path, ion mode, precursor search, MSP format | Verify file access, polarity, NIST MSP formatting, precursor/adduct metadata |

## 8. Pre-run checklist

- [ ] The method file matches the positive or negative polarity of the selected mzML files.
- [ ] `Ms1Tolerance` is positive and `MassToleranceType` is explicitly `Da` or `Ppm`.
- [ ] `RetentionTimeTolerance` and feature-table RT are both expressed in minutes.
- [ ] Exactly one of `CommonRange`, `ExtendedRange`, and `ExtremeRange` is enabled.
- [ ] Exactly one strategy is enabled in each `Mines*` and `PubChem*` group.
- [ ] Every enabled custom structure/spectral database path exists and matches the intended ion mode.
- [ ] `SolventType`, `MassRangeMin`, and `MassRangeMax` match the analytical conditions and acquisition range.
- [ ] A small QC subset has been tested and the Processing Log shows plausible MSP-generation and tolerance-match counts.
- [ ] `MSFINDER_method_effective.txt` contains all intended MS-FINDER settings and no OMIXIS-only RT setting.
- [ ] `Feature_MSP_matches.tsv` and the final `Untargeted_Compound_ID_Positive.tsv` or `Untargeted_Compound_ID_Negative.tsv` contain plausible feature matches and top-scoring annotations.

## 9. Recommended method-header comments

For reproducibility, place at least the ion mode, instrument/method, validation date, editor, tolerance evidence, database policy, and custom spectral-library version at the beginning of each method file.

```text
# Ion mode: Positive
# Instrument/method: [describe]
# Validated on: YYYY-MM-DD
# Edited by: [name or role]
# MS1/MS2 tolerance basis: [describe]
# RT tolerance basis: [describe]
# Database/spectral-library version: [describe]
RetentionTimeTolerance=0.02
```

## Official references

- [MS-FINDER tutorial: parameter settings and data sources](https://systemsomicslab.github.io/mtbinfo.github.io/MS-FINDER/tutorial)
- [MS-FINDER console application](https://systemsomicslab.github.io/compms/msfinder/consoleapp.html)
- [CompMS and MS-FINDER information](https://systemsomicslab.github.io/compms/index.html)


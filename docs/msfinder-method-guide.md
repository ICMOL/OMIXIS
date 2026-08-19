# MS-FINDER method guide

An MS-FINDER method is a plain-text `key=value` file. It controls feature-to-MSP matching, formula prediction, structure search, database selection, and spectral-library search.

## Core matching settings

```text
Ms1Tolerance=0.1
MassToleranceType=Da
Ms2Tolerance=0.1
RetentionTimeTolerance=0.02
```

- `Ms1Tolerance` and `MassToleranceType` control both feature-to-MSP precursor matching and molecular-formula search.
- `Ms2Tolerance` controls fragment matching during spectral and structure search.
- `RetentionTimeTolerance` is in minutes and controls feature-to-MSP RT matching in OMIXIS.

Choose tolerances from known compounds or QC data acquired on the same instrument. The supplied generic methods use `0.1 Da` as a broad starting template; they are not universal instrument-calibration files. High-accuracy instruments may require a narrower tolerance, while lower-accuracy data may require this wider range.

## Structure databases

The bundled local structure databases can be selected individually using `True` or `False`:

```text
HMDB=True
YMDB=True
PubChem=True
SMPDB=True
UNPD=True
ChEBI=True
PlantCyc=True
KNApSAcK=True
BMDB=True
FooDB=True
ECMDB=True
DrugBank=True
T3DB=True
STOFF=True
NANPDB=True
```

Enabling more databases improves coverage but increases candidate number, run time, and structural ambiguity. For broad exploratory work, all bundled local databases may be enabled. For focused studies, enable only databases relevant to the sample type.

The three `Mines*` settings are mutually exclusive, as are the three `PubChem*` online-retrieval settings. For reproducible local processing, use:

```text
MinesNeverUse=True
MinesOnlyUseForNecessary=False
MinesAllways=False
PubChemNeverUse=True
PubChemOnlyUseForNecessary=False
PubChemAllways=False
```

`PubChem=True` in the local-database section is separate from the `PubChem*` online-retrieval strategy.

## Experimental spectral libraries

MS-FINDER can search its bundled experimental spectral library and an optional user-provided MSP library:

```text
IsRunSpectralDbSearch=True
IsUseInternalExperimentalSpectralDb=True
IsUseUserDefinedSpectralDb=True
UserDefinedSpectralDbFilePath=D:\\libraries\\my_positive_library.msp
```

Use separate positive- and negative-ion MSP libraries. If no user library is available, use:

```text
IsUseUserDefinedSpectralDb=False
UserDefinedSpectralDbFilePath=
```

Never leave `IsUseUserDefinedSpectralDb=True` with an empty or invalid path.

## Positive and negative methods

Use separate positive- and negative-ion method files, even when their generic starting values are identical. OMIXIS validates polarity from mzML metadata; separate files prevent accidental use of the wrong custom MSP library and provide clear provenance when polarity-specific parameters are later adjusted.

## In-silico search

```text
IsRunInSilicoFragmenterSearch=True
TreeDepth=2
FormulaMaximumReportNumber=10
StructureMaximumReportNumber=5
TryTopNMolecularFormulaSearch=3
```

Increasing candidate limits or tree depth can improve recall but increases run time. Start with a small QC subset when validating a new method.

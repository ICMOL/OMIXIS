# Standard-list format and MSI evidence

## Standard-list format

The authentic-standard reference file is optional. It must be tab-separated and contain these exact columns:

```text
m/z	Retention Time	Chemical Formula
175.11719	0.504	C6H14N4O2
176.10674	0.567	C6H13N3O3
```

- `m/z`: expected precursor m/z
- `Retention Time`: expected RT in minutes
- `Chemical Formula`: molecular formula for traceable standard-list evidence

Use a standard list generated under matching chromatography, ion mode, and analytical conditions. Do not use one column or one ion mode's RT values for a different condition.

## MSI evidence reported by OMIXIS

OMIXIS uses a simplified evidence classification:

| Result | Rule |
|---|---|
| Level 1 | A feature has an MS-FINDER compound annotation and matches an authentic-standard-list entry by m/z and RT. |
| Level 2 | A feature has either an MS-FINDER compound annotation or an authentic-standard-list m/z/RT match, but not both. |
| Unknown | Neither source of evidence is present. |

The standard-list m/z tolerance follows the MS-Picker `mzwidth` setting. The RT comparison uses the standard-list matching tolerance implemented by OMIXIS.

## Interpretation

Level 1 is evidence supported by both MS/MS annotation and an authentic-standard-list m/z/RT match. It does not replace manual confirmation of MS/MS-spectrum quality, adduct assignment, or matched-standard analysis when a definitive identification claim is required.

Level 2 is a computational or reference-list-supported annotation and should be reported as putative.


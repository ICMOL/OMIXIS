<h1>
  <img src="./logo.png" width="42" align="center">
  OMIXIS
</h1>

**OMIXIS** is a graphical platform for LC–MS metabolomics data processing.  
It integrates peak detection (MS-Picker), peak quality assessment (MS-Point), and feature alignment (MS-Aligner) into a structured local workflow, helping users generate feature tables for downstream statistical analysis and machine learning.

## Overview

LC–MS metabolomics workflows often involve multiple preprocessing tools, manual parameter tuning, and repeated transfer of large raw datasets between different environments. These fragmented processes can slow down analysis and create unnecessary technical barriers.

OMIXIS streamlines metabolomics data preparation through an integrated local workflow. With an intuitive graphical interface, users can process raw LC–MS data, evaluate peak quality, perform feature alignment, and generate structured feature tables ready for downstream analysis.

By reducing repetitive manual steps and eliminating unnecessary data transfers, OMIXIS delivers a more efficient, secure, and user-friendly preprocessing experience — making advanced metabolomics analysis accessible to researchers without extensive programming expertise.

## Key Features

- **Graphical workflow**  
  Provides an intuitive interface for operating LC–MS metabolomics preprocessing modules.

- **Local execution**  
  Runs directly on a personal computer, reducing upload time and potential data exposure risks.

- **Integrated quantitation pipeline**  
  Combines peak detection, peak quality assessment, and feature alignment in a structured workflow.

- **Peak quality assessment**  
  Uses multiple peak quality metrics to support feature reliability evaluation.

- **Structured output**  
  Generates feature tables for downstream statistical analysis, machine learning, and biomarker discovery.

## Workflow Overview
<img src="./workflow.png" width="100%" align="center">

The OMIXIS workflow consists of three main quantitation modules:

1. **MS-Picker**  (https://github.com/ICMOL/MS-Picker)
   
   Detects metabolite peaks from LC–MS data and extracts feature-level information.

2. **MS-Point**  (https://github.com/ICMOL/MS-Point)
   
   Evaluates detected peaks using peak quality metrics and generates a quality score for each feature.

3. **MS-Aligner**  (https://github.com/ICMOL/MS-Aligner)
   
   Aligns detected features across samples and produces a structured feature table.


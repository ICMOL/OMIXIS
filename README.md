<h1>
  <img src="./logo.png" width="42" align="center">
  OMIXIS
</h1>

**OMIXIS** is a graphical platform for LC–MS metabolomics data processing.  
It integrates peak detection (MS-Picker), peak quality assessment (MS-Point), and feature alignment (MS-Aligner) into a structured local workflow, helping users generate feature tables for downstream statistical analysis and machine learning.

## Overview

LC–MS metabolomics analysis often requires users to operate multiple preprocessing tools, adjust parameters manually, and transfer large raw datasets between different environments. These steps can increase the technical burden for users and make the workflow less efficient.

OMIXIS provides an integrated local workflow for metabolomics data preparation. Through a graphical user interface, users can process raw LC–MS data, evaluate peak quality, align detected features, and export structured feature tables for downstream analysis.

The platform is designed to reduce repetitive manual operations, avoid unnecessary upload of large raw datasets, and make metabolomics preprocessing more accessible to users without extensive programming experience.

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

1. **MS-Picker**  
   Detects metabolite peaks from LC–MS data and extracts feature-level information.

2. **MS-Point**  
   Evaluates detected peaks using peak quality metrics and generates a quality score for each feature.

3. **MS-Aligner**  
   Aligns detected features across samples and produces a structured feature table.


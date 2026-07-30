# Developmental_state

This repository contains the data and code used to produce the figures and documentary analysis for the manuscript on green hydrogen governance and state capacity in Chilean Patagonia.

## Repository structure

```
Developmental_state/
├── data/
│   └── data_documental.xlsx      # Documentary coding dataset
├── scripts/
│   └── plots.R                   # Reproducible R script for figures
├── outputs/                      # Generated figures
└── Microcrisis_H2V.Rproj         # RStudio project
```

## Contents

### Data
`data/data_documental.xlsx` contains the documentary coding matrix used to analyse institutional responsibilities and thematic emphasis across Chile's Green Hydrogen Strategy.

### Scripts
`scripts/plots.R` reproduces:

1. **Figure 1.** Location map of the main green hydrogen projects in Magallanes.
2. **Figure 2.** Institutional heatmap summarising documentary mentions by institution and analytical theme.

The script downloads the official Chilean regional shapefile directly from the Biblioteca del Congreso Nacional and uses Natural Earth for the national inset map. It expects `data_documental.xlsx` to be located in the `data/` folder. 

## Software requirements

- R (≥4.3 recommended)
- RStudio (optional)
- Packages used in `plots.R` include:
  - sf
  - ggplot2
  - dplyr
  - tidyverse
  - readxl
  - ggrepel
  - ggspatial
  - cowplot
  - rnaturalearth
  - viridis
  - forcats
  - skimr
  - patchwork
  - and other packages loaded through `pacman`.

## Reproducibility

Open `Microcrisis_H2V.Rproj`, place the documentary dataset in the `data/` directory, and run `scripts/plots.R` from beginning to end. The figures will be reproduced from the raw coding matrix.

## Outputs

The repository generates publication-ready figures illustrating:

- Spatial distribution of major green hydrogen projects in Magallanes.
- Institutional distribution of documentary references across analytical themes.

# Codebook

## Dataset

**File:** `data_documental.xlsx`

**Unit of analysis:** Institution × documentary coding record.

## Variables

| Variable | Readable name | Type | Allowed values | Definition |
|----------|---------------|------|----------------|------------|
| Institution | Institution | Text | Official institution names | Public institution or organisation referenced in the documentary analysis. |
| Count | Minimum coding threshold | Integer | ≥0 | Total number of coded references before filtering. The plotting script retains observations where Count ≥ 3. |
| A1–A18 | Analytical themes | Integer | 0 or positive integers | Number of documentary mentions assigned to each analytical theme for a given institution. Higher values indicate more frequent references. |

## Institutions

Examples include:

- Ministry of Energy
- Ministry of Economy
- CORFO
- Regional Governments
- Ministry of Environment
- Environmental Evaluation Service
- National Petroleum Company (ENAP)
- Municipalities

## Theme variables

Variables A1–A18 represent the analytical coding framework developed for the documentary analysis. Each variable stores the number of mentions coded to that theme for each institution.

## Data processing

The R script:

1. Imports the Excel file.
2. Filters observations where `Count >= 3`.
3. Reshapes the data from wide to long format.
4. Aggregates mentions by institution and theme.
5. Produces descriptive summaries and a heatmap.

## Missing values

- 0 = no coded mentions.
- Blank cells should be treated as missing and investigated before analysis.

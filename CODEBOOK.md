# Codebook

This codebook follows Open Science Framework recommendations for documenting datasets by describing variable names, definitions, and allowed values. 

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

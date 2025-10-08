# TEVA Healthcare Data Analysis Project

This repository contains Jupyter notebooks for healthcare data analysis, focusing on synthetic NAMCS (National Ambulatory Medical Care Survey) data generation and real NHMACS/NMACS schizophrenia survey data processing.

## Project Overview

The project consists of three main notebooks that work together to create, process, and analyze healthcare survey data:

1. **data_prep_commented.ipynb** - Synthetic NAMCS data generation
2. **parse_excel_commented.ipynb** - Real survey data extraction and processing
3. **compute_catalog_commented.ipynb** - Data catalog scanning and PII detection

---

## 📊 Notebooks Description

### 1. data_prep_commented.ipynb - Synthetic NAMCS Data Generation

**Purpose**: Generates realistic synthetic healthcare visit data following the NAMCS schema using AI.

**Key Features**:
- Defines comprehensive NAMCS schema with 29+ fields covering patient demographics, diagnoses, procedures, medications, and vital signs
- Exports schema to JSON format for AI consumption
- Uses Databricks Claude Sonnet 4 to generate 100 realistic patient visit records per month for 2025
- Parses and flattens AI-generated JSON data into structured Delta tables
- Supports both automated AI-based DDL generation and manual schema definition

**Main Sections**:
1. **Schema Definition** - Creates detailed field definitions with descriptions, possible values, and examples
2. **Data Generation** - AI-powered synthetic data creation with prompts for diversity and randomization
3. **Data Processing** - JSON parsing, array explosion, and flattening into columnar format
4. **Table Management** - Delta table creation, deletion, and verification

**Output**: 
- `mc.teva.namcs_synthetic_json` - Raw JSON format
- `mc.teva.namcs_synthetic_flat` - Flattened columnar format

**Use Cases**:
- Testing and development with realistic healthcare data
- Training machine learning models without PHI concerns
- Demonstration and proof-of-concept implementations
- Research on healthcare patterns without privacy restrictions

---

### 2. parse_excel_commented.ipynb - Real Survey Data Processing

**Purpose**: Extracts, cleans, and documents real NHMACS/NMACS schizophrenia survey data from Excel files.

**Key Features**:
- Automatic table detection from complex Excel sheets with multiple tables
- Intelligent column name normalization and type inference
- Creates 8 clean Delta tables with national and regional schizophrenia statistics
- AI-generated comprehensive documentation for all tables and columns
- Applies metadata as SQL COMMENT statements for discoverability

**Main Sections**:
1. **Excel Parsing** - Custom functions to detect and extract multiple tables from sheets
2. **Data Cleaning** - Column normalization, empty column removal, numeric type detection
3. **Table Creation** - Saves extracted data as Delta tables with descriptive names
4. **Documentation Generation** - Uses AI to explain acronyms and generate detailed descriptions
5. **Metadata Application** - Applies documentation to actual tables via SQL COMMENT

**Output Tables** (mc.teva catalog):
- `NHMACS_national_schiz_avg_annual_freq_race_ethnic_unimputed`
- `NMACS_national_schiz_avg_annual_freq_race_ethnic_unimputed`
- `NHMACS_national_schiz_avg_annual_freq_race_ethnic_imputed`
- `NMACS_national_schiz_avg_annual_freq_race_ethnic_imputed`
- `NHMACS_regional_schiz_avg_annual_freq_race_ethnic_unimputed`
- `NMACS_regional_schiz_avg_annual_freq_race_ethnic_unimputed`
- `NHMACS_regional_schiz_avg_annual_freq_race_ethnic_imputed`
- `NMACS_regional_schiz_avg_annual_freq_race_ethnic_imputed`

**Data Characteristics**:
- **Imputed vs Unimputed**: Shows data with/without statistical estimation for missing race/ethnicity
- **National vs Regional**: Provides both overall U.S. statistics and geographic breakdowns
- **NHMACS vs NMACS**: National Hospital Medical Care Survey vs National Medical Care Survey

**Use Cases**:
- Analyzing schizophrenia treatment patterns across demographics
- Studying healthcare disparities by race/ethnicity and region
- Statistical research on mental health service utilization
- Policy analysis and resource allocation planning

---

### 3. compute_catalog_commented.ipynb - Data Catalog Security Scan

**Purpose**: Scans Databricks catalogs to identify columns that may contain sensitive or PII data using AI analysis.

**Key Features**:
- Scans first 10 catalogs extracting complete metadata hierarchy
- Defines critical words list for PII and security-sensitive column detection
- Three AI-based analysis approaches:
  - Decimal scoring (0-1) for sensitivity assessment
  - Boolean matching with specific critical word identification
  - Direct PII detection
- Excludes system objects (starting with underscore)

**Main Sections**:
1. **Critical Words Definition** - List of sensitive column names (grantor, grantee, PII-related terms)
2. **Catalog Scanning** - Iterates through catalogs > databases > tables > columns
3. **Column Inventory** - Creates complete catalog of all columns across the workspace
4. **AI Analysis** - Multiple approaches to detect sensitive data patterns
   - Score-based assessment for risk ranking
   - Pattern matching for specific term detection
   - Binary PII classification

**Output**:
- `mc.teva.columns` - Complete column inventory
- Analysis results showing which columns may contain sensitive data

**Use Cases**:
- Data governance and compliance auditing
- PII discovery across large data estates
- Security risk assessment
- Automated data classification
- Privacy regulation compliance (GDPR, HIPAA, etc.)

---

## 🔄 Workflow Integration

### Suggested Execution Order:

1. **Start with parse_excel_commented.ipynb** if you have real survey data to process
   - Extracts and cleans your source data
   - Creates foundational tables with proper documentation

2. **Run data_prep_commented.ipynb** to augment with synthetic data
   - Generates additional test/development data
   - Follows similar schema patterns for consistency

3. **Execute compute_catalog_commented.ipynb** for security audit
   - Scans all created tables
   - Identifies sensitive columns requiring protection
   - Helps establish data governance policies

### Data Flow:
```
Excel Files → parse_excel → Clean Delta Tables → compute_catalog → Security Report
                                                 ↓
                              data_prep → Synthetic Delta Tables → compute_catalog → Security Report
```

---

## 🛠️ Technical Requirements

### Environment:
- Databricks workspace with Unity Catalog
- Databricks Runtime with Spark
- Access to Databricks AI (Claude Sonnet 4)

### Python Libraries:
- `pandas` - Data manipulation
- `numpy` - Numerical operations
- `openpyxl` - Excel file reading
- `pyspark` - Distributed data processing
- `databricks-sdk[openai]` - AI integration

### Permissions Required:
- Read/write access to `mc.teva` catalog
- Read access to `/Volumes/mc/teva/files/`
- Permission to use Databricks AI query function
- Catalog scanning permissions (for compute_catalog)

---

## 📁 Key Files and Artifacts

### Input Files:
- `YW_NHMACS_NMACS_MEPS_SCHIZ_CLEAN.xlsx` - Source Excel with survey data
- `namcs_schema3.json` - NAMCS field definitions
- `critical_words.json` - PII detection keywords

### Output Tables (mc.teva catalog):
- `namcs_synthetic_json` - Raw synthetic data (JSON)
- `namcs_synthetic_flat` - Flattened synthetic data
- `dates_2025` - Reference date table
- `NHMACS_*` / `NMACS_*` - Real survey data tables (8 tables)
- `table_names` - Reference table for iteration
- `columns` - Complete column inventory

---

## 🎯 Common Use Cases

### Healthcare Research:
- Study patient demographics and visit patterns
- Analyze medication prescribing trends
- Compare regional healthcare utilization
- Research mental health service delivery

### Data Governance:
- Discover PII across healthcare databases
- Audit data sensitivity levels
- Implement column-level security
- Comply with HIPAA regulations

### Development & Testing:
- Generate realistic test data
- Build ML models without PHI exposure
- Create demos and proofs of concept
- Train teams on healthcare data analysis

---

## 📝 Best Practices

1. **Data Privacy**: Always use synthetic data for development/testing; handle real survey data according to privacy regulations
2. **Documentation**: Each notebook includes detailed markdown cells explaining every step
3. **Reproducibility**: Hardcoded schemas and parameters ensure consistent results
4. **Modularity**: Notebooks can be run independently or as part of integrated workflow
5. **AI Usage**: Prompts include instructions for diversity to avoid repetitive generation

---

## 🔍 Acronyms & Terminology

- **NAMCS**: National Ambulatory Medical Care Survey
- **NHMACS**: National Hospital Medical Care Survey
- **NMACS**: National Medical Care Survey
- **PII**: Personally Identifiable Information
- **PHI**: Protected Health Information
- **ICD-10**: International Classification of Diseases, 10th Revision
- **CPT**: Current Procedural Terminology
- **DDL**: Data Definition Language
- **Delta**: Databricks Delta Lake format
- **Imputed**: Missing values statistically estimated
- **Unimputed**: Original data without statistical filling

---

## 📧 Support & Contributing

For questions or issues with these notebooks, please refer to the inline documentation within each notebook. Each cell includes detailed comments explaining its purpose and functionality.

---

## 📄 License

This project is designed for healthcare data analysis and research purposes. Ensure compliance with all applicable healthcare data regulations (HIPAA, etc.) when working with real patient data.


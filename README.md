# Overview
# 
This project implements a **configuration-driven healthcare eligibility data pipeline** designed to ingest eligibility files from multiple partner systems, standardize them into a **single unified dataset**, and route invalid records into a **rejects dataset.**

The solution focuses on clarity, extensibility, and data quality, reflecting how partner onboarding and validation would be handled in a real-world data engineering environment.

## **1. Project Structure**
## 

eligibility-pipeline/

└──

├── notebooks/

│   └── 01_config_loader.ipynb         # partner configurations and loading

│   └── 02_io_readers_writers.ipynb                 # Spark readers and Output writers

│   └── transformations_and_validations.ipynb          # Standardization logic AND Core validation rules

│   └── pipeline_runner.ipynb          # Orchestrates and runs the pipeline

├── Outputs/

│   └── TwoSourcePartner_tables.ipynb       # ACME + Better Care results

│   └── ThirdPartner_Tables.ipynb       # Extensibility & validation demo

│

├── config/

│   └── partners.json                  # Partner configuration file

├── config/

│   └── acme.txt
    └── better_care.csv
    └── unitedhealthsystem.csv


## **2. What Was Built**

- A scalable pipeline that ingests partner eligibility files with **different formats and schemas**
- Centralized transformations and validations applied consistently across partners
- A unified output dataset for valid records
- A rejects dataset for invalid or malformed records
- A configuration-driven design that allows new partners to be added **without changing pipeline code**

## **3. High-Level Design**

The pipeline is organized into modular components:

- **Spark Readers**

  Ingest partner files based on configuration (file path, delimiter, headers).

- **Core Transformations**

  Normalize partner-specific schemas into a common eligibility model, including date parsing, name standardization, and phone normalization.

- **Core Validations**
  
  Apply centralized data quality rules to all partners.

- **Spark Writers**

  Persist unified and reject datasets for downstream consumption.

- **Pipeline Runner**

  Orchestrates the end-to-end flow for all configured partners.

Each component is implemented once and reused across all partners.


##   **4. Partner Configuration** (partners.json)

All partner-specific logic is defined in a single configuration file, including:

- File path
- Delimiter
- Header presence
- Column mappings
- Partner code

Example (simplified):

`{

  "partner_key": "acme_health",

  "partner_code": "ACME",

  "file_path": ".../acme.txt",

  "delimiter": "|",

  "has_header": true,

  "column_mapping": {

    "MBI": "external_id",

    "FNAME": "first_name",

    "LNAME": "last_name",

    "DOB": "dob",

    "EMAIL": "email",

    "PHONE": "phone"

  }

}`

**_Adding a new partner requires only adding a new entry to this file, no pipeline code changes._**

## **5. Standardized Output Schema**

All valid records are standardized into the following schema:

_Field =_
Description

_external_id_	=
Partner member identifier

_first_name_	=
Standardized first name

_last_name_	=
Standardized last name

_dob_ =
ISO-formatted date (YYYY-MM-DD)

_email_	=
Lowercased email

_phone_	=
Normalized phone number

_partner_code_	=
Source partner identifier

_**This ensures consistent downstream analysis regardless of source format.**_

## **6. Validation & Error Handling (Bonus Points)**

The pipeline includes centralized validation and error-handling logic:

**Validations Implemented**

Missing external_id > routed to rejects

Invalid date formats > flagged and routed to rejects

Invalid phone numbers > flagged and routed to rejects

**Error Handling Approach**

Malformed or invalid rows do not fail the pipeline
Each rejected record includes a clear reject_reason
The pipeline continues processing valid records without failure

## **7.Third Partner Extensibility Demonstration**

To demonstrate extensibility and validation handling, a third partner with:

- A different delimiter
- Mixed date formats 
- Missing identifiers
- Invalid phone values

was added only via configuration.

**No changes were made to the core processing logic:**

- Readers
- Transformations
- Validations
- Writers

The resulting unified and reject outputs clearly demonstrate the pipeline’s ability to handle new partners and malformed data gracefully.

_The Outputs folder highlights the differences in unified and rejected records between the two source partners and the third partner._


## **8.Design Decisions & Logic**

- Single configuration file for all partners to reflect real-world ingestion framework
- Enabled flag support to control partner execution without removing config
- Notebook modularization for clarity and reviewer readability
- Centralized validation logic for consistency across partners and Graceful handling of malformed data
- Databricks-native approach aligned with AgeBold’s tooling

## **9.Summary**

This project demonstrates a clean, extensible approach to healthcare eligibility ingestion, emphasizing:

- Clean, configuration-driven ingestion
- Scalable partner onboarding
- Schema standardization
- Structured validation and error handling
- Clear, maintainable pipeline structure



### Overall, this project demonstrates my approach to building practical data
### pipelines that are scalable, easy to extend, and resilient to data quality
### issues commonly found in real-world healthcare data.







## What Is a Computable Phenotype?

- A **computable phenotype** is a *reproducible, algorithmic definition* of a clinical condition or trait that can be implemented on electronic health data.
- It consists of **structured criteria**, such as:
  - Diagnosis codes (e.g., ICD-10, SNOMED)
  - Lab values (e.g., HbA1c > 6.5%)
  - Medications
  - Procedures
  - Clinical observations
- Used to identify **patients or events** corresponding to a clinical phenomenon of interest (e.g., “type 2 diabetes”, “ICU delirium”, “post-operative infection”).
- In practice, it is typically represented as a **cohort definition**: inclusion/exclusion logic applied over time, not just a static list of codes.

> As emphasized in the *Book of OHDSI*:  
> *“A cohort is not defined by [just a] code set. A well-defined cohort specifies how a patient enters a cohort and how a patient exits a cohort.”*  
> This means that timing, frequency, and context matter.

---

##  Why Do We Need Computable Phenotypes?

- **Real-world data is messy** - clinical concepts are not neatly labeled.
- We must **infer clinical states** from data collected for routine care or billing.
- Computable phenotypes provide a **standard bridge** between:
  - **Clinical understanding** (what coma means medically)
  - **Data logic** (how to find it in structured EHR fields)
- They enable:
  - **Reproducible research**: same definition = same cohort across studies.
  - **AI/ML development**: accurate outcome labeling is essential for model training.
  - **Portability**: definitions can be shared and reused across datasets and institutions.
  - **Efficiency**: avoids reinventing definitions for common conditions (e.g., sepsis, diabetes).

 *Example:* If we want to train a model to predict coma recovery, we first need a consistent way to define “coma” and “recovery” in the data.

---

## Types of Phenotype Definitions

### 1. **Rule-Based Phenotypes**
- Built using **explicit logical rules**, typically by domain experts.
- Example:
  - *“≥2 outpatient diagnoses of type 2 diabetes (ICD-10 E11.x) AND HbA1c > 6.5%.”*
- Transparent, interpretable, and easy to implement with SQL, ATLAS, or cohort tools.
- Often used in clinical studies and shared libraries (e.g., OHDSI Phenotype Library).

### 2. **Probabilistic / ML-Based Phenotypes**
- Use **machine learning models** to classify patients.
- Trained on labeled datasets or proxy gold standards.
- Example:
  - Logistic regression or neural network identifies “drug-induced liver injury” based on patterns.
- Can capture **complex or subtle patterns** that rules might miss.
- Require:
  - Ground truth labels (e.g., from chart review or registries)
  - Careful validation to avoid overfitting or spurious features

*Note:* Many modern phenotyping approaches use **hybrids**: rules + NLP + ML.

---

#  Background: OMOP CDM and Phenotyping

Developing **sharable, reproducible phenotypes** is greatly enhanced by using a **Common Data Model (CDM)**. One of the most widely adopted CDMs in healthcare research is the **OMOP CDM**, maintained by the OHDSI community.

The OMOP CDM organizes clinical data into **standardized, domain-specific tables** (e.g., `person`, `condition_occurrence`, `drug_exposure`, `measurement`, etc.) and encodes all clinical concepts using **standard vocabularies** like:
- **SNOMED CT** (diagnoses and conditions)
- **LOINC** (laboratory tests)
- **RxNorm** (medications)
- **ICD-9/10** (mapped to SNOMED)

This structure allows researchers to write **a single, portable phenotype definition** that works across any OMOP-transformed dataset. The same definition can be applied in different hospitals, health systems, or research networks - enabling **portability, consistency, and reuse**.

Communities like **OHDSI** and **FDA Sentinel** leverage this model to create **libraries of reusable computable phenotypes** for conditions such as diabetes, sepsis, and COVID-19.

---

##  OMOP CDM: Key Concepts

-  **Domain-Based Tables**  
    - Each type of data (conditions, procedures, drugs, measurements) is stored in its own table.
    - Tables contain rows of clinical events linked to a `person_id`, with standardized `concept_id`s.

-  **Standardized Vocabularies**  
    - Every clinical term is mapped to a **standard concept**.
    - This enables **cross-system interoperability** (e.g., a single diabetes concept can include ICD-9, ICD-10, SNOMED codes).

-  **Temporal Data**  
    - Most tables include **event dates** and optionally **timestamps**.
    - Supports **temporal logic** in phenotyping (e.g., “diagnosis must occur during hospital stay”).

-  **Reproducibility & Portability**  
    - OMOP cohort definitions can be stored as **JSON or SQL**.
    - They can be shared, rerun, and validated across multiple institutions with OMOP-converted data.

---

##  Computable Phenotype vs. Traditional Definitions

| Traditional Definition | Computable Phenotype |
|------------------------|----------------------|
| Written in prose, e.g., in a methods section | Encoded in machine-executable format (SQL, JSON, Python) |
| Ambiguous interpretation possible | Precise and unambiguous logic |
| Lists conditions verbally (e.g., “two diabetes codes + HbA1c > 7%”) | Specifies exact code sets, timing, data sources |
| Hard to reuse or adapt | Easy to share, validate, reuse across datasets |

 *Example:*  
> Traditional: “Patients with at least two diabetes diagnoses and an HbA1c > 7%.”  
>  
> Computable: `condition_occurrence` entries with concept IDs for diabetes (e.g., SNOMED 201826), at least two, ≥30 days apart, AND `measurement` table entry for HbA1c with `value_as_number > 7`, LOINC 4548-4, within 90 days of diagnosis.
>
> ## Why OMOP Matters in Rule-Based and Probabilistic Phenotyping

- Enables **cross-platform** and **multi-institutional research** by harmonizing EHR data into a common schema.
- Reduces **ambiguity** in clinical condition definitions by enforcing structured vocabularies and reproducible logic.
- Simplifies **validation** and **reuse** of phenotypes through sharable cohort definitions and community-reviewed libraries.
- Provides a **standard foundation for both rule-based and machine learning (ML)-based phenotyping workflows**.

### What About Machine Learning?

OMOP is not limited to rule-based phenotyping - it also plays a key role in **ML and probabilistic phenotyping** by:

- Structuring the input space: Features like demographics, diagnoses, medications, labs, and procedures are organized and standardized, making them easy to use as inputs to ML models.
- Enabling **label generation**: Rule-based phenotypes created with tools like **ATLAS** can be used to define cohorts for training supervised models (e.g., “cases” and “controls”).
- Supporting scalable feature extraction: Using OMOP CDM, features can be derived consistently across datasets without bespoke pipelines.
- Ensuring portability: ML models trained on OMOP data are more portable because input fields are consistent across sites.

###  Do I Need ATLAS for ML?

- **Not directly.** ATLAS is not a machine learning tool, but:
  - It helps you define **high-quality cohorts and labels** using clinical logic.
  - These definitions can be exported and used to **train or evaluate ML models**.
  - In semi-supervised pipelines (e.g., using **Aphrodite**), ATLAS-defined cohorts often serve as “anchors” or weak labels.

So while ATLAS is **not required** for ML, **knowing ATLAS accelerates phenotype design**, labeling, and validation, which are essential upstream steps in any clinical ML pipeline.

#  Tools for Phenotype Development

Developing robust, portable computable phenotypes requires both clinical logic and technical tooling. Below are essential tools and platforms used in OMOP-based phenotyping workflows:

---

##  OHDSI ATLAS

- A **free, web-based graphical user interface (GUI)** for building and sharing cohort definitions on OMOP CDM data.
- Features:
  - **Interactive cohort editor**: Build inclusion/exclusion rules without writing code.
  - **Concept set manager**: Select standardized vocabularies (e.g., SNOMED, RxNorm, LOINC).
  - **Temporal criteria**: Define rules like “at least two diagnoses within 30 days.”
  - **Cohort characterization**: View summary statistics and attrition flow diagrams.
  - **Export options**: Generate executable SQL and JSON for downstream use.
- Limitations in notebook-based environments mean that our demonstrations will use code, but the underlying logic parallels what ATLAS does visually.

 [ATLAS Wiki](https://github.com/OHDSI/Atlas/wiki)
 [ATLAS Demo](https://atlas-demo.ohdsi.org/)
 
---

### **OHDSI Athena**
A vocabulary management system to browse, search, and download standardized concept sets (SNOMED, LOINC, RxNorm) for use in OMOP-based queries and phenotypes.

 [Athena Browser](https://athena.ohdsi.org/)

---

##  OHDSI Phenotype Library

- An **open, peer-reviewed repository** of cohort definitions developed by the OHDSI community.
- Each phenotype includes:
  - A clinical description
  - Concept sets and logic
  - Diagnostic performance (e.g., PPV, sensitivity)
- Promotes **reuse and transparency** of validated definitions.
- All entries conform to the **OMOP CDM** using **standardized vocabularies**.

 [Phenotype Library](https://data.ohdsi.org/PhenotypeLibrary/)

---

##  PhysioNet & MIMIC-IV

- **PhysioNet** provides access to clinical waveform and EHR datasets like **MIMIC-IV**:
  - ICU patients' data (labs, meds, diagnoses, vitals)
  - Matched high-resolution waveform data (ECG, SpO₂, BP, etc.)
- **MIMIC-IV-to-OMOP** conversions exist and enable hybrid phenotyping (e.g., combining structured labs with dynamic waveforms).
- Useful in complex challenges (e.g., **coma recovery**, **AKI detection**).
- Supports multimodal and temporal modeling.

 [MIMIC-IV on PhysioNet](https://physionet.org/content/mimic-iv-demo/2.2/)

---
##  OHDSI/Aphrodite: Semi-Supervised Phenotyping

- [Aphrodite](https://github.com/OHDSI/Aphrodite) is an OHDSI R package that supports **semi-supervised phenotype generation**; a semi-automated ML-based phenotyping framework built on OMOP CDM.
- Uses **anchor-based learning**:
  - You specify reliable “anchor” variables (e.g., a specific diagnosis or drug exposure).
  - The model uses these to find **latent, probabilistic phenotypes** from EHR data.
- Uses weak supervision and surrogate labels (e.g., NLP terms, surrogate codes) to train classifiers.
- Supports logistic regression and other simple classifiers for portability.
- Benefits:
  - Reduces reliance on manual labeling or chart review
  - Scales phenotype development in large datasets
- Works with OMOP CDM and integrates with OHDSI infrastructure.
- Ideal for **bootstrapping phenotypes** when full ground truth is unavailable.

 [Aphrodite GitHub](https://github.com/OHDSI/Aphrodite)
 
---

### **HDR UK Phenotype Library**
UK-based phenotype repository that includes curated and validated phenotype definitions used in national health research projects.
- Focuses on reproducibility and metadata transparency.
- Definitions often include code lists, logic, and implementation details.
- May require adaptation to OMOP CDM structure.

 [HDR UK Phenotype Library](https://phenotypes.healthdatagateway.org/phenotypes/)

---

### **ComPLy (Computable Phenotype Library)**
- Contains a growing library of phenotype definitions, with attention to formal structure and cross-institutional compatibility.
- Definitions are available in structured formats that support reusability and translation.
- Emphasis on explainability and formal computability.

 [ComPLy](https://phenotypelibrary.org/search)

##  Programming Tools

### R
- `DatabaseConnector`: Connects to OMOP CDM databases.
- `CirceR`: Renders ATLAS cohort logic and JSONs programmatically.

### Python
- Use **pandas** for basic querying and analysis on synthetic OMOP-like data.
- For live OMOP CDM databases, use:
  - Direct SQL (via `psycopg2`, `sqlalchemy`, etc.)
  - REST APIs if available
  - Tools like `pyomop`

---
#  Step-by-Step: Developing a Computable Phenotype

Developing a robust computable phenotype involves a structured process that combines clinical expertise, data standardization, and iterative validation. The steps:

---

## 1️ Define the Clinical Concept and Use Case

- **Clearly articulate** the phenotype and why it's needed.
  - Is it an **outcome** (e.g., postoperative infection)?
  - An **exposure** (e.g., medication use)?
  - A **condition** (e.g., coma)?
- Example: *"Coma recovery in ICU"* could be defined as a comatose patient (e.g., GCS ≤ 8) who improves within 72 hours.
- Collaborate with **clinical experts** to specify defining features.

---

## 2️ Identify Data Criteria (Variables & Codes)

- Determine which EHR data elements act as **proxies**:
  - Diagnoses → `condition_occurrence` (ICD-10, SNOMED)
  - Labs → `measurement` (LOINC)
  - Medications → `drug_exposure` (RxNorm)
  - Vitals or observations → `observation`, `measurement`
- Use **OMOP-standard vocabularies** (via tools like OHDSI Athena).
- Create **concept sets**: groups of related concept IDs.

###  Examples:
- **Acute kidney injury (AKI)**: Creatinine + AKI diagnosis codes
- **Hospital-acquired infection (HAI)**: Culture results + timing post-admission
- **Coma Recovery**: GCS score + traumatic brain injury codes

---

## 3️ Set Temporal Logic and Criteria

- Define **time-based rules**:
  - Multiple code occurrences? (e.g., “2 diagnoses > 30 days apart”)
  - Time windows? (e.g., “infection after 48h of admission”)
  - Index date: When does the patient enter the cohort?
  - Phenotype duration?
- Add **exclusion criteria** (e.g., confounding conditions).

###  Coma Recovery Example:
- Entry: ICU admission with GCS ≤ 8 on day 0
- Outcome: GCS > 8 within 72 hours
- Exclude: Patients with pre-existing neurologic conditions

---

## 4️ Implement the Phenotype Logic

- Use **ATLAS** to:
  - Define initial event (e.g., ICU visit with condition X)
  - Add inclusion rules (e.g., age ≥ 18, measurement threshold)
  - Export SQL or JSON
- Or code directly in **SQL/Python/R** using OMOP tables.
- Use **synthetic data** for safe prototyping (e.g., Synthea OMOP).

---

## 5️ Test the Definition (Internal Validation)

- Run the cohort query:
  - Do the numbers make sense?
  - Inspect patient records if possible.
- Use [**CohortDiagnostics**](https://ohdsi.github.io/CohortDiagnostics/) to:
  - Review age/gender distributions
  - Check concept usage patterns
  - Spot potential logic errors

---

## 6 Refine the Definition

- Adjust concept sets, windows, or logic based on:
  - Unexpected prevalence
  - False positives/negatives
- Keep version control and document changes.

---

## 7️ Validate the Phenotype (External / High-Level)

###  Low-Level (Technical) Validation
- Check that logic accesses the correct OMOP fields and concept IDs.
- Validate units, measurement types, and date joins.

###  High-Level (Clinical) Validation
- Compare phenotype output against:
  - Manual chart review
  - Registry data or gold-standard labels
- Assess:
  - **PPV** (Positive Predictive Value)
  - **Sensitivity** (Recall)
  - **Specificity**

####  Tools:
- [**PheValuator**](https://github.com/OHDSI/PheValuator): Estimates PPV and sensitivity using a secondary model (no chart review).
- **Multi-site validation**: Improves generalizability and robustness.

---

## 8️ Document and Share

- Include:
  - Clinical intent
  - Logic in pseudocode or ATLAS export
  - Validation results
  - Limitations
- Share via:
  - OHDSI Phenotype Library
  - GitHub (for versioning and reuse)
- In publications, append full definitions or link repositories (now often required by journals).

---

##  Note on OMOP's Role

By following the OMOP CDM:
- Your phenotype logic uses **standard concept IDs**, enabling **cross-site portability**.
- Tools like **ATLAS** and **CohortDiagnostics** help encode, review, and refine.
- You create **AI-ready** labels and cohorts, whether for rule-based phenotyping or as ground truth for machine learning models.
## Example Phenotype: Coma Recovery in ICU

### Scenario
We want to identify patients in an Intensive Care Unit (ICU) who are admitted in a coma (very low consciousness due to acute brain injury) and determine who recovers consciousness within 72 hours. This phenotype could be used to study or predict coma recovery outcomes, which is crucial in acute brain injury management (e.g., guiding decisions about life-sustaining therapies).

Our goal is to develop a computable definition for "coma on admission" and the outcome "early recovery vs. non-recovery," using structured EHR data (e.g., GCS scores, diagnosis codes, timing).

### Clinical Definition
- **Cohort Inclusion**: Adult patients (≥18 years) with acute brain injury who are comatose upon ICU admission. "Comatose" is defined by a Glasgow Coma Scale (GCS) score ≤ 8 in the first day of ICU stay. Acute brain injury includes diagnoses like traumatic brain injury, intracerebral hemorrhage, ischemic stroke, etc. ICU setting is required.
- **Outcome**: "Recovery of consciousness" within 72 hours – operationalized as GCS improving to >8 within the first 3 days of ICU, or any clinical documentation of neurological improvement (proxied as GCS > 8). Patients who do not meet this threshold in 72 hours are "non-recovered".

### OMOP Implementation Plan
We will demonstrate with a synthetic dataset using OMOP-like tables (`person`, `visit_occurrence`, `condition_occurrence`, `measurement`, `death`).

**Logic Steps:**
1. Identify candidate cohort entries: ICU visits for patients with an acute brain injury diagnosis on the same day.
2. Among those, check the first-day GCS measurement. Include if GCS ≤ 8.
3. Evaluate outcome: check for any GCS > 8 within 72 hours from admission.
4. Classify patients as "recovered" or "not recovered".

---

```python
# Example Phenotype: Coma Recovery in ICU (OMOP-style synthetic data)

import pandas as pd
from datetime import datetime, timedelta

# --- Synthetic Data Setup ---

# Patients table
persons = pd.DataFrame([
    {"person_id": 1, "birth_year": 1980, "gender": "M"},
    {"person_id": 2, "birth_year": 1950, "gender": "F"},
    {"person_id": 3, "birth_year": 1975, "gender": "M"}
])

# ICU visits
visits = pd.DataFrame([
    {"visit_id": 101, "person_id": 1, "visit_concept": "ICU", "start_date": datetime(2025,1,1), "end_date": datetime(2025,1,10)},
    {"visit_id": 102, "person_id": 2, "visit_concept": "ICU", "start_date": datetime(2025,2,1), "end_date": datetime(2025,2,5)},
    {"visit_id": 103, "person_id": 3, "visit_concept": "ICU", "start_date": datetime(2025,3,1), "end_date": datetime(2025,3,7)}
])

# Conditions
conditions = pd.DataFrame([
    {"person_id": 1, "condition_concept_id": 1001, "condition_name": "Traumatic brain injury", "condition_date": datetime(2025,1,1)},
    {"person_id": 2, "condition_concept_id": 1002, "condition_name": "Stroke", "condition_date": datetime(2025,2,1)},
    {"person_id": 3, "condition_concept_id": 2001, "condition_name": "Myocardial infarction", "condition_date": datetime(2025,3,1)}
])

# GCS measurements
measurements = pd.DataFrame([
    {"person_id": 1, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,1,1), "value": 6},
    {"person_id": 1, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,1,2), "value": 7},
    {"person_id": 1, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,1,3), "value": 9},
    {"person_id": 1, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,1,4), "value": 10},
    {"person_id": 2, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,2,1), "value": 5},
    {"person_id": 2, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,2,2), "value": 5},
    {"person_id": 2, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,2,3), "value": 5},
    {"person_id": 2, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,2,4), "value": 4},
    {"person_id": 3, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,3,1), "value": 15},
    {"person_id": 3, "measurement_concept_id": 3001, "measurement_name": "GCS", "measurement_date": datetime(2025,3,2), "value": 15}
])

# Death table
death = pd.DataFrame([
    {"person_id": 2, "death_date": datetime(2025,2,5)}
])
```
> Our synthetic data has three patients: Person 1 with TBI, initial GCS 6 improving to 9 by day 3; Person 2 with stroke, GCS 5 staying low and the patient dies; Person 3 with a heart attack and normal GCS 15 throughout. Only Persons 1 and 2 meet the “coma on admission due to brain injury” criteria.
```python
# --- Phenotype Logic ---

# Define concept sets
brain_injury_concepts = [1001, 1002]  # brain injury codes
gcs_concept = 3001                    # GCS measurement concept ID

# Step 1: Identify ICU patients with acute brain injury
cohort_candidates = conditions[conditions['condition_concept_id'].isin(brain_injury_concepts)]

# Step 2: Filter by coma (GCS ≤ 8 on admission)
coma_patients = []
for pid in cohort_candidates['person_id']:
    gcs_vals = measurements[(measurements['person_id']==pid) & (measurements['measurement_concept_id']==gcs_concept)]
    if not gcs_vals.empty:
        first_gcs = gcs_vals[gcs_vals['measurement_date'] == gcs_vals['measurement_date'].min()]
        if (first_gcs['value'] <= 8).any():
            coma_patients.append(pid)
coma_patients = set(coma_patients)
print("Cohort (coma on admission):", coma_patients)

# Step 3: Recovery evaluation within 72 hours
recovered = []
for pid in coma_patients:
    gcs_vals = measurements[(measurements['person_id']==pid) & (measurements['measurement_concept_id']==gcs_concept)]
    first_day = gcs_vals['measurement_date'].min()
    cutoff = first_day + timedelta(days=3)
    within72 = gcs_vals[gcs_vals['measurement_date'] <= cutoff]
    if (within72['value'] > 8).any():
        recovered.append(pid)
recovered = set(recovered)
print("Recovered within 72h:", recovered)

# Step 4: Identify non-recovered patients
non_recovered = coma_patients - recovered
print("Not recovered in 72h:", non_recovered)

# Optional: Check for death among non-recovered
non_recovered_df = pd.DataFrame({'person_id': list(non_recovered)})
death_cases = non_recovered_df.merge(death, on='person_id', how='inner')
print("Deaths among non-recovered:", death_cases['person_id'].tolist())
```
> Cohort (coma on admission): {1, 2}
> Recovered within 72h: {1}
> Not recovered in 72h: {2}
> Deaths among non-recovered: [2]

### Interpreting the Result
- **Person 1** and **Person 2** met the cohort criteria (coma on admission due to brain injury).
- Person 1 recovered: GCS increased to 9 by day 3.
- Person 2 did not recover: GCS stayed ≤ 5 and the patient died.
- **Person 3** was not included in the cohort: no acute brain injury diagnosis and normal GCS.

This example demonstrates how we apply inclusion criteria and outcome assessment in code.

### Real-World Implementation Notes
In a real OMOP database, implementation might use SQL joins across `condition_occurrence`, `visit_occurrence`, and `measurement` tables with temporal constraints (e.g., ensuring the measurement is within 24h of ICU admission).

**Phenotyping Logic Summary:**
- Define cohort entry: ICU admission with brain injury and coma (GCS ≤ 8).
- Additional filters: Adult patients, first ICU stay only.
- Outcome: GCS improvement within 72 hours.
- Label for modeling: Binary yes/no for early recovery.

### Validation Strategy
- Internal validation: Review sample cases (e.g., 20 "recovered" and 20 "non-recovered") to assess positive predictive value (PPV) and sensitivity.
- Evaluate edge cases: e.g., comatose patients without explicit brain injury diagnosis might be missed.

### Phenotype Reusability
This definition uses standard OMOP concepts (e.g., SNOMED for brain injury, LOINC for GCS). The cohort logic can be exported as JSON or SQL and reused across OMOP datasets (e.g., MIMIC-IV OMOP). This ensures consistent, replicable identification of coma recovery cohorts across institutions.

## Other Example Challenges and Phenotypes

Briefly, let's touch on how one might approach other complex phenotyping challenges mentioned:

### Healthcare-Associated Infection (HAI) in ICU
- **Phenotyping Goal**: Distinguish hospital-acquired infections from those present on admission.
- **Computable Definition**:
  - No infection evidence on admission (e.g., no antibiotics or positive cultures in first 48 hours).
  - New positive culture or antibiotic course initiated after 48 hours.
- **Advanced Features**:
  - Include lab trends (e.g., leukocytosis), vital signs (fever), or waveform data (e.g., temperature, HRV).
  - Validate against standard surveillance definitions (e.g., CDC for ICU infections).
- **Student Exercise**: Propose a simple computable phenotype for HAI using structured data.

### Neonatal Phenotyping
- **Context**: Newborns (e.g., in NICU) require age-specific thresholds.
- **Example Phenotype**: Neonatal sepsis based on lab values, apnea events, bradycardia.
- **Challenges**:
  - Gestational age-based variation in normal ranges.
  - High false positive rate - need multiple signals for specificity.
- **Student Exercise**: Define phenotype criteria stratified by gestational or postnatal age.

### Multimodal Coma Recovery Prediction
- **Phenotype Use**: Serves as label for predictive models.
- **Features**:
  - Static: Diagnosis, demographics.
  - Dynamic: GCS trends, vital signs, medication use.
- **Key Point**: Accurate outcome phenotype definition ensures reliable ML training labels.

### Physiologic “Cardio-metabolic Age”
- **Not a binary phenotype**, but a continuous derived metric.
- **Approach**:
  - Use self-supervised learning on longitudinal EHR + ICU waveform data.
  - Train to predict chronological age, then recalibrate to reflect physiologic risk.
- **Validation**: Check correlation with outcomes (e.g., mortality, frailty).
- **OMOP Consideration**: Use OMOP for structured data, supplement with waveform data from external sources.

### Early Detection and Subphenotyping of AKI
- **Standard AKI Phenotype**: Based on **Kidney Disease: Improving Global Outcomes (KDIGO)** (creatinine rise ≥0.3 mg/dL in 48h or x2 baseline in 7d).
- **Advanced Use Cases**:
  - Early detection using lab/vital sign patterns or NLP.
  - Subphenotyping via unsupervised clustering (e.g., shock-related vs toxic AKI).
- **Student Exercise**:
  - Implement AKI phenotype on synthetic data.
  - Propose surrogate criteria for earlier detection.
  - Discuss validation strategies.

---

Across these examples, successful phenotyping always involves:
- Clear clinical definition and use-case framing.
- Rule or ML-based operationalization.
- Standardized data usage (e.g., OMOP concepts).
- Internal and external validation.

Use resources like OHDSI’s Phenotype Library as starting points where available.

## Validating Phenotype Definitions

Validation is critical to ensure that a computable phenotype truly reflects the intended clinical concept and behaves reliably in data. Below is a summary of best practices:

- **Face Validity / Expert Review**: Engage clinicians early. Ensure your criteria make sense clinically and capture the intended patients. This step can prevent logical errors that arise from a purely data-driven perspective.

- **Internal Consistency Checks**: Use diagnostics like OHDSI's CohortDiagnostics to assess concept distributions, incidence rates, and cohort overlaps. Discrepancies (e.g., unintended diagnoses dominating a cohort) often signal logic flaws.

- **Ground Truth Comparison**: Where possible, compare algorithm outputs to a gold standard (manual chart review, registry labels). This allows estimation of sensitivity, specificity, PPV, and NPV. Consider splitting validation into:
  - *Low-level*: Are you pulling the right data fields and concepts?
  - *High-level*: Does the result match the true clinical state?

- **Multiple Sites / Datasets**: Validate on external datasets to ensure generalizability. Differences in coding habits can cause a well-performing phenotype in one setting to fail elsewhere. OMOP CDM helps portability, but validation is still necessary.

- **Documenting Validation Results**: Transparently report the methods and results of validation, including metrics and limitations. This enables others to interpret results correctly and adapt definitions to their context.

- **Continuous Improvement**: Phenotypes evolve. Maintain version control, revalidate after changes, and document updates. Share via open libraries like OHDSI’s Phenotype Library.

Robust phenotyping is interdisciplinary, iterative, and foundational for reliable downstream analytics and ML models.

---

## Homework Assignment

- Build a basic cohort definition in ATLAS.
- Try coding a simple phenotype using synthetic data.
- Explore published definitions in the OHDSI Phenotype Library.
> Remember: your phenotype is the foundation for robust inference or ML model development.

### Optional Task
Choose one of the following and develop a computable phenotype:

#### 1. Early Hospital-Acquired Infection (HAI) Detection
- **Goal**: Identify patients who developed infections during hospitalization.
- **Criteria**: No infection signs on admission; positive culture or antibiotics started after 48h.
- **Validation**: Compare with infection control logs or chart review.

#### 2. NICU Alarm Fatigue: Significant Apnea/Bradycardia Events
- **Goal**: Identify clinically significant events while excluding false alarms.
- **Criteria**: Event durations, oxygen desaturation thresholds, stratify by gestational age.
- **Validation**: Compare with nurse annotations or alarm logs.

#### 3. AKI Onset and Subphenotyping
- **Goal**: Identify and cluster types of AKI.
- **Criteria**: KDIGO thresholds on creatinine; subclassify using vitals, diagnoses.
- **Validation**: Confirm AKI by manual chart review or match known outcomes.

**Deliverables:**
1. **Phenotype Definition** – Include logic, thresholds, and rationale.
2. **Code or Pseudocode** – SQL/Python/R or structured pseudocode.
3. **Validation Plan** – Describe how you'll test accuracy.

---

## Quiz Questions

1.	In your own words, what is a computable phenotype and how does it differ from a traditional narrative phenotype description?
2.	Why is using a common data model like OMOP and standard vocabularies important in sharing phenotype definitions across institutions?
3.	Name two tools or resources that can assist in creating computable phenotypes and briefly describe their roles (e.g., what does OHDSI ATLAS provide? What is contained in the OHDSI Phenotype Library?).
4.	Suppose you want to define a phenotype for “Major Depressive Disorder (MDD)” using EHR data. What data elements might you include in the criteria (give at least three), and what are some challenges in making this definition computable?
5.	Describe the difference between low-level (technical) validation and high-level (clinical) validation of a phenotype algorithm. Why are both necessary?
6.	If a phenotype algorithm has a sensitivity of 95% and a positive predictive value (PPV) of 60%, what does that mean in practical terms? In what scenarios might such an algorithm be acceptable, and when might it be problematic?
7.	Provide an example of a temporal criterion in a phenotype definition (for any condition) and explain how you would implement it in a database query or code.
8.	How can unstructured data (like clinical notes) or high-frequency data (like ICU waveforms) enhance phenotyping? What additional steps or tools are needed to incorporate these into a computable phenotype?

## Resourses

1. [The Book of OHDSI](https://ohdsi.github.io/TheBookOfOhdsi/)
2. [A User's Guide To Computable Phenotypes (PDF)](https://dcricollab.dcri.duke.edu/sites/NIHKR/KR/Blake_Users_Guide_to_Computable_Phenotypes.pdf)
3. [Electronic Health Records-Based Phenotyping](https://rethinkingclinicaltrials.org/chapters/conduct/electronic-health-records-based-phenotyping/electronic-health-records-based-phenotyping-introduction/)
4. [ATLAS Tutorial: Create A New Cohort Definition](https://www.youtube.com/watch?v=JQFGedOaNiw)
5. [2019 OHDSI Tutorials - Cohort Definition & Phenotyping (1 of 3)](https://www.youtube.com/watch?v=SKiZpHEfv_E)
6. [2019 OHDSI Tutorials - Cohort Definition & Phenotyping (2 of 3)](https://www.youtube.com/watch?v=SFCPwKJORy4)
7. [2019 OHDSI Tutorials - Cohort Definition & Phenotyping (3 of 3)](https://www.youtube.com/watch?v=FO3vQ9ajObY)

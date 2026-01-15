###  Glossary of Key Terms for Computable Phenotyping

| **Term** | **Definition** |
|---------|----------------|
| **Computable Phenotype** | A machine-executable definition of a clinical condition or event using structured EHR data (diagnoses, labs, medications, procedures, etc.). |
| **Cohort** | A group of patients defined by specific inclusion/exclusion rules over time. In OMOP/OHDSI, a cohort represents the result of a phenotype query. |
| **Index Date** | The date a patient qualifies for the cohort-anchors temporal logic (e.g., "30 days after index"). |
| **Phenotype Logic** | The rule set (inclusion, exclusion, temporal) that defines how cases are identified from data. |
| **Inclusion Criteria** | Conditions that must be met for a patient to enter a cohort. |
| **Exclusion Criteria** | Conditions that disqualify a patient from the cohort (e.g., confounding conditions). |
| **Temporal Logic** | Time-based rules linking clinical events (e.g., "AKI onset within 48h of surgery"). |
| **OMOP CDM** | OMOP Common Data Model: a standardized structure and vocabulary to harmonize clinical data across institutions. |
| **Concept ID / Standard Concept** | A numeric ID representing a clinical concept (e.g., SNOMED, LOINC) in OMOP. Enables cross-vocabulary querying. |
| **Concept Set** | A group of standard or classification concept IDs representing a higher-level medical entity (e.g., "acute brain injury"). |
| **OHDSI ATLAS** | A graphical OHDSI tool for building cohorts and concept sets from OMOP data, producing SQL/JSON. |
| **OHDSI Athena** | An online tool by OHDSI that provides access to standardized vocabularies (e.g., SNOMED, LOINC, RxNorm) used in OMOP CDM. It helps users search, explore, and download concept sets. |
| **Phenotype Library** | A curated repository of phenotype definitions (e.g., OHDSI Phenotype Library, PheKB), often with validation metadata. |
| **OHDSI Aphrodite** | An R package developed by OHDSI to support semi-automated phenotype generation using weakly supervised learning from labeled/unlabeled EHR data. Useful for creating probabilistic phenotypes. |
| **Validation (Phenotype)** | Assessment of a phenotype’s accuracy. Includes low-level (technical) and high-level (clinical) validation. |
| **Low-Level Validation** | Verifies code logic and data correctness (e.g., ensuring concept sets pull correct patients). |
| **High-Level Validation** | Confirms clinical correctness via chart review or comparison to expert-curated labels. |
| **True Positive (TP)** | A case where the phenotype correctly identifies a patient who truly has the condition. Used to calculate metrics like sensitivity and precision. |
| **False Positive (FP)** | A patient wrongly labeled as having the condition. |
| **False Negative (FN)** | A patient wrongly missed by the phenotype, though they do have the condition. |
| **Positive Predictive Value (PPV)** | The proportion of identified patients who truly have the condition. `PPV = TP / (TP + FP)` |
| **Precision** | Synonym for PPV: proportion of predicted positives that are true. |
| **Sensitivity (recall or true positive rate)** | Proportion of true cases correctly identified. `Sensitivity = TP / (TP + FN)` |
| **Recall** | Synonym for Sensitivity: proportion of actual positives that were captured. |
| **Specificity (true negative rate)** | Proportion of true non-cases correctly excluded. `Specificity = TN / (TN + FP)` |
| **F1 Score** | Harmonic mean of precision and recall. Useful when balancing false positives and false negatives. |
| **Chart Review** | Manual inspection of records by clinicians to establish ground truth for validation. |
| **Clinical Concept** | A structured medical entity (e.g., symptom, disease, lab) represented via a standard vocabulary. |
| **Rule-Based Phenotype** | Defined using deterministic logic (e.g., ≥2 ICD codes + abnormal lab). |
| **Probabilistic / ML-Based Phenotype** | Learned from data patterns by a machine learning model, often integrating longitudinal or unstructured data. |
| **Proxy Variable** | An indirect measure of a clinical concept (e.g., oxygen therapy as proxy for respiratory distress). |
| **Subphenotyping** | Dividing a broad phenotype into meaningful subtypes (e.g., clustering AKI into shock vs nephrotoxic). |
| **Foundation Model (Clinical)** | A large model trained on multimodal healthcare data (EHR, vitals, notes) for generalizable predictive tasks. |
| **Confounder** | A variable associated with both exposure and outcome, potentially distorting true effect estimates (e.g., in a study of sedative use and coma recovery, age could be a confounder - older patients are more likely to receive sedatives and also have slower recovery).* |
| **Bias** | Systematic error in data or analysis that can distort phenotype validity or model performance. Common sources include demographic imbalances, missing data, or institution-specific coding practices. |
| **Gold Standard / Reference Standard** | The most accurate known method for identifying true cases (e.g., expert chart review, registry). |

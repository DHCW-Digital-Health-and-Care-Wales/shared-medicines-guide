## Medicines Implementation Guide

## Introduction

The FHIR® Shared Medicines API has been developed by NHS Wales. The API aims to better support the delivery of care by opening up nationally stored information and data that patients want to share with healthcare professionals. 

Our vision aligns with our health and social care partners in the UK: to produce a library of nationally defined HL7® FHIR® resources and patterns that can be implemented to simplify integration and interoperability within the UK.

## Mission

NHS Wales are working to collaborate with patients, healthcare workers and other key stakeholders to develop a national shared medications service which will be available to organisations providing direct care to the patient. The Medicines information will be visible thoughout the patient journey. It will collate and present information about the patient that should be known by the clinician or care team in order to provide more effective care. We want a better service experience for the patient, by giving them confidence that the care teams they deal with understand their condition with consistancy. We must improve their sense of well being and a small but important way to do this is to remove the need to ask for the same information multiple times.

### Medicines

Medicines information can indicate the patient's current and prior relationships with medicines being prescribed, dispensed and administered. The use of medicines are complex and touch on many general medical and clinical specialties. Patients can buy and supply their own medicines as well as those prescribed to them. There is a lot of variance involved.

Key purposes include:

* ensuring the key information is shared consistently across health and care – wherever the patient is treated

* ensuring that the information is clearly visible in clinical systems

* Identifying a patient that may require localised adjustments to care 




Authorised healthcare workers can:

* query to see a collated medicines record from primary and secondary care
* modify the collated medicines record to update or add a new medicines information



## Overview

Medicines information should be moved between different software using HL7 FHIR R4

- [DataStandardsWales-MedicationList](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-MedicationList?version=2.2.0)
- [DataStandardsWales-MedicationStatement](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-MedicationStatement?version=2.2.0)
- [DataStandardsWales-MedicationRequest](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-MedicationRequest?version=2.2.0)
- [DataStandardsWales-MedicationDispense](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-MedicationDispense?version=2.2.0)
- [DataStandardsWales-MedicationAdministration](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-MedicationAdministration]?version=2.2.0)
- [DataStandardsWales-Medication](https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home/FHIR-Assets/Profiles-and-Extensions/Profiles/DataStandardsWales-Medication]?version=2.2.0)



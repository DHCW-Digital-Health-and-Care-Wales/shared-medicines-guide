# IHR to MedicationStatement

This details the field mapping between the IHR response and an NHS Wales profiled MedicationStatementFHIR resource. 

See the [source](#source-structure) and [target](#target-structure) data structures for reference. 

Quoted values ("") are static values and not read from IHR.

| FHIR                             | IHR                                                          | notes              |
| ----------------------------------- | ------------------------------------------------------------ | ---------- |
| meta.profile                     | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-MedicationStatement" |                    |
| identifier.system                | "https://fhir.nhs.wales/Id/wgpr-source-identifier"           |                    |
| identifier.value                 | ``RecordElementProvenance.RecordElementID.IdValue``          |                    |
| extention:lastIssueDate.url | "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-MedicationStatementLastIssueDate" |  |
| extention:lastIssueDate.value | "active"                                                     | see [note 1](#note-1-last-issue-date) |
| extention:CourseOfTherapyType.url | "https://fhir.nhs.wales/StructureDefinition/Extension-DataStandardsWales-MedicationCourseOfTherapyType" |  |
| extention:CourseOfTherapyType.coding.system | "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy" |  |
| extention:CourseOfTherapyType.coding.Code |  | see [note 2](#note-2-course-of-therapy-type-coding) |
| extention:CourseOfTherapyType.coding.Display |  |  |
| status |  | see [note 3](#note-3-status) |
| category | "Community" |  |
| medicationCodeableConcept.Coding[0].System | ``Drug.DrugDictionary`` <br />``Drug.DrugDictionary.ClincalCodeSchemaId``<br />``Drug.DrugDictionary.ClincalCodeSchemaVersion`` | see [note 4](#note-4-coding-system) |
| medicationCodeableConcept.Coding[0].Code | ``Drug.DrugCodeValue`` |  |
| medicationCodeableConcept.Coding[0].Display | ``Drug.DrugIdentifier`` |  |
| medicationCodeableConcept.Text | ``Drug.DrugIdentifier`` |  |
| effectiveDateTime | ``FirstPrescribedDate`` |  |
| DateAsserted | ``RecordElementProvanance.RecordedInSourceAt`` |  |
| Dosage.Text | ``Dosage.DoseDescription`` |  |
| Dosage.AdditionalInstructions.Text | ``AdditionalInstructions`` |  |
| Subject |  | Referenced resource. See [patient](Patient.md) for more information. |
| InformationSource |  | Referenced resource. See [organization](Organization.md) for more information. |



#### Notes

##### Note 1: last issue date

Uses the most recent value in `issued` .  If the medication hasnt been issed then the extention si not added.



##### Note 2: course of therapy type coding

Determined by the XML node name and ``prescriptionType``:

* when XML node name is  ``PrecribedAcute``, or ``PrecribedDiscontinued`` and ``prescriptionType`` is "acute":
  * code = "acute"
  * display = "Short course (acute) therapy"
* when ``PrecribedRepeat``, or ``PrecribedDiscontinued`` and ``prescriptionType`` is "repeat"
  * code = "continuous"
  * display = "Continuous long term therapy"



##### Note 3: status

Status follows a complicated pattern to address variation between suppliers

* when ``additionalInstructions`` contains "not issued" or ``Status`` contains "not issued" 
  * Unknown
* when ``Status`` contains "current" or there is no ``status`` and  XML node name is  ``PrecribedAcute`` or ``PrecribedRepeat``
  * Active
* when ``Status`` contains "mistake"
  * EnteredInError
* when ``Status`` contains "past" or "expired"  or the XML node name is  ``PrecribedDiscontinued``
  * Completed
* otherwise
  * Unknown





##### Note 4: coding system

uses ``Drug.DrugDictionary``, ``Drug.DrugDictionary.ClincalCodeSchemaId and Drug.DrugDictionary.ClincalCodeSchemaVersion`` to determine the coding system. 

The ``DrugDictionary`` node is not available from both suppliers, but GP systems are DM+D compliant. DM+D is assumed when there is no ``DrugDictionary`` node for this reason. 



* When ``Drug.DrugDictionary`` is not present " then "https://dmd.nhs.uk"
* When ``Drug.DrugDictionary`` is "dm+d" then "https://dmd.nhs.uk"
* When ``Drug.DrugDictionary`` is "read" and ``ClinicalCodeSchemeVersion `` is 2 then "http://read.info/v2"
* When ``Drug.DrugDictionary`` is "read" and ``ClinicalCodeSchemeVersion `` is 3 then "http://read.info/ctv3"



If there is no ``Drug.DrugCodeValue`` at all, then this logic is avoided completely and a transfer degraded snomed coded CodeableConcept  is returned with its text set as ``Drug.DrugIdentifier``.



### source structure

```xml
<!--RecordElement repeats-->
<RecordElement>
    <RecordElementProvenance>
        <RecordElementID/>
        <ExtractedAt/>
        <RecordedBy>
            <StructuredName>
                <Title/>
                <GivenName/>
                <FamilyName/>
            </StructuredName>
        </RecordedBy>
        <Role/>
        <RecordedInSourceAt/>
        <RecordingOrganisation>
            <OrganisationId/>
            <OrganisationName/>
        </RecordingOrganisation>
        <SourceSystemID/>
        <SourceSystemType/>
    </RecordElementProvenance>
    <!-- one of  PrescribedAcute, PrescribedRepeat, PrescribedDiscontinued-->  
    <!-- note that DrugDictionary is not available from all suppliers--> 
    <PrescribedDiscontinued>
        <Drug>
            <DrugCodeValue/>
            <DrugIdentifier/>
            <DrugDictionary>
                <ClinicalCodeSchemeVersion/>
                <ClinicalCodeSchemeId/>
            </DrugDictionary>
        </Drug>
        <QuantityPrescribed>
            <QuantityValue/>
            <QuantityUnit/>
        </QuantityPrescribed>
        <Dosage>
            <DoseDescription/>
        </Dosage>
        <PrescriptionType/>
        <FirstPrescribedDate/>
    </PrescribedDiscontinued>
    
    <PrescribedRepeat>
        <Drug>
            <DrugCodeValue/>
            <DrugIdentifier/>
            <DrugDictionary>
                <ClinicalCodeSchemeVersion/>
                <ClinicalCodeSchemeId/>
            </DrugDictionary>
        </Drug>
        <QuantityPrescribed>
            <QuantityValue/>
            <QuantityUnit/>
        </QuantityPrescribed>
        <Dosage>
            <DoseDescription/>
            <LastPrescribedDate/>
        </Dosage>
        <PrescriptionType/>
        <FirstPrescribedDate/>
        <IssuesOnCurrent/>
        <IssuesAuthorised/>
    </PrescribedRepeat>

    <PrescribedAcute>
        <Drug>
            <DrugCodeValue/>
            <DrugIdentifier/>
            <DrugDictionary>
                <ClinicalCodeSchemeVersion/>
                <ClinicalCodeSchemeId/>
            </DrugDictionary>
        </Drug>
        <QuantityPrescribed>
            <QuantityValue/>
            <QuantityUnit/>
        </QuantityPrescribed>
        <Dosage>
            <DoseDescription/>
        </Dosage>
        <AdditionalInstructions/s>
            <PrescriptionType/>
            <FirstPrescribedDate/>
            </PrescribedAcute>
</RecordElement>
```

### target structure

```xml
<MedicationStatement>
    <meta>
        <profile/>
    </meta>
    <contained/>
    <extension/>
    <identifier>
        <system/>
        <value/>
    </identifier>
    <status/>
    <category/>
    <medicationCodeableConcept>
        <coding>
            <system/>
            <code/>
            <display/>
        </coding>
    </medicationCodeableConcept>
    <subject>
        <reference/>
        <type/>
        <display/>
    </patient>
    <effectiveDateTime/>
    <dateAsserted/>
    <dosage>
        <text/>
        <additionalInstructions>
            <text/>
        </additionalInstructions>
    </dosage>
    <InformationSource>
        <reference/>
        <type/>
        <display/>
    </InformationSource>
</MedicationStatement>
```


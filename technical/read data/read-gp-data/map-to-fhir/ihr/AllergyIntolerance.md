# IHR to AllergyIntolerance

This details the field mapping between the IHR response and an NHS Wales profiled AllergyIntolerance FHIR resource. 

See the [source](#source-structure) and [target](#target-structure) data structures for reference. 

Quoted values ("") are static values and not read from IHR.

| FHIR                             | IHR                                                          | notes              |
| ----------------------------------- | ------------------------------------------------------------ | ---------- |
| meta.profile                     | ``"https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-AllergyIntolerance"`` |                    |
| identifier.system                | ``"https://fhir.nhs.wales/Id/wgpr-source-identifier"``          |                    |
| identifier.value                 | ``RecordElementProvenance.RecordElementID.IdValue``          |                    |
| clinicalStatus.coding.system     | ``"http://terminology.hl7.org/CodeSystem/allergyintolerance-clinical"`` |                    |
| clinicalStatus.coding.value      | ``"active"  ``                                                   | always active      |
| code.coding.system |  | see [note 1](#note-1-coding-system) and [note 2](#note-2-verify-allergy-coding-feature) |
| code.coding.value | ``ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeValue`` |  |
| code.coding.display | ``ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeSelectedTerm`` |  |
| code.text | ``ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeSelectedTerm`` |  |
| dateRecorded | ``DateRecorded`` |  |
| onsetDateTime | ``StartDate`` |  |
| patient |  | Referenced resource. See [patient](Patient.md) for more information. |
| recorder |  | see [note 3](#note-3-recorder) |



#### Notes

##### Note 1: coding system

System determined by ``ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeScheme.ClinicalCodeSchemeId``.

* When SCT or SNOMED then ``"http://snomed.info/sct"``
* When READ and ``ihr:ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeScheme.ClinicalCodeSchemeVersion `` is 2 then ``"http://read.info/v2"``
* When READ and ``ihr:ClinicalWarning.ClinicalInformation.ClinicalCode.ClinicalCodeScheme.ClinicalCodeSchemeVersion `` is 3 then ``"http://read.info/ctv3"``



##### Note 2: verify allergy coding feature

If the `tryMapReadCodingToSnomed` feature is enabled then each allergy coding received from the GP system is verified via terminlology services prior to being surfaced to the consumer. The feature uses the UK SNOMED concept maps from READ coding to SNOMED coding. This finds the equivalent SNOMED code for the READ code. If the match exists and matches exactly (including the display element) then the SNOMED coding is **added**. If not and the ``addSnomedDegradedCoding`` feature is also enabled then the following SNOMED coding is **added** to flag the record data as transfer degraded.

| FHIR                | IHR                              | notes |
| ------------------- | -------------------------------- | ----- |
| code.coding.system  | ``"http://snomed.info/sct"``         |       |
| code.coding.value   | ``"196411000000103"``                |       |
| code.coding.display | ``"Transfer-degraded record entry"`` |       |



##### Note 3: recorder

Recorder is a referenced resource. The IHR Source doesn't produce sufficient detail to identify the recording individual by a professional registration body. A practitioneRole is created to identify a role and organization and contained within the allergyIntolerance because of this limitation. 

| FHIR               | IHR                                                          | notes                                                        |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| id                 | ``RecordElementProvenance.RecordElementID.IdValue``          |                                                              |
| meta.profile       | ``"https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-PractitionerRole"`` |                                                              |
| code.coding.system | ``"http://snomed.info/sct" ``                                    |                                                              |
| code.coding.code   |                                                              | see [note 3A](#note-3a-recorder-roles)                      |
| organization       |                                                              | Logically referenced resource. See [organization]() for more information. |

##### Note 3A: recorder roles

Roles are mapped from the ``RecordElementProvenance.Role.IdValue`` to SNOMED

| IHR Role                       | coding.code | coding.display                     |
| ------------------------------ | ----------- | ---------------------------------- |
| "administrator"                | "158966004" | "Medical administrator - national" |
| "community nurse"              | "224540001" | "Community nurse"                  |
| "gp registrar"                 | "309327004" | "General practitioner registrar"   |
| "partner" or "senior partner"  | "309323000" | Partner of general practitioner"   |
| "general medical practitioner" | "62247001"  | "General practitioner"             |
| "pharmacist"                   | "46255001"  | "Pharmacist"                       |





### source structure

```xml
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
    <AllergyAdverseReaction>
        <ClinicalWarning>
            <ClinicalInformation>
                <ClinicalCode>
                    <ClinicalCodeValue/>
                    <ClinicalCodeScheme>
                        <ClinicalCodeSchemeVersion/>
                        <ClinicalCodeSchemeId/>
                    </ClinicalCodeScheme>
                    <ClinicalCodeSelectedTerm/>
                </ClinicalCode>
            </ClinicalInformation>
        </ClinicalWarning>
        <StartDate/>
    </AllergyAdverseReaction>
</RecordElement>
```

### target structure

```xml
<AllergyIntolerance>
    <meta>
        <profile/>
    </meta>
    <contained>
        <PractitionerRole>
            <id/>
            <meta>
                <profile/>
            </meta>
            <identifier>
                <system/>
                <value/>
            </identifier>
            <organization>
                <type/>
                <identifier>
                    <use/>
                    <system/>
                    <value/>
                </identifier>
                <display/>
            </organization>
            <code>
                <coding>
                    <system/>
                    <code/>
                    <display/>
                </coding>
            </code>
        </PractitionerRole>
    </contained>
    <identifier>
        <system/>
        <value/>
    </identifier>
    <clinicalStatus>
        <coding>
            <system/>
            <code/>
            <display/>
        </coding>
    </clinicalStatus>
    <code>
        <coding>
            <system/>
            <code/>
            <display/>
        </coding>
        <text/>
    </code>
    <patient>
        <reference/>
        <type/>
        <display/>
    </patient>
    <onsetDateTime/>
    <recordedDate/>
    <recorder>
        <reference/>
        <type/>
        <display/>
    </recorder>
</AllergyIntolerance>
```


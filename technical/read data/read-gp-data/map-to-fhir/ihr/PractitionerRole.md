  # IHR to PractitionerRole

This details the field mapping between the IHR response and an NHS Wales profiled PractitionerRole FHIR resource. 

See the [target](#target structure) data structures for reference. There are no source mappings used directly on this resource, but a variation is used for AllegyIntolerance recorder. See [AllergyIntolerance note 3](allergyIntolernace.md#note-3%3A-recorder) for more details. The following is used generally to describe the patient's GP at a GP practice.

Quoted values ("") are static values and not read from IHR.

| FHIR                       | IHR                                                          | notes                                                        |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| meta.profile               | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-PractitionerRole" |                                                              |
| practitioner               | "official"                                                   | Referenced resource. See [practitioner](Practitioner.md) for more information. |
| orgnaization               |                                                              | Referenced resource. See [organization](Organization.md) for more information. |
| code[0].coding.system      | "http://snomed.info/sct"                                     |                                                              |
| code[0].coding.code        | "62247001"                                                   |                                                              |
| code[0].coding.display     | ""General practitioner"                                      |                                                              |
| specialty[0].coding.system | "https://fhir.hl7.org.uk/CodeSystem/UKCore-PracticeSettingCode" |                                                              |
| specialty[0].coding.code   | "600"                                                        |                                                              |
| specialty[0].coding.code   | "General Medical Practice"                                   |                                                              |



### target structure

```xml
<PractitionerRole>
    <meta>
        <profile/>
    </meta>
	<practitioner>
        <reference/>
        <type/>
        <display/>
    </practitioner>
    <organization>
        <reference/>
        <type/>
        <display/>
    </organization>
    <code>
        <system/>
        <code/>
        <display/>
    </code>
    <specialty>
        <system/>
        <code/>
        <display/>
    </specialty>
</PractitionerRole>
```






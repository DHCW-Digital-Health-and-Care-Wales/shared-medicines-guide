  # IHR to List

This details the field mapping between the IHR response and an NHS Wales profiled List FHIR resource. 

See the [target](#target structure) data structures for reference. There are no source mappings used directly on this resource, 

Quoted values ("") are static values and not read from IHR.

| FHIR          | IHR                                                          | notes                                                        |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| meta.profile  | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-MedicationList" |                                                              |
| title         |                                                              | see [note 1](#note 1: list title)                            |
| status        | "current"                                                    |                                                              |
| mode          | "snapshot"                                                   |                                                              |
| code.system   | "http://snomed.info/sct"                                     |                                                              |
| code.code     | "933361000000108"                                            |                                                              |
| code.display  | "Medications and medical devices"                            |                                                              |
| date          |                                                              | see [note 2](#note 2: list date)                             |
| subject       |                                                              | Referenced resource. See [patient](patient.md) for more information. |
| source        |                                                              | Referenced resource. See [practitionerRole](practitionerRole.md) for more information. |
| entry[*].item |                                                              | Referenced resource. See [medicationStatement](medicationStatement.md) for more information. |



##### note 1: list title

"GP Record Medications " plus the date portion of ``list.date`` 

##### note 2: list date

UTC formatted datetime string. Represents the most recent activity in any medicationStatement referenced in an entry item. The most recent activity considers the following properties in medicationStatement:

* effective start
* effective end
* date asserted
* last issue date

### target structure

```xml
<List>
    <meta>
        <profile/>
    </meta>
    <title/>
    <status/>
    <mode/>
	<subject>
        <reference/>
        <type/>
        <display/>
    </subject>
    <source>
        <reference/>
        <type/>
        <display/>
    </source>
    <code>
        <system/>
        <code/>
        <display/>
    </code>
    <entry>
        <item>
            <reference/>
            <type/>
            <display/>
        </item>
    </entry>
</List>
```






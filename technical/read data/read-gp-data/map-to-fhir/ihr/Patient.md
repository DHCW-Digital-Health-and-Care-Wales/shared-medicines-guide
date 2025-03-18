  # IHR to Patient

This details the field mapping between the IHR response and an NHS Wales profiled Patient FHIR resource. 

> Note: nhs number from this source is treated as present and traced and the relevant identifier extension is assigned

  See the [source](#source structure) and [target](#target structure) data structures for reference. 

  Quoted values ("") are static values and not read from IHR.

| FHIR                        | IHR                                                          | notes                                                        |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| meta.profile                | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-Patient" |                                                              |
| identifier.use              | "official"                                                   |                                                              |
| identifier.system           | "https://fhir.nhs.uk/Id/nhs-number"                          |                                                              |
| identifier.value            | ``NHSnumber.IdValue``                                        |                                                              |
| identifer:extention.url     | "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-NHSNumberVerificationStatus" |                                                              |
| identifer:extention.system  | "https://fhir.hl7.org.uk/CodeSystem/UKCore-NHSNumberVerificationStatusWales" |                                                              |
| identifer:extention.code    | "01"                                                         |                                                              |
| identifer:extention.display | "Number present & traced"                                    |                                                              |
| name[0].use                 | "official"                                                   |                                                              |
| name[0].family              | ``Demographics.FormalName.StructuredName.FamilyName``        |                                                              |
| name[0].given[*]            | ``Demographics.FormalName.StructuredName.GivenName``         |                                                              |
| gender                      | ``Demographics.Gender``                                      | see [note 1](#Note 1: gender)                                |
| birthDate                   | ``Demographics.DateOfBirth``                                 |                                                              |
| address.use                 | "home"                                                       |                                                              |
| address.type                | "both"                                                       |                                                              |
| address.line[0]             | ``Demographics.Address.StructuredAddress.PropertyNumber``    |                                                              |
| address.line\[x\]           | ``Demographics.Address.StructuredAddress.StructuredAddress.AddressLine[x]`` |                                                              |
| address.postalCode          | ``Demographics.Address.PostCode``                            |                                                              |
| telecom[x].use              | ``Demographics.Telephone.UnstructuredTelecom.TelecomMode``   | see [note 2](#Note 2: telecom use)                           |
| telecom[x].system           | "http://hl7.org/fhir/contact-point-system"                   |                                                              |
| telecom[x].value            | ``Demographics.Telephone.UnstructuredTelecom.TelecomMode``   |                                                              |
| generaPractitioner          |                                                              | Referenced resource. See [organization](organization.md) for more information. |



#### Notes

##### Note 1: gender

Gender determined using the following logic when reading   ``Demographics.Gender``:

* when "M" then "Male"
* when "F" then "Female"
* otherwise "Unknown"



**Note 2: telecom use**

Telecom use checks if ``Demographics.Telephone.UnstructuredTelecom.TelecomMode`` contains a value:

* contains "home" then "Home"
* contains "mobile" then "Mobile"
* contains "work" then "Work"
* otherwise the value is not set at all



### source structure

```xml
<PatientDetails>
    <NHSnumber/>
    <Demographics>
        <FormalName>
            <StructuredName>
                <Title/>
                <GivenName/>
                <FamilyName/>
            </StructuredName>
        </FormalName>
        <Gender/>
        <DateOfBirth/>
        <Address>
            <StructuredAddress>
                <PropertyNumber/>
                <AddressLine/>
                <AddressLine/>
                <AddressLine/>
                <AddressLine/>
            </StructuredAddress>
            <PostCode/>
            <AddressType/>
        </Address>
        <Telephone>
            <UnstructuredTelecom/>
            <TelecomType/>
            <TelecomMode/>
        </Telephone>
    </Demographics>
</PatientDetails>
```

### target structure

```xml
<Patient>
    <meta>
        <profile/>
    </meta>
	<identifier>
        <system/>
        <value/>
        <use/>
    </identifier>
    <name>
        <use/>
        <family/>
        <given/>
    </name>
    <gender/>
    <birthDate/>
    <address>
        <use/>
        <type/>
        <line/>
        <postalcode/>
    </address>
    <telecom>
        <use/>
        <system/>
        <value/>
    </telecom>
</Patient>
```





| Property path                                               | Transformation                                               | System    | Source                                                       |
| ----------------------------------------------------------- | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ |
| meta.profile                                                | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-Organization" | EMIS,INPS |                                                              |
|identifier.use|"official"|EMIS,INPS||
|identifier.system|"https://fhir.nhs.uk/Id/ods-organization-code"|EMIS,INPS||
|identifier.value||EMIS,INPS|ihr:Practice/ihr:OrganisationId/ihr:IdValue|
|active|"true"|EMIS,INPS||
|name||EMIS,INPS|ihr:Practice/ihr:OrganisationName|
|address.use|"work"|EMIS,INPS||
|address.type|"both"|EMIS,INPS||
|address.line[0]||EMIS,INPS|ihr:Practice/ihr:OrganisationAddress/ihr:StructuredAddress/ihr:PropertyNumber|
|address.line\[x\]||EMIS,INPS|ihr:Practice/ihr:OrganisationAddress/ihr:StructuredAddress/ihr:AddressLine[x]|
|address.postalCode||EMIS,INPS|ihr:Practice/ihr:OrganisationAddress/ihr:PostCode|


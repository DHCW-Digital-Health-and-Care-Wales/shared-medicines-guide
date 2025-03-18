  # IHR to Organization

This details the field mapping between the IHR response and an NHS Wales profiled Organization FHIR resource. 

> Note: there is no direct (first class) relationship between target resources and organization. This resource is itself referenced via the first class practitionerRole relationship with a target. The service has the cabability to return an Organization or correctly reference it. This maintains good data quality when read or stored in other systems.

  See the [source](#source structure) and [target](#target structure) data structures for reference. 

  Quoted values ("") are static values and not read from IHR.

| FHIR               | IHR                                                          | notes |
| ------------------ | ------------------------------------------------------------ | ----- |
| meta.profile       | "https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-Organization" |       |
| identifier.use     | "official"                                                   |       |
| identifier.system  | "https://fhir.nhs.uk/Id/ods-organization-code"               |       |
| identifier.value   | ``GPdetails.Practice.OrganisationId.IdValue``                |       |
| name               | ``GPdetails.Practice.OrganisationName``                      |       |
| address.use        | "work"                                                       |       |
| address.type       | "both"                                                       |       |
| address.line[0]    | ``GPdetails.Practice.OrganisationAddress.StructuredAddress.PropertyNumber`` |       |
| address.line\[x\]  | ``GPdetails.Practice.OrganisationAddress.StructuredAddress.AddressLine[x]`` |       |
| address.postalCode | ``GPdetails.Practice.OrganisationAddress.PostCode``          |       |





### source structure

```xml
<PatientDetails>
    <NHSnumber/>
    <Demographics/>
    <GPdetails>
        <RegisteredGP/>
        <RegisteredGPcode/>
        <UsualGP/>
        <UsualGPcode/>
        <Practice>
            <OrganisationId/>
            <OrganisationName/>
            <OrganisationAddress>
                <StructuredAddress>
                    <PropertyNumber/>
                    <AddressLine/>
                    <AddressLine/>
                    <AddressLine/>
                </StructuredAddress>
                <PostCode/>
                <AddressType/>
            </OrganisationAddress>
            <OrganisationTelecom/>                        
        </Practice>
    </GPdetails>
</PatientDetails>
```

### target structure

```xml
<Organization>
    <meta>
        <profile/>
    </meta>
	<identifier>
        <system/>
        <value/>
        <use/>
    </identifier>
    <name/>
    <address>
        <use/>
        <type/>
        <line/>
        <postalcode/>
    </address>
</Organization>
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


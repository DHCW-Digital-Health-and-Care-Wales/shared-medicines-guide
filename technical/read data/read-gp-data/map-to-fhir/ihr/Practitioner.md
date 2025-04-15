  # IHR to Practitioner

This details the field mapping between the IHR response and an NHS Wales profiled Practitioner FHIR resource. 

  See the [source](#source structure) and [target](#target structure) data structures for reference. 

  Quoted values ("") are static values and not read from IHR.

| FHIR              | IHR                                                          | notes                                               |
| ----------------- | ------------------------------------------------------------ | --------------------------------------------------- |
| meta.profile      | ``"https://fhir.nhs.wales/StructureDefinition/DataStandardsWales-Practitioner"`` |                                                     |
| identifier.use    | ``"official"``                                                 |                                                     |
| identifier.system |                                                              | see [note 1](#note-1-identifer-systems-and-values) |
| identifier.value  | ``RegisteredGPcode.IdValue``                                 |                                                     |
| name[0].use       | ``"official"``                                                   |                                                     |
| name[0].family    | ``RegisteredGP.StructuredName.FamilyName``                   |                                                     |
| name[0].given[*]  | ``RegisteredGP.StructuredName.GivenName``                    |                                                     |



#### Notes

##### Note 1: Identifer systems and values

Sources do not qualify the identifier system so the type of identifier is interpretted from ``RegisteredGPcode.IdValue`` using pattern matching to produce both of these systems. Either provider may outout one of two common forms with well defined patterns. Each can be derived from the other due to one being a checksum based on the other. The identifier values are then generated and both systems below are produced:

* ``"https://fhir.hl7.org.uk/Id/gmp-number"``
* ``"https://fhir.hl7.org.uk/Id/din-number"``

  



### source structure

```xml
<PatientDetails>
    <GPdetails>
        <RegisteredGP>
            <StructuredName>
                <Title/>
                <GivenName/>
                <FamilyName/>
            </StructuredName>
        </RegisteredGP>
        <RegisteredGPcode>
            <IdValue/>
            <IdScheme/>
        </RegisteredGPcode>
        <UsualGP>
            <StructuredName>
                <Title/>
                <GivenName/>
                <FamilyName/>
            </StructuredName>
        </UsualGP>
        <UsualGPcode>
            <IdValue/>
            <IdScheme/>
        </UsualGPcode>
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
            <OrganisationTelecom>
                <UnstructuredTelecom/>
                <TelecomType/>
                <TelecomMode/>
            </OrganisationTelecom>                        
        </Practice>
    </GPdetails>
</PatientDetails>
```

### target structure

```xml
<Practitioner>
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
</Practitioner>
```






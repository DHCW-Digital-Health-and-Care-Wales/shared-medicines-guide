# IHR

The service makes use of an internal IHR Adapter service. This takes FHIR parameters and converts to IHR request. It then takes the IHR response and maps it to a FHIR search response. These request and responses are mapped to and from the IHR SOAP service as described here. 

> The adapter has an overloaded GetGpRecord method which supports a number of FHIR resource variants, eg SearchParams or Parameters. The basic elements that are used to construct the variants are consistant - the IHR request is formed the same regardless.

## FHIR Request

The incoming FHIR request from the consumer is either a List search:

``/list?code={code}&subject.identifier={identifier}&_include={include}&date={date}``

or an AllergyIntolerance search:

``/AllergyIntolerance?patient.identifier={identifier}&_include={include}``

> AllergyIntollerance requests using the above format will result in an internal list based request with an allergy code to make the retrieval criterion selection more consistant.

the ``_include`` parameter will determine which additional resources should be included in the response.

To call the GP record we need to know:

* **the patient's NHS number**: the ``identifier`` parameter if its already an NHS number identifier or discovered in the patient lookup via PDQ
* **the IHR service url**: discovered in the Endpoint lookup via WRDS
* **the IHR record retrieval criterion**: determined by the ``code`` parameter and optionally the ``date`` parameter

in addition to this:

* **the person Identifier**: the responsible person and their Id, determined by ``x-audit-user`` request header or a default in config

  * a FHIR identifier token eg ``https://fhir.hl7.org.uk/Id/gmp-number|G6123456|SYNTHETIC, Practitioner A Dr (GMP: G6123456) (General practitioner)``

* **the device Identifier**: the calling system id and type, determined by ``x-audit-device`` request header or a default in config

  * a FHIR identifier token eg ``https://fhir.nhs.wales/Id/application-instance-identifier|001|Synthetic Application``

    



## IHR Request

### Retrieval criterion

for medication list requests 3 retrieval criterion are generated. For an allery request one criterion is generated.

* ``date`` defaults to two years prior unless a newer date is provided by the consumer but is always ommited for allergy requests
* ``max`` defaults to 200

```xml
 <ihr:RetrievalCriterion>
     <ihr:WhatKind>
         <ihr:IdValue>{one of PrescribedAcute, PrescribedRepeat, PrescribedDiscontinued, AllergyAdverseReaction}</ihr:IdValue>
     </ihr:WhatKind>
     {if date
      <ihr:EffectiveInterval>
    	<ihr:Start>{date:yyyy-MM-ddTHH:mm:ss}</ihr:Start>
	  </ihr:EffectiveInterval>
     }
     <ihr:HowMany>{max}</ihr:HowMany>
 </ihr:RetrievalCriterion>
```

The retrieval criterion and other parameters are mapped to the SOAP request. The security mechanism in the header has been omitted.
```xml
<soapenv:Envelope xmlns:ihr="http://wales.nhs.uk/ihc/ihr" xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
 <soapenv:Header>
    <!-- security header ommited in this sample -->
 </soapenv:Header>
 <soapenv:Body>
    <ihr:GetRecordRequest>
       <ihr:RequestProvenance>
          <ihr:RequestID>
             <ihr:IdValue>{random 8 digit or guid}</ihr:IdValue>
          </ihr:RequestID>
          <ihr:RequestingSystem>
             <ihr:SourceSystemID>
                <ihr:IdValue>{deviceIdentifier.value}</ihr:IdValue>
             </ihr:SourceSystemID>
             <ihr:SourceSystemType>
                <ihr:IdValue>{deviceIdentifier.display}</ihr:IdValue>
             </ihr:SourceSystemType>
             <ihr:SystemUser>
                <ihr:UserID>
                   <ihr:IdValue>{personIdentifier.value}</ihr:IdValue>
                </ihr:UserID>
             </ihr:SystemUser>
          </ihr:RequestingSystem>
          <ihr:AccessEvent>
             <ihr:EffectiveTime>{current date and time as "yyyy-MM-ddTHH:mm:ss.fffffZ"}</ihr:EffectiveTime>
             <ihr:EventType>
                <ihr:IdValue>GetRecord</ihr:IdValue>
             </ihr:EventType>
             <ihr:ResponsiblePerson>
                <ihr:UnstructuredName>{personIdentifier.display}</ihr:UnstructuredName>
             </ihr:ResponsiblePerson>
          </ihr:AccessEvent>
          <ihr:NHSnumber>
             <ihr:IdValue>{nhsNumber}</ihr:IdValue>
          </ihr:NHSnumber>
       </ihr:RequestProvenance>
            {retrievalCriterion}
        </ihr:GetRecordRequest>
    </soapenv:Body>
 </soapenv:Envelope>
```



## IHR Response

This is the response structure used to map to the FHIR output. The example contains no mappings but is here for illustration.

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Header>
        <!-- omitted for brevity-->
    </soap:Header>
    <soap:Body>
        <ihr:GetRecordResponse xmlns="urn:nhs-itk:ns:201005" xmlns:ihr="http://wales.nhs.uk/ihc/ihr">
            <ihr:ResponseProvenance>
                <ihr:RequestID/>
                <ihr:ResponseID/>
                <ihr:RespondingSystem/>
                <ihr:RespondedAt/>
                <ihr:NHSnumber/>
            </ihr:ResponseProvenance>
            <PatientExtract xmlns="http://wales.nhs.uk/ihc/ihr">
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
                <RecordSharingConsent>
                    <ClinicalCodeValue>93C0.</ClinicalCodeValue>
                    <ClinicalCodeScheme>
                        <ClinicalCodeSchemeVersion>2</ClinicalCodeSchemeVersion>
                        <ClinicalCodeSchemeId>Read</ClinicalCodeSchemeId>
                    </ClinicalCodeScheme>
                    <ClinicalCodeSelectedTerm>Consent given for upload to local shared electronic record</ClinicalCodeSelectedTerm>
                </RecordSharingConsent>
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
                    <!-- one of AllergyAdverseReaction, PrescribedAcute, PrescribedRepeat, PrescribedDiscontinued-->
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
             </PatientExtract>
        </ihr:GetRecordResponse>
    </soap:Body>
</soap:Envelope>
```



## FHIR Response

The IHR response is used to generate several [FHIR resources from IHR Records](#Bundle Entries). 

> **Note:** the FHIR request `` _includes`` parameter is used to refine which resources are returned and how references between resources are defined in the result.



**target** means ``medicationStatement`` ``Lists`` or ``allergyIntollererance`` resources and are mapped from ``RecordElement`` instances

**Reference** means resources generated to support the target resources and are mapped from ``PatientDetails`` or ``RecordElementProvenance`` An example : generating a patient resource named in a medicationStatement list.



### Bundle Structure

The outermost elements are templated to form a searchset Bundle response as follows



```json
{
    "resourceType": "Bundle",
    "meta": {
        "lastUpdated": "{current date and time as local time}",
        "tag": [
            {
                "system": "https://fhir.nhs.wales/internal/bundle/source-type",
                "code": "gp-read"
            }
        ]
    },
    "type": "searchset",
    "total": 1, // how many target resources are returned
    "link": [
        {
            "relation": "self",
            "url": "{request url}"
        }
    ],
    "entry": [
        // repeats
        {
            "fullUrl": "urn:uuid:{guid}",
            // one of patient | practitionerRole | List | MedicationStatment | allergyIntolerance
            "resource": {},
            "search": {
                // match for target resources, include for a reference and outcome for a failure
                "mode": "{match|include|outcome}"
            }
        }
     ]
}
```

### Bundle Entries

The IHR Record is used to create entries in the bundle. See the following links for defined mappings from IHR to create [Patient](ihr\patient.md), [Practitioner](ihr\practitioner.md), [PractitonerRole](ihr\practitionerRole.md), [Organization](ihr\organization.md), [MedicationStatement](ihr\medicationstatement.md), [List](ihr\list.md) and [AllergyIntolerance](ihr\allergyintolerance.md) resources.




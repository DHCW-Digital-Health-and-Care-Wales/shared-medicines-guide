# PDQ

Internally the service makes a 'search for patient' FHIR search request and expects a FHIR search response. It maps a [FHIR Request](FHIR Request) to a [PDQ XML request](PDQ Request). The [PDQ XML Response](PDQ Response) is mapped to a [FHIR Response](FHIR Response) as described here. The intent of the design is to be swappable with a similar search for patient request to CDR at a point when that is viable.



> **note:** the PDQ is called to retrieve the NHS number and GP Practice code if we need to. Demographic elements are mapped for quality and diagnostics but not used programatically nor exposed to our api consumer. 



#### FHIR OID Mapping for patient identifiers

A mapping table is used to convert between FHIR naming systems and PDQ OID values to do the patient search and to format the response.

* This is a two way conversion between the OID and naming system
* The mapping is expected to support PAS originated identifiers and the NHS number but not all systems
  * Unmatched identifers are forwarded without modification
  * multiple matches can be generated if the naming system maps to more than one OID value
* the output is a list if FHIR identifiers with their system properties as either a naming system (outbound FHIR response) or OID (inbound PDQ Request)


| OID  | Naming System                                          |
| :--- | :----------------------------------------------------- |
| NHS  | https://fhir.nhs.uk/Id/nhs-number                      |
| 103  | https://fhir.sbuhb.nhs.wales/Id/pas-identifier         |
| 108  | https://fhir.sbuhb.nhs.wales/Id/pas-identifier         |
| 109  | https://fhir.bcuhb.nhs.wales/Id/central-pas-identifier |
| 110  | https://fhir.bcuhb.nhs.wales/Id/east-pas-identifier    |
| 111  | https://fhir.bcuhb.nhs.wales/Id/west-pas-identifier    |
| 131  | https://fhir.vunhst.nhs.wales/Id/pas-identifier        |
| 310  | https://fhir.vunhst.nhs.wales/Id/pas-identifier        |
| 182  | https://fhir.vunhst.nhs.wales/Id/canisc-identifier     |
| 132  | https://fhir.abuhb.nhs.wales/Id/pas-identifier         |
| 140  | https://fhir.cavuhb.nhs.wales/Id/pas-identifier        |
| 149  | https://fhir.hduhb.nhs.wales/Id/pas-identifier         |
| 170  | https://fhir.pthb.nhs.wales/Id/pas-identifier          |
| 126  | https://fhir.ctmuhb.nhs.wales/Id/pas-identifier        |
| 154  | https://fhir.nhs.wales/Id/lims-identifier              |



## FHIR Request

```Patient?identifier={system|value}&Identifier={system|value}```

Each identifier is provided by the consumer in a 'system|value' FHIR Token format as a query string parameter.  

The system elements of the identifiers are mapped to OIDs using the mapping table and used to form a PDQ request.




### PDQ Request

This is an extract from the larger payload showing the relevant mapping from the FHIR request 

```xml
<QPD>
    <QPD.1>
        <CE.1>IHR PDQ Query</CE.1>
    </QPD.1>
    <QPD.2>PatientQuery</QPD.2>
    <!-- QPD.3 repeats for each identifier-->
    <QPD.3>
        <QIP.1>@PID.3.1</QIP.1>
        <QIP.2>{identifier.Value}</QIP.2>
    </QPD.3>
    <QPD.3>
        <QIP.1>@PID.3.4</QIP.1>
        <QIP.2>{identifier.System}</QIP.2>
    </QPD.3>
    <QPD.3>
        <QIP.1>@PID.3.5</QIP.1>
        <QIP.2>{if identifier.System = "NHS" then "NH", if "100" then "PE", else "PI"}</QIP.2>
    </QPD.3> 
</QPD>"
```





### PDQ Response

An example response from a PDQ request, which is used to contruct the FHIR response. This partial example contains no mappings but illustrates the structured PDQ response. 

```xml
<!-- this element repeats for each match -->
<RSP_K21.QUERY_RESPONSE>
    <PID>
        <PID.1>001</PID.1>
        <PID.3>
            <!-- an enterprise id example -->
            <CX.1>28895466</CX.1>
            <CX.4>
                <HD.1>100</HD.1>
            </CX.4>
            <CX.5>PE</CX.5>
        </PID.3>
        <PID.3 >
            <!-- a WDS ID example -->
            <CX.1 >6703468</CX.1>
            <CX.4 >
                <HD.1 >129</HD.1>
            </CX.4>
            <CX.5 >PI</CX.5>
        </PID.3>
        <PID.3>
            <!-- a swansea bay PAS example -->
            <CX.1>NN046351</CX.1>
            <CX.4>
                <HD.1>108</HD.1>
            </CX.4>
            <CX.5>PI</CX.5>
        </PID.3>
        <PID.3>
            <!-- NHS number example -->
            <CX.1>1000000001</CX.1>
            <CX.4>
                <HD.1>NHS</HD.1>
            </CX.4>
            <CX.5>NH</CX.5>
        </PID.3>
        <PID.5>
            <XPN.1>
                <FN.1>PATIENT</FN.1>
            </XPN.1>
            <XPN.2>No Nhs Number</XPN.2>
            <XPN.5>Mr.</XPN.5>
            <XPN.7>U</XPN.7>
        </PID.5>
        <PID.7>
            <TS.1>19850914000000</TS.1>
        </PID.7>
        <PID.8>M</PID.8>
        <PID.9>
            <XPN.7>A</XPN.7>
        </PID.9>
        <PID.11>
            <XAD.1>
                <SAD.1>3 Prop Name Example Street</SAD.1>
            </XAD.1>
            <XAD.2>Example Locality</XAD.2>
            <XAD.3>Example Town City</XAD.3>
            <XAD.5>CF03 3EF</XAD.5>
            <XAD.7>H</XAD.7>
        </PID.11>
        <PID.13>
            <XTN.1>01234567890</XTN.1>
            <XTN.2>PRN</XTN.2>
            <XTN.4>anyemail@domain.com</XTN.4>
        </PID.13>
        <PID.13>
            <XTN.1>07987654321</XTN.1>
            <XTN.2>PRS</XTN.2>
        </PID.13>
        <PID.14>
            <XTN.1>1234561</XTN.1>
            <XTN.2>WPN</XTN.2>
        </PID.14>
        <PID.15>
            <CE.1>ENG</CE.1>
        </PID.15>
        <PID.16>
            <CE.1>S</CE.1>
        </PID.16>
        <PID.17>
            <CE.1>CHR</CE.1>
        </PID.17>
        <PID.22>
            <CE.1>A</CE.1>
        </PID.22>
        <PID.24>X</PID.24>
        <PID.28>
            <CE.1>ENG</CE.1>
        </PID.28>
        <PID.32>01</PID.32>
    </PID>
    <PD1>
        <PD1.3>
            <XON.3>W00020</XON.3>
        </PD1.3>
        <PD1.4>
            <XCN.1>G612345</XCN.1>
        </PD1.4>
    </PD1>
</RSP_K21.QUERY_RESPONSE>
```



## FHIR Response

A FHIR searchset response is expected, so the PDQ response is mapped back to FHIR. The ordering in the source PDQ response is not changed when its mapped to FHIR. The OID values from PDQ are mapped to the relevant Naming System using the mapping described above. 

* if the patient match has a WDS ID (129 OID) in the PDQ result then this is considered a strong enough indicator of quality to add the nhs number traced status extension to the response when an NHS number (NH) is present.

  

```json
{
	"type":"searchset",
    "total": 0, // set to total matches found
    "entry":[
        // each RSP_K21.QUERY_RESPONSE
    	{
        "resource":
            {
    		"resourceType":"Patient",
    		"identifier":
                [
                	// tracedAndVerified if PID.3[*].4.1 = 129 exists 
                    // each PID.3
                	{         
                    	// include extension if PID.3.5 = "NH" and tracedAndVerified 
                    	"extension":
                    	{
                        	"url": "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-NHSNumberVerificationStatus",
                        	"coding":[
                            	{
                                	"system":"https://fhir.hl7.org.uk/CodeSystem/UKCore-NHSNumberVerificationStatusWales",
                                	"code":"01",
                                	"value":"number present & traced"
                            	}
                        	],
                    	},
                        "system":"{map PID.3.4.1 (OID) to Naming System}",
                        "value":"PID.3.1"
                }
            ], 
			"name":
                [
                    {
                    "given": ["PID.5.2", "PID.5.3[*]"],
                    "family": "PID.5.1",
                    "prefix": ["PID.5.5[*]"],
                    "suffix": ["PID.5.4[*]"],
                    "use": "usual"
                    }
            	], 
			"address":[
                // each PID.11
                {
                    "lines":[
                        "PID.11.1.1",
                        "PID.11.2",
                        "PID.11.3",
                        "PID.11.4",
                    ],
                    "postalCode":"PID.11.5",
                    "use":{if PID.11.7="H" then "home"}
                }
            ], 
            "birthDate":"PID.7",
			"generalPractitioner":
                {
            	"resource":
                    {
                        "type":"Organization",
                        "reference":"Organization/{PD1.3.3}",
                        "identifier":
                        	{
                    		"system" :"https://fhir.nhs.uk/Id/ods-organization-code",
                            "code":"PD1.3.3"
                    		}
        			}
                }
			},
        "search":
         	{
            "mode":"match",
        	"score":{if PID.32="01" then 1.0}  
            }
        }
		
    ]
}
```


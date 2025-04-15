# WRDS

Internally the service makes a 'search for organization endpoint' FHIR search request and expects a FHIR search response. It maps a [FHIR Request](#fhir-request) to a [WRDS GPPractice Lookup Request](#wrds-gppractice-lookup-request). The [WRDS Response](#wrds-response) is mapped to a [FHIR Response](#fhir-response) as described here. 

Ultimately this process is a service locator pattern.  We are trying to determine which GP service provider holds the patients records. This is achieved by discovering which provider is active at a specific GP practice. We are emulating an Endpoint search and allocating details using environment specific config. We use the config to construct two endpoints. We select the endpoint using the WRDS response.

```c#
[
    Endpoint { 
    	Name : configuration["data:services:ihr:inps:endpoint:name"], 
    	Address : configuration["data:services:ihr:inps:endpoint:address"] 
    },
	Endpoint { 
    	Name : configuration["data:services:ihr:emis:endpoint:name"], 
    	Address : configuration["data:services:ihr:emis:endpoint:address"] 
	}
]
```



> **note:** this implementation assumes a future where FHIR reference data has the capacity to return Endpoint resources managed by an Organization. FHIR reference data is expected to come from the CDR FHIR server. In this future the CDR may represent this relationship using other resources, possibly via a Device resource.



## FHIR Request

Prior to this the patient resource has been constructed. Their GP Practice Code is allocated to the GeneralPractitioner property.

```Endpoint?managingOrganization.identifier={system|value}```

the identifier is an ODS identifier:

* system: `` https://fhir.nhs.uk/Id/ods-organization-code`` 
* value: {patient.GeneralPractitioner[0].Identifier.value}.



### WRDS GPPractice Lookup Request

The managingOrganization parameter is used to construct the lookup request. We only need the value part of the identifier

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:mes="http://www.wales.nhs.uk/namespaces/MessageRelease2">
  <soapenv:Header />
  <soapenv:Body>
    <mes:GetResultSetRequest>
      <mes:LookupTable>
        <mes:Name>GPpracticeLookup</mes:Name>
        <mes:Id></mes:Id>
        <mes:Namespace>http://www.wales.nhs.uk/namespaces/NSD/IHR/PLS</mes:Namespace>
      </mes:LookupTable>
      <mes:AttributeToRetrieve>
        <mes:Name>GPpracticeSystemSupplierCode</mes:Name>
        <mes:Namespace>http://www.wales.nhs.uk/namespaces/NSD/IHR/PLS</mes:Namespace>
      </mes:AttributeToRetrieve>

      <mes:AttributeValuePair>
        <mes:Attribute>
          <mes:Id></mes:Id>
          <mes:Name>GPPracticeCode</mes:Name>
          <mes:Namespace>http://www.wales.nhs.uk/namespaces/NSD/IHR/PLS</mes:Namespace>
        </mes:Attribute>
        <mes:Value>{identifierValue}</mes:Value>
        <mes:ExactMatch>0</mes:ExactMatch>
      </mes:AttributeValuePair>
    </mes:GetResultSetRequest>
  </soapenv:Body>
</soapenv:Envelope>
```

## WRDS Response

The Supplier is taken from the ``GPpracticeSystemSupplierCode`` attribute in the response and used to select one of the configured endpoints. ``NEWINPS`` or ``MICROTEST`` select one supplier, ``NEWEMIS`` selects the other.



## FHIR Response

The selected endpoint is returned as a FHIR searchSet response. The original organization used to query WRDS is added back as the managing organization of the endpoint

```json
{
	"type":"searchset",
    "total": 1, // set to total matches found
    "entry":[
        // the selected endpoint
    	{
        "resource":
            {
    		"resourceType":"Endpoint",
            "name":"{from config}",
            "address": "{from config}",
            "managingOrganization":
                {
                    "type": "Organization",
                    "reference": "Organization/{identifierValue}",
                    "Identifier":
                    {
                        "system": "https://fhir.nhs.uk/Id/ods-organization-code",
                        "value":"{identifierValue}"
                    }
                }
                
            },
        "search":
         	{
            "mode":"match", 
            }
        }  
```


## Invoke SMR GP API (read) 

> See also: [find patient by identifiers]() | [find gp supplier]() | [get GP records as FHIR]()

This process is invoked from the main SMR API entry point and is not directly exposed to api consumers. The consumer parameters are inspected and further actions follow to get the GP data in FHIR format.

```mermaid
flowchart LR
 

    TRIGGER(["request data <br>from GP API <br>with FhirClient"]) 
    S1["validate <br>parameters"]
    S1A{"valid?"}
    S2{"valid for <br>GP request?"}
    S3{"NHS number <br>& GP code <br>provided?"} 
    S4[[find Patient <br>by identifiers]]
    S4A{"OK?"}
    S5[[find GP supplier]]
    S5A{"OK?"}
    S6[[get gp records <br>as FHIR]]
    S6A{"OK?"}
    E4A[bundle with data]
    E4B[bundle with operationOutcome]
    E4C[operationOutcome]
    E4D[empty bundle]

    SUCCESS(["result returned"])
    LOGERROR["log error"]
    LOGWARNING["log warning"]


TRIGGER --> S1
S1 ---> S1A
S1A -- yes ---> S2
S1A -- no ---> E4C
S2 -- no ---> E4D
S2 -- yes ---> S3
S3 -- no ---> S4
S3 -- yes ---> S6

S4 ---> S4A
S4A -- no ---> E4B
S4A -- yes --->  S5 

S5 ---> S5A
S5A -- no ---> E4B
S5A -- yes ---> S6

S6 ---> S6A
S6A -- no ---> E4B
S6A -- yes ---> E4A


E4A --> LOGINFO
E4C --> LOGERROR
E4B --> LOGWARNING

E4D --> SUCCESS

LOGERROR --> SUCCESS    
LOGWARNING --> SUCCESS
LOGINFO --> SUCCESS

style TRIGGER fill:#0085FC
style LOGERROR fill:red, color:#000
style LOGWARNING fill:#ff9900, color:#000
style LOGINFO fill:#efefef, color:#000
style SUCCESS fill: green

```


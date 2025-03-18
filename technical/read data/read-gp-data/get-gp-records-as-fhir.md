## sub-process: get gp records as FHIR

> see also: [FHIR mapping](map-to-fhir/index.md)

The GP supplier endpoint is invoke with the NHS number and the results are convered to FHIR resources. Note that the *20 years* noted allows for the approximate period that the supplier software was brought into service.

```mermaid
flowchart LR
 

    TRIGGER(["get gp records as FHIR"]) 
    S1["add <br>NHS number <br>to request"]
    S2["min date is <br>two years ago"]
    S3{"request type?"}

    M1["add repeat,acute and discontinued parameters"]
    M2{"has date<br>parameters?"}
    M3["set min date to to param date"]

    A1["add allergyIntolerance parameter"]
    A2["set min date to twenty years ago"]
        
    S4["Send request <br>to <br>IHR3 GetRecord <br>Endpoint"]
    S5{"OK?"}
    S6["Map response <br>to <br>FHIR"]
    END(["stop"])
    ABORT(["invalid request"])
    SUCCESS(["result returned"])
   
TRIGGER --> S1
S1 --> S2
S2 --> S3
S3 -- medication --> M1
S3 -- allergy --> A1 
M1 --> M2  
A1 --> A2
A2 --> S4
M2 -- yes --> M3 
M2 -- no --> S4  
M3 --> S4 
S4 --> S5 
S5 -- yes --> S6 
S5 -- no --> ABORT 
S6 --> SUCCESS




style TRIGGER fill:#0085FC
style END fill:#ff9900, color:#000
style ABORT fill:#ff9900, color:#000
style SUCCESS fill: green
```








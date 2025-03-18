## read data from SMR API

> see also:  [security](security.md) | [audit](audit.md) | [Invoke SMR API (read)](invocation.md)

As an overview, the pattern for calling the API involves speaking to an API gateway/management system. For our purposes, the gateway handles authentication and audit.

```mermaid
flowchart 
 

    TRIGGER(["consumer wants to <br>read data"]) 
    S1{"are they <br>onboarded?"}
    S2{"are they <br>authenticated?"}
    ABORT(["process terminates"])
    S2A[["consumer authenticates with API Management"]]
    S2B{"API access <br>permitted?"}
    S3[["invoke SMR API read"]]
    S4{"server error <br>result?"}
    S4A[["API Management Audits result"]]
    S4B["API Management <br>intercepts response"]
    SUCCESS(["process ends"])
   
TRIGGER --> S1
S1 -- No ---> ABORT
S1 -- Yes ---> S2
S2 -- No ---> S2A
S2 -- Yes ---> S2B
S2A --> S2
S2B -- No --> ABORT
S2B -- Yes--> S3
S3 --> S4
S4 -- No --> S4A
S4 -- Yes --> S4B
S4B --> SUCCESS
S4A --> SUCCESS

style TRIGGER fill:#0085FC
style ABORT fill:#ff9900, color:#000
style SUCCESS fill: green
```


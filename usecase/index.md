
# Use cases

### [View medicines history record for a patient](view-medicines-history/index.md)
A user can view a medicines history record for a clear overview of current and previous medines activity

### [Update medicines history record for a patient](update-medicines-history/index.md)
A user can add a known risk so that others can understand it




## Understanding Wireframes
Our wireframes are not prescriptive. Wireframes are intended to provide design guidance, where needed, to help users complete tasks. Quality measures, if you need to assess wireframes, should consider information architecture and user experience.  Colours and the layout are indicative of visual priority. Wireframes are useful starting points for designers and user centred design activities. They are not examples of production-ready outputs.

## Background: all use cases

The following are common to all use cases and examples

### Common assumptions

* a medicines history record is available in FHIR R4

* the system can read and write to the medicines history record in FHIR R4

  

### Comon definitions & abbreviations

* the system: a digital tool with medicines management functionality
  
* the user: a person with accces to the system
  
* HCW: Health Care worker

* FHIR: Fast Healthcare Interoperability Resource. A healthcare data exchange framework from HL7 international for the modern web

  




## Flow diagrams

We use flow diagrams to describe steps taken between the start and end of process. Some key elements are shown here and, where appropriate. how these relate to FHIR resources.

| element                    | description                                                  | FHIR property path                      |
| -------------------------- | ------------------------------------------------------------ | --------------------------------------- |
| **bold** terms             | describes a data element, normally a property of the FHIR resource, expanded for readability. | n/a                                     |
| [ a \| b \| c ]            | Allowed values for a FHIR resource property                  | n/a                                     |
| **add record**             | This sub process is responsible for populating a new FHIR allergyIntollerance resource | . (allergyIntollerance)                 |
| >  **category**            | A text property with restricted values. <br />FHIR allergyIntollerance resource can have several categories at the same time | .category[x]                            |
| >  **clinical status**     | A coded property                                             | .clinicalStatus                         |
| >  **verification status** | A coded property                                             | .verificationStatus                     |
| >  **criticality**         | A text property with restricted values.                      | .criticality                            |
| >  **substance**           | A coded property. SHOULD be DM+D or SNOMED coded, MUST have text equivalent when not coded | .code  -                                |
| >  **note**                | FHIR allergyIntollerance resource can have several notes. Each note can have formatted or plain text, an author and a date | .note[x]                                |
| > **add reaction event**     | This sub process is responsible for populating a reaction event within a FHIR allergyIntollerance resource. A resource can have several reaction events. | .reaction[x]                            |
| > > **event severity**      | A text property with restricted values                       | .reaction[x].severity                   |
| > > **event onset**         | a date or partial date (in UTC format)                       | .reaction[x].onSet                      |
| > > **event symptoms**      | A coded property. SHOULD be SNOMED coded, MUST have text equivalent when not coded. A reaction can have several manifestations. | .reaction[x].manifestation[y]           |
| > > **event substance**     | A coded property. SHOULD be DM+D or SNOMED coded, MUST have text equivalent when not coded. MUST ONLY be populated when the known product or substance in the event differs from the main | .reaction[x].manifestation[y].substance |
| > > **event note**          | Can have several notes. Each note can have formatted or plain text, an author and a date | .reaction[x].manifestation[y].note[z]   |


# Questions & Answers

## If a GP prescribes a medicine for an acute treatment, how is this represented in the record?
Lets assume the following values:  
* a Virtual Medicinal Product (VMP):
  * 'Co-amoxiclav 250mg/125mg dispersible tablets sugar free' (DMD: 39021311000001109) 
* or a Virtual Medicinal Product Pack (VMPP):
  'Co-amoxiclav 250mg/125mg dispersible tablets sugar free 21 tablet' (DMD: 1069211000001101)
* 7 days supply - 21 tablets
* 1 tablet 3 times a day
* recorded by Dr Alison Bradley (GMP: 123456) at Cwm Cymru Practice (ODS: W12345)
* for Mr Alan Brown (NHS: 1234567890)
* recorded and issued on Tuesday 15th April 2025
* we look at the record on the 17th April 2025

### Unless you ask to see all available details, the item will be only be summarised as an entry in a list.
With the list, we can see some information of :
* the source (the GP)
* the subject(the patient with their trusted NHS number)
* the medication (it's text display, status and dose instruction)
* the date of the list- which is always the most recent activity date and not the date of the request
```javascript
{
// the title format is source, content and most recent activity date
  title: "GP Record Medications 2025-04-15",
// the patient has their name as text and their NHS number
  subject: {
    identifier: {
      system: "https://fhir.nhs.uk/Id/nhs-number",
      value: "1234567890",
      extension:
        {
          url: "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-NHSNumberVerificationStatus",
          coding: [
            {
              system: "https://fhir.hl7.org.uk/CodeSystem/UKCore-NHSNumberVerificationStatusWales",
              code: "01",
              value: "number present & traced"
            }
          ],
        },
    display: "Mr Alan Brown (NHS: 1234567890)"
  },
// the GP and their practice with any official codes
  source:{
   display: "Dr Alison Bradley (GMP: 123456) at Cwm Cymru Practice (ODS: W12345)"
  },
  entry: [
    // the medication with its status and dose instruction
    {
      item: {
        display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free - active - 1 tablet 3 times a day"
      }
    }
    // and any other entries from the gp record
  ]
}
```
### If you ask to see all available details, the medication, the request, the patient and information about the recorder can also be returned. 
The list is as above, but the item now points to the detail in a medicationStatement. Other properties expand in the same way, for the patient and Practice.
If we focus on the medicationStatement, we can see some further information about the following:
* coding of the medicine
* acute or repeat 
* status
* category
* effective date
* asserted date
* quantity supplied
* information source (the GP practice)
```javascript
{
// the title format is source, content and most recent activity date
  title: "GP Record Medications 2025-04-15",
// the patient has their name and their NHS number as text. More details are in the reference (not shown)
  subject: {
    reference: "#patient-example",
    display: "Mr Alan Brown (NHS: 1234567890)"
  },
// the GP and their practice with any official codes. More details are in the reference (not shown)
  source:{
   reference: "#practitioner-role-example",
   display: "Dr Alison Bradley (GMP: 123456) at Cwm Cymru Practice (ODS: W12345)"
  },
  entry: [
    // the medication with its status and dose instruction plus more details in the reference (see below)
    {
      item: {
        reference: "#acute-statement-example",
        display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free - active - 1 tablet 3 times a day"
      }
    }
    // and any other entries from the gp record
  ]
},
// the medication statement
{
    id: "acute-statement-example",
    // this describes the order (prescription) that the statement was derived from
    contained: [
        {
            resourceType: "MedicationRequest",
            id: "1",
            status: "completed",
            intent: "order",
            medicationCodeableConcept: {
                coding: [
                    {
                        system: "https://dmd.nhs.uk",
                        code: "39021311000001109",
                        display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free"
                    }
                ],
                text: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free"
            },
            subject: {
              reference: "#patient-example",
              display: "Mr Alan Brown (NHS: 1234567890)"
            },
            requester: {
                type: "Organization",
                identifier: {
                    use: "official",
                    system: "https://fhir.nhs.uk/Id/ods-organization-code",
                    value: "W00010"
                },
                display: "Synthetic General Practice B (ODS: W00010)"
            },
            courseOfTherapyType: {
                coding: [
                    {
                        system: "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy",
                        code: "acute",
                        display: "Short course (acute) therapy"
                    }
                ]
            },
            dispenseRequest: {
                quantity: {
                    value: 21,
                    unit: "tablet",
                    code: "tablet"
                }
            }
        }
    ],
    extension: [
        {
            url: "https://fhir.nhs.wales/StructureDefinition/Extension-DataStandardsWales-MedicationCourseOfTherapyType",
            valueCodeableConcept: {
                coding: [
                    {
                        system: "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy",
                        code: "acute",
                        display: "Short course (acute) therapy"
                    }
                ]
            }
        }
    ],
    identifier: [
        {
            system: "https://fhir.nhs.wales/Id/wgpr-source-identifier",
            value: "4642"
        }
    ],
    status: "active",
    category: {
        coding: [
            {
                system: "http://terminology.hl7.org/CodeSystem/medication-statement-category",
                code: "community",
                display: "Community"
            }
        ]
    },
    medicationCodeableConcept: {
        coding: [
            {
                system: "https://dmd.nhs.uk",
                code: "39021311000001109",
                display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free"
            }
        ],
        text: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free"
    },
    subject: {
       reference: "#patient-example",
       display: "Mr Alan Brown (NHS: 1234567890)"
    },
    effectiveDateTime: "2024-10-24",
    dateAsserted: "2024-10-24T08:46:30.0000000+01:00",
    informationSource: {
        type: "Organization",
        identifier: {
            use: "official",
            system: "https://fhir.nhs.uk/Id/ods-organization-code",
            value: "W12345"
        },
        display: "Cwm Cymru Practice (ODS: W12345)"
    },
    derivedFrom: [
        {
            reference: "#1"
        }
    ],
    dosage: [
        {
            text: "1 tablet 3 times a day"
        }
    ]
},
```

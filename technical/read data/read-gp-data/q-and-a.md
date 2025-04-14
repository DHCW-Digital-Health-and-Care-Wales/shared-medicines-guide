# Questions & Answers

## Q: If a GP prescribes a medicine for an acute treatment, how is this represented in the record?
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

### A: Unless you ask to see all available details, the item will be only be summarised as an entry in a list.
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
### A: If you ask to see all available details, the medication, the request, the patient and information about the recorder can also be returned. 
The list is shown as as the previous example. However, the item now points to the detail in a medicationStatement. Other properties expand in the same way, for the patient and practice.
If we focus on the medicationStatement, we can see some further information about the following:
* coding of the medicine
* acute or repeat 
* status
* category
* effective date
* asserted date

* information source (the GP practice)

In addition, the 'contained' property holds a medicationRequest, which is how prescriptions/orders are represented in FHIR. Several properties of the request echo those in the statement (medication, source, patient). Requests like these are always presented with a status echoing that of the statement and the intent is always order. Requests have a courseOfTherapyType property, which indicates acute or repeat(continuous). We have an extention in the statement that echos this. On the request, we can see:
* quantity supplied
* acute or repeat

Unfortunately, we are unable to show detailed dispense activity from this source.
```javascript
// firstly the list again:
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
// the reference points to the following resource using its id property
        reference: "#acute-statement-example",
        display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free - active - 1 tablet 3 times a day"
      }
    }
// and any other entries from the gp record
  ]
},
// followed by the medication statement
{
    id: "acute-statement-example",
// this describes the order (prescription) that the statement was derived from
    contained: [
        {
            resourceType: "MedicationRequest",
            id: "1",
            status: "completed",
            intent: "order",
// echos the medication statement
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
// echos the medication statement
            subject: {
              reference: "#patient-example",
              display: "Mr Alan Brown (NHS: 1234567890)"
            },
// echos the medication statement
            requester: {
                type: "Organization",
                identifier: {
                    use: "official",
                    system: "https://fhir.nhs.uk/Id/ods-organization-code",
                    value: "W00010"
                },
                display: "Synthetic General Practice B (ODS: W00010)"
            },
// acute or repeat(continuous)
            courseOfTherapyType: {
                coding: [
                    {
                        system: "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy",
                        code: "acute",
                        display: "Short course (acute) therapy"
                    }
                ]
            },
// quantity ordered
            dispenseRequest: {
                quantity: {
                    value: 21,
                    unit: "tablet",
                    code: "tablet"
                }
            }
        }
    ],
// echos the medication request courseOfTherapyType property
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
// id from the source record
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
    effectiveDateTime: "2025-04-15",
    dateAsserted: "2025-04-15T10:46:30.0000000+01:00",
    informationSource: {
        type: "Organization",
        identifier: {
            use: "official",
            system: "https://fhir.nhs.uk/Id/ods-organization-code",
            value: "W12345"
        },
        display: "Cwm Cymru Practice (ODS: W12345)"
    },
// the link to the contained request
    derivedFrom: [
        {
            reference: "#1"
        }
    ],
// dosage detail- including additional instruction where provided
    dosage: [
        {
            text: "1 tablet 3 times a day"
        }
    ]
},
```
## Q: If a GP prescribes a medicine for an repeat treatment, how is this represented in the record?
### A: Unless you ask to see all available details, the item will be summarised as an entry in a list in exactly the same way as an acute.
There is no differciation between acute or repeat medications in the list format.
### A: If you ask to see all available details, the response is similar to an acute. 
The overall strcture is identical but the course of therapy type changes and some further information about the repeat is shown in the contained request:
* repeats issued
* repeats authorised
* last issue date

 The following leaves out any unchanged properties from the acute exmaple so you can focus on the differences.
 
```javascript
// the same medication statement as a repeat with just the changes shown:
{
// this describes the order (prescription) that the statement was derived from
    contained: [
        {
// other properties omitted...            
// acute or repeat(continuous)
            courseOfTherapyType: {
                coding: [
                    {
                        system: "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy",
                        code: "continuous",
                        display: "Continuous long term therapy"
                    }
                ]
            },
// how many repeats issued (if issued at all)
            extension: [
               {
                   url: "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-MedicationRepeatInformation",
                   extension: [
                       {
                           url: "numberOfPrescriptionsIssued",
                           valueUnsignedInt: 1
                       }
                   ]
               }
          ],
            dispenseRequest: {
// how many repeats authorized
                numberOfRepeatsAllowed: 3,
                quantity: {
                    value: 21,
                    unit: "tablet",
                    code: "tablet"
                }
            }
        }
    ],
// echos the medication request courseOfTherapyType property
    extension: [
        {
            url: "https://fhir.nhs.wales/StructureDefinition/Extension-DataStandardsWales-MedicationCourseOfTherapyType",
            valueCodeableConcept: {
                coding: [
                    {
                        system: "http://terminology.hl7.org/CodeSystem/medicationrequest-course-of-therapy",
                        code: "continuous",
                        display: "Continuous long term therapy"
                    }
                ]
            }
        },
// when the repeat was last issued (if issued)
        {
            url: "https://fhir.hl7.org.uk/StructureDefinition/Extension-UKCore-MedicationStatementLastIssueDate",
            valueDateTime: "2025-04-14"
        }
    ],
}
```

## Q: What would need to happen for an item on the GP record to have a completed status?

### A: There a a few situations that can result in the GP system automatically changing the status of a prescription (after a set period of time) which results in a completed status in the record:

* An acute has run its course 
* An acute has been cancelled
* an entire repeat course (this means all issues) has been cancelled

Note that a peiod of time will need to pass before this change to completed is enacted. In the itnerim period the status may be shown as 'unknown'.

## Q: What would need to happen for an item on the GP record to have an unknown status?

### A: The status is unknown when the GP system has produced a 'not issued' status. 
This is normally temporary following a cancel (see previous) or if the prescription has been post dated. Because these are two very different activities in terms of intent, the status is set to unknown.

## Q: If a GP prescribes a medicine for an acute treatment, and then moves this to a repeat, how is this represented in the record?

### A: There are two outcomes, depending on the activity by the GP.

* if they modify the acute medication and simply switch the type to repeat then there will be one item returned (repeat, and potentially active)
* if they consider the acute as complete and decide to add an new prescription to cover the repeat then there will be two items returned- one acute (complete) and on repeat (potentially active)


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
// the date (most recent activity of any entry)
  date: "2025-04-15T10:46:30.0000000+01:00",
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
  date: "2025-04-15T10:46:30.0000000+01:00",
  entry: [
// the medication with its status and dose instruction plus more details in the reference (see below)
    {
      item: {
// this time the reference point us to the next resource using its id property
        reference: "#acute-statement-example",
        display: "Co-amoxiclav 250mg/125mg dispersible tablets sugar free - active - 1 tablet 3 times a day"
      }
    }
// and any other entries from the gp record
  ]
},

// the next resource: a medication statement
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
// echos the medication statement - the reference would show more detail (not shown)
            subject: {
              reference: "#patient-example",
              display: "Mr Alan Brown (NHS: 1234567890)"
            },
// echos the medication statement - the reference would show more detail (not shown)
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
// the reference would show more detail (not shown)
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
// the link to the contained property above, indicating this is derived from that request 
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
## Q: Why is the medication request shown in the contained property of the statement, can we get this by itself?
### A: The GP record information available to us doesnt explicitly state who prescribed the medication. 

The medicationRequest profile, which describes how this particular data model should be used,  has this property as required. As the GP record often contains information from other parties (community pharmacist, hospital consultant etc) it would be inaccurate to assume the GP as the prescriber. As this means the resource cannot (and should not) stand alone its referenced as a contained resource so that quantity and repeat information can be carried.

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

## Q: Is dosage.additionalInstruction populated from the pharmacy Instructions in the clinical system? Or from the patient instructions?

### A: Additional Instructions as recorded against the medication in the GP record are recorded here. 
There is no distinction of patient or pharmacy instruction in that data source. Data supplied from other sources may wish to support this differentiation by populating the patientInstruction field as appropriate.

## Q: Is there are clinical risks in returning just the medication list without the details?.

For example if a med had been stopped because someone had an allergic reaction and you just return the name and dosage of the medication but not the fact that it had been stopped or the allergies a clinician may think they are currently taking this medication.

### A: The description of the medication in list format includes the status and status reason where appropriate but is not recomended for prescribing scenarios.

The API specification recommends that, in a prescribing scenario, consumers retrieve all available data and not just the list.  If a consumer goes against the recomendation then they can see the status and dose instructions for active medications or status and status reason in all other cases. There may not always be a status reason - this depends on how each individual uses the GP software for example.

We considered using the date property of the list entry but considered it a misuse in this case. Additionally medications can have several dates and this may have led to misinterpretation.

## Q: There is a date filter in the API. how does this work? Do I get prescribing activity? Whats the cut-off?

### A1: You are filtering list by date, not medication dates specifically.

The list date indicates when the list was created or changed. This may be a clinical date (eg date of discharge) or an activity date (eg the date list was modified post discharge for a correction). 

The date filter exists to allow consumers to filter for lists sourced from other systems by date. for example - medications at discharge occurring between 8am Friday and 5pm on the following Monday.

The list from the GP record follows the same pattern. Its the most recent medication activity (prescribed, recorded, issued) of any of its entries, echoing the created or changed pattern for persisted lists.

By default (with no dates used) the system will return medications for the prior two years from the GP record and all other data from other sources. With a date filter applied, the GP list will only return items with an activity during that time and the list will be dated to reflect that.

### A2: You get medications with activity in that date range.
This activity may be the recording, updating, or prescribing of medications during that period not prescribing activity alone.

### A2: Activity would need to occur within the date range for it to be returned.
As an example, if a months supply was prescribed two days prior to a date range and no other activity occurred for it during the date range then this medication is unlikely to be shown. We set the GP default to be two years to cover the widest definition of current, but there will be situations where medicines fall outside of this window over time.

## Q: Why are the NHS Wales FHIR profiles? Shouldnt we all be using UKCore profiles?

### A: The Wales FHIR profiles support the regulatory, reporting and system requirements for healthcare data exchange for organizations in NHS Wales.

Coding systems may differ, naming systems differ and so profiling is needed to capture these variants. The Wales profiles exist to standardise healthcare data exchange in Wales. This is a similar goal to those of NHS Digital (England) and UKCore. The Wales profiles use UK Core as their base definition and will obviously evolve over time, just as UK Core and NHS Digital (England) will. We intend to keep our implementation as close as possible to the other home nations although some drift is expected as each standard improves and goes through its ballot process.

## Q: Why is there a 'course of therapy type' extention on the medication statement when its fully available on the medication request? What about compatiability with other home nations on this?
### A: Its a convenience extention to allow the use of the statement without the request and still know about repeat or acute activity.
This isnt mandated (extentions are never mandated) but if systems supply medicines information to the record without being able to construct a full request themselves, then this can be used to carry this important information. Compatability with NHS England should really use the dispense and request resources- the statements are simple summaries. In practice, EPS will provide a great deal of compatibility for medications when the roll out completes.

## Q: How do the Ids work? If I give you an Id, do you keep it?
### A: You give us the ids you want us to keep as identifiers on resources. We control the id property.

As a basic rule, we dont allow resources to have their id properties allocated by consuming systems. The only supplier of this ('resource id') is the SMR. We encourage the use of identifiers ('business identifiers') to record the logical ids used in consuming systems.

```javascript
{
// the id we provide ('resource Id')
 id: "123456",
// the ids you and others provide ('business identiifers')
 identifier:  [
 {
  system: ""
  value: ""
 },
// repeats
]
```

**Resource Id** 

You can expect supplied resource Ids to be immutable but be aware that resources may not always have have a resource Id. We use the'fullurl' property in bundle resources to resolve references for this reason- resources dont need ids for this to work.

You should not populate the id property with your own internal value for the resource. Use a business identifier for this.

**Business identifiers**

The FHIR resource identifier property is used to carry all known logical identifiers for that resource as defined by source or consuming systems. These should be respected and transmitted by all parties to support tracing by the systems that need them. Business identifiers may also be used to determine add or update when messages are processed prior to updating the SMR.

For example, on a medication statement:
```javascript
{
 identifier: 
 [
  {
     // an identifier from the GP record
     system: "https://fhir.nhs.wales/Id/wgpr-source-identifier",
     value: "dd6a9e73-8b1e-4c62-9a66-c38ef2775467"
  },
  {
     // an identifier from the SMR API
     system: "https://fhir.nhs.wales/Id/smr-record-identifier",
     value: "5db929db-e09b-2624-67bc-7a14e90cb74d"
  }
  ,
  {
     // an identifier from an EPMA
     system: "https://fhir.cavuhb.nhs.wales/Id/epma-identifier",
     value: "7df7fdda-bf7e-46f8-87c3-9dbf219f7d93"
  }
 ]
}
```

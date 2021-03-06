_CP-PCD-PIV-140_

_Update to PIV section of TF Vol 2_

# **IHE Change Proposal**

Tracking information:

| IHE Domain | Patient Care Devices |
| --- | --- |
| Change Proposal ID: | CP-PCD-PIV-140 |
| Change Proposal Status: | Submitted |
| Date of last update: | 06/25/2019 |
| Person assigned: | Jeff Rinda |

Change Proposal Summary information:

| Update to PIV section of TF Vol 2 |
| --- |
| Submitter's Name(s) and e-mail address(es): | Jeff Rinda (jeffrey.rinda@icumed.com) |
| Submission Date: | 06/25/2019 |
| Integration Profile(s) affected: | PIV |
| Actor(s) affected: | IOP, IOC |
| IHE Technical Framework or Supplement modified: | PCD TF-2 Rev 8 (Oct 23 2018) |
| Volume(s) and Section(s) affected: | Vol 2, section 3 |
| Rationale for Change:Adding detail about supported use cases including bolus from existing infusion and multistep.
 |

_Add new section 3.3.4.4.10 to TF vol 2 beginning at line 1010. Note that the existing section 3.3.4.4.10 Expected Actions will need to be renumbered to 3.3.4.4.11._

**3.3.4.4.10 Rate change, titration, Bolus from existing infusion, and Multistep**

**3.3.4.4.10.1 Rate change or titration**

ORC-1 Order Control = "XO".

RXG-5 Give Amount-Minimum and RXG-7 Give Units must be empty.

RXG-15, 16 must contain a rate or dose rate and cannot contain a dose.

**3.3.4.4.10.2 Bolus from existing infusion**

Considerations:

- An infusion is currently programmed on the pump.
- A bolus of the same medication is ordered (i.e. there is a new order in the EHR).
- The EHR workflow provides the nurse the capability to administer the bolus from the same bag or syringe using the PIV PCD-03 transaction to send the bolus order to the pump.
- No assumption is made about the behavior of the pump once the bolus has been delivered. Depending on the pump type or model it may stop, alarm, or resume delivering the underlying infusion.

ORC segment

- ORC-1 Order Control = "CH" (change)
- ORC-2 Placer Order Number = bolus order ID (child order ID)
- ORC-8 Parent = parent order ID

A bolus order may be specified in 3 ways:

- Dose or Volume + Rate
- Dose or Volume + Duration
- Rate + Duration

The following table outlines the required and optional data for each type of bolus order.

| **Bolus order type** | **ORC-1** | **ORC-2** | **ORC-8** | **RXG-15/16** | **TQ1 segment** | **OBX segment with OBX-5 = MDCX\_INFUSION\_PROGRAM\_TYPE = clinician-dose** | **OBX segment with OBX-5 = MDC\_FLOW\_FLUID\_PUMP** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dose or volume + rate | "CH" | Bolus (child) order ID | Parent order ID | Dose or volume amount | O | R | R |
| Dose or volume + duration | "CH" | Bolus (child) order ID | Parent order ID | Dose or volume amount | R | R | O |
| Rate + duration | "CH" | Bolus (child) order ID | Parent order ID | Rate | R | R | O |

OBX segment

- Include an OBX segment specifying the order type of bolus (clinician dose) where

OBX-5 = MDCX\_INFUSION\_ORDER\_TYPE. This term has enumerations "clinician-dose", "loading-dose", and "continuous".

- When RXG-15 and RXG-16 specify a dose, include an OBX segment specifying the rate where OBX-5 = MDC\_FLOW\_FLUID\_PUMP.

When the IPEC or DEC profiles contain information about a bolus from an existing infusion, note that the PCD-01 and PCD-10 messages contain bolus information in the Clinician Dose Info section.

In the OBR segment, OBR-2 Placer Order Number contains the order ID of the bolus as a "child" order ID. OBR-29 Parent is used to contain the order ID for the parent (i.e. the existing infusion).

**3.3.4.4.10.3 Multistep**

The ordered medication and concentration must be identical in all steps.

All steps are represented in the BCMA by a single order and a single order ID.

Some pump models may support different dosing units (RXG-15 and 16) among steps (see Example 2 in the PCD TF vol 1, Section 4.4).

**PCD-03 message structure**

When used for multistep the HL7 RGV message structure used by PIV will require repetition of certain segments resulting in some duplication.

The simplified message structure for multistep is shown below.

{} indicates repetition, [] indicates optionality.

MSH

PID

[PV1]

ORC

{

RXG

[TQ1]

RXR

OBX (multiple)

}

Each step must contain:

- Step number in RXG-1 (values are 1...n)
- An OBX segment to indicate the type of the current step
  - OBX-3 = MDCX\_INFUS\_ORDER\_STEP\_TYPE
  - enumerations are "loading dose" or "continuous"
  - "loading dose" is optional and supported in step 1 only
- An OBX segment to indicate the total number of steps in the program
  - OBX-3 = MDCX\_INFUS\_TOTAL\_NUM\_STEPS
- Additional OBX segments containing the pump ID or patient parameters (e.g. height, weight, BSA) as required

The following table applies to how data is provided in PCD-01 and PCD-10 messages when the IPEC or DEC profiles are used for a multistep infusion.

| **Data** | **Term** | **Location within PCD-01 or PCD-10 message** | **Required** |
| --- | --- | --- | --- |
| Current step number | MDCX\_INFUS\_ORDER\_STEP\_NUM | INFUSATE\_SOURCE\_\* | Yes |
| Total number of steps | MDCX\_INFUS\_TOTAL\_NUM\_STEPS | INFUSATE\_SOURCE\_\* | Yes |
| Current step volume to be infused | MDCX\_VOL\_STEP\_TBI | INFUSATE\_SOURCE\_\* |
 |
| Current step volume delivered | MDCX\_VOL\_STEP\_DELIV | INFUSATE\_SOURCE\_\* | Yes |
| Current step volume remaining | MDCX\_VOL\_STEP\_REMAIN | INFUSATE\_SOURCE\_\* |
 |
| Total volume infused for all steps | MDC\_VOL\_FLUID\_DELIV\_TOTAL | INFUSATE\_SOURCE\_\* | Yes |
|
 |
 | \* indicates the appropriate source |
 |

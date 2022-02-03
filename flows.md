---
title: Sample Flows
layout: default
sort: 5
---
# Sample Flows/Dialogue Sequences
This document provides a translation of typical use cases into concrete dialogue sequences. It is meant to provide guidance to APDS adopters on their way to become NPP users.

## Use Case 1: Pay on Arrival

## Use Case 2: ANPR Frictionless Payment
### Overall Flow
![Use Case 2 Flow](assets/images/usecases/usecase2flow.png)
### Overview of engaged Endpoints
#### 1. SP uploads Assigned Rights to Platform
<span style="color: green; font-size: 20px">&#x278A;</span> **`SP` &#x27A1; `Platform`**
```
POST /v1/parking/rights/assigned
```

#### 2. VRM sent to Platform
<span style="color: green; font-size: 20px">&#x278B;</span> **`Operator` &#x27A1; `Platform`**
```
GET /v1/parking/rights/assigned/place={locationId}&identifier_id={VRM}
```
Response can include 0 (nothing found), 1 (exactly one match) or more (VRM registered with multiple SPs).

#### 3. Operator sends Session Details to Platform
#### 4. Platform sends Sessino to selected SP
#### 5. Is Session on Platform?
#### 6. Completed Session sent to Platform
#### 7. Platform sends Session Details to SP
#### 8. SP sends Payment Confirmation
#### 9. Platform sends Payment Details to Operator

### Detailed Request/Response Examples
#### 1. SP uploads Assigned Rights to Platform
Request: `POST /v1/parking/rights/assigned`

Payload:
``` json
{
    "id": "20410eec-2352-4cc1-820c-21ea6cbce506",
    "version": 1,
    "rightHolder": {
        "credentials": [
            {
                "type": "licensePlate",
                "identifier": {
                    "id": "BD18SMR",
                    "className": "UKNumberPlate"
                }
            }
        ]
    },
    "rightSpecification": {
        "id": "0ab95c73-4c2b-4fe3-8528-0403c7734b92",
        "version": 1
    },
    "issuanceTime": "2021-10-18 15:21:00",
    "expiry": "2024-12-31 23:59:59",
    "assignedRightIssuer": {
        "id": "SP0001",
        "className": "DigitalServiceProvider"
    },
    "issueMethod": "electronic"
}
```
Response: `HTTP/1.1 201 CREATED`

## Use Case 3: Pay on Departure Ticket Payment


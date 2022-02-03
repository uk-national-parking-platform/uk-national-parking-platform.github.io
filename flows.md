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
**`SP` &#x27A1; `Platform`**
```bash
POST /v1/parking/rights/assigned
```

#### 2. VRM sent to Platform
**`Operator` &#x27A1; `Platform`**
```bash
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

## Use Case 3: Pay on Departure Ticket Payment


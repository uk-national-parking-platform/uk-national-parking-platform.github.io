---
title: Sample Flows
layout: default
sort: 3
---
# Sample Flows/Dialogue Sequences
This document provides a translation of typical use cases into concrete dialogue sequences. It is meant to provide guidance to APDS adopters on their way to become NPP users.

## Use Case 1: Payment on Arrival
### Overall Flow
#### Overview
![Use Case 1 Overview](assets/images/usecases/usecase1overview.png)

#### Flow
![Use Case 1 Flow](assets/images/usecases/usecase1flow.png)

### Overview of engaged Endpoints
#### 1. SP sends Session to Platform
<span style="color: red; font-size: 22px">&#x278A;</span> **`SP` &rarr; `Platform`**
```
POST /v1/parking/sessions
```

#### 2. Enforcement Officer checks Assigned Right on Platform
<span style="color: green; font-size: 22px">&#x278B;</span> **`Enforcement System` &rarr; `Platform`**
```
GET /v1/parking/rights/assigned
```

#### 3. Alternative Enforcement Approach: Subscription
<span style="color: green; font-size: 22px">&#x278C;</span> **`Platform` &rarr; `Enforcement System`**
``` note
As an alternative to adhoc platform queries (see 2.), a platform user can also register a subscription for new/updated information. In this example the enforcement service provider's backend system will then be informed about each new assigned right being created.
```
```
PUT {provider-provided notification endpoint for new assigned rights}
```

### Detailed Request/Response Examples
#### 1. SP sends Session to Platform
``` note
Normally, the SP would first have to send an Assigned Right to the Platform, followed by a Session record referencing this Assigned Right. For efficiency reasons, the APDS interface allows to just send a Session Record. The APDS-conformant Platform is then expected to automatically create a corresponding Assigned Right.
```

Request: `POST /v1/parking/sessions`

Payload: 
``` json

{
  "id": "e2089853-b790-459b-884e-4869b40d193d",
  "version": 1,
  "initiator": "e0326b5c-d73d-43ab-b274-3ecaea5a80db",
  "actualStart": "2021-01-16T13:10:02",
  "acutalEnd": "2021-01-16T14:10:02",
  "credentials": [
    {
      "identifier": {
        "id": "AB12XYZ",
        "className": "UKNumberPlate"
      },
      "type": "licensePlate",
      "issuer": {
        "en": "Vehicle Registry"
      }
    }
  ],
  "hierarchyElement": {
    "id": "001702e7-99f6-4e48-ac7c-df412907b29b",
    "version": 1
  },
  "segments": [
    {
      "id": "8fff245d-b384-406a-bef2-5e6affbe7609",
      "version": 1,
      "actualStart": "2021-01-16T13:10:02",
      "actualEnd": "2021-01-16T14:10:02",
      "validationType": [
        "licensePlate"
      ],
      "financialTransactions": [
        {
          "dateCollected": "2021-01-16T13:10:02",
          "segmentValue": 450,
          "serviceProvider": {
            "en": "RED Parking"
          },
          "taxIncluded": true,
          "transactionId": "TX-ID-PROVIDER-DEFINED-0001"
        }
      ]
    }
  ]
}

```

Response: `HTTP/1.1 200 OK`

Payload:
``` json

{
  "code": 200,
  "status": "OK",
  "message": "session e2089853-b790-459b-884e-4869b40d193d created"
}

```

#### 2. Enforcement Officer checks Assigned Right on Platform
Request: `GET /v1/parking/rights/assigned?place=001702e7-99f6-4e48-ac7c-df412907b29b&credential_id=MS00MS&start_after=1610751600`

Response: `HTTP/1.1 200 OK`

Payload: 
``` json

{
  "meta": {
    "referenceInstant": 1610802000,
    "offset": 0,
    "pageSize": 100,
    "total": 1
  },
  "data": [
    {
      "id": "bec97cab-8eb7-4868-be9f-da95fa17d19e",
      "version": 1,
      "rightHolder": {
        "credentials": [
          {
            "identifier": {
              "id": "MS00MS",
              "className": "UKNumberPlate"
            },
            "issuer": {
              "en": "DMV"
            }
          }
        ]
      },
      "rightSpecification": {
        "id": "a298d061-a30c-4a5a-bfe0-308f20d3ddc7",
        "version": 1
      },
      "expiry": "2021-01-16T14:10:02",
      "assignedRightIssuer": {
        "id": "ID_RED_PARKING",
        "className": "DigitalServiceProvider"
      },
      "issueMethod": "electronic"
    }
  ]
}

```

#### 3. Alternative: Notification based on previous Subscription
Request: `PUT {provider-provided Notification Endpoint for new Assigned Rights}`

Payload:
``` json

{
  "id": "bec97cab-8eb7-4868-be9f-da95fa17d19e",
  "version": 1,
  "rightHolder": {
    "credentials": [
      {
        "identifier": {
          "id": "MS00MS",
          "className": "UKNumberPlate"
        },
        "issuer": {
          "en": "DMV"
        }
      }
    ]
  },
  "rightSpecification": {
    "id": "a298d061-a30c-4a5a-bfe0-308f20d3ddc7",
    "version": 1
  },
  "expiry": "2021-01-16T14:10:02",
  "assignedRightIssuer": {
    "id": "ID_RED_PARKING",
    "className": "DigitalServiceProvider"
  },
  "issueMethod": "electronic"
}

``` 

Response: `HTTP/1.1 200 OK`


## Use Case 2: ANPR Frictionless Payment
### Overall Flow
![Use Case 2 Flow](assets/images/usecases/usecase2flow.png)
### Overview of engaged Endpoints
#### 1. SP uploads Assigned Rights to Platform
<span style="color: red; font-size: 22px">&#x278A;</span> **`SP` &rarr; `Platform`**
```
POST /v1/parking/rights/assigned
```

#### 2. VRM sent to Platform
<span style="color: green; font-size: 22px">&#x278B;</span> **`Operator` &rarr; `Platform`**
```
GET /v1/parking/rights/assigned/place={locationId}&identifier_id={VRM}
```
Response can include 0 (nothing found), 1 (exactly one match) or more (VRM registered with multiple SPs).

#### 3. Operator sends Session Details to Platform
<span style="color: green; font-size: 22px">&#x278C;</span> **`Operator` &rarr; `Platform`**
```
POST /v1/parking/sessions
```

#### 4. Platform sends Session to selected SP
<span style="color: red; font-size: 22px">&#x278D;</span> **`Platform` &rarr; `SP`**
```
PUT {SP-provided Notification Endpoint for new Sessions}
```

#### 5. Is Session on Platform?
<span style="color: green; font-size: 22px">&#x278E;</span> **`Operator` &rarr; `Platform`**
```
GET /v1/parking/sessions/place={locationId}&identifier_id={VRM}
```

#### 6. Completed Session sent to Platform
<span style="color: green; font-size: 22px">&#x278F;</span> **`Operator` &rarr; `Platform`**
```
PUT /v1/parking/sessions/{session id from 3. }
```

#### 7. Platform sends Session Details to SP
<span style="color: red; font-size: 22px">&#x2790;</span> **`Platform` &rarr; `SP`**
```
PUT /{SP-provided Notification Endpoint for Session Data Updates}
```

#### 8. SP sends Payment Confirmation
<span style="color: red; font-size: 22px">&#x2791;</span> **`SP` &rarr; `Platform`**
```
PUT /v1/parking/sessions/{session id from 3.}
```

#### 9. Platform sends Payment Details to Operator
<span style="color: green; font-size: 22px">&#x2792;</span> **`Platform` &rarr; `Operator`**

_MISSING_

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

#### 2. VRM sent to Platform
Request: `GET /v1/parking/rights/assigned?identifier_id=BD18SMR&place=220001`

Response: `HTTP/1.1 200 OK`

Response Payload:
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

#### 3.	Operator sends Session Details to Platform (incl. Assigned Right)
Request: `POST /v1/parking/sessions`

Payload: 
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "validationType": ["licensePlate"],
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

``` 

Response: `HTTP/1.1 201 CREATED`

#### 4. Platform sends Session to selected SP

Request: `PUT /callbacks.service-provider-001.com/sessions/new`

Payload:
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "validationType": ["licensePlate"],
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

```

Response: `HTTP/1.1 200 OK`

#### 5. Is Session on Platform?
Request: `GET /v1/parking/sessions?identifier_id=BD18SMR&place=220001&start_after=1635724800`

Response: `HTTP/1.1 200 OK` 

Payload: 
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "validationType": ["licensePlate"],
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

```

#### 6. Completed Session sent to Platform
Request: `PUT /v1/parking/sessions/fe5eea6a-4be3-46fa-b037-20f04334ccdd`

Payload:
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "actualEnd": "2021-11-01 17:23:01",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "actualEnd": "2020-11-01 17:23:01",
            "validationType": ["licensePlate"],
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

```

Response: `HTTP/1.1 200 OK`

#### 7. Platform sends Session Details to SP
Request: `PUT /callbacks.service-provider-001.com/sessions/updated`

Payload:
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "actualEnd": "2021-11-01 17:23:01",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "actualEnd": "2020-11-01 17:23:01",
            "validationType": ["licensePlate"],
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

``` 

Response: `HTTP/1.1 200 OK` 

#### 8. SP sends Payment Confirmation
Request: `PUT /v1/parking/sessions/fe5eea6a-4be3-46fa-b037-20f04334ccdd`

Payload:
``` json

{
    "id": "fe5eea6a-4be3-46fa-b037-20f04334ccdd",
    "version": 1,
    "actualStart": "2021-11-01 13:05:00",
    "actualEnd": "2021-11-01 17:23:01",
    "credentials": [
        {
            "identifier":
            {
                "id":"BD18SMR", 
                "className":"UKNumberPlate"
            },
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "9323f1a7-ac48-4ab1-a141-7381373583ca",
            "version": 1,
            "actualStart": "2020-11-01 13:05:00",
            "actualEnd": "2020-11-01 17:23:01",
            "validationType": ["licensePlate"],
            "financialTransactions": [
                {
                    "dateCollected": "2021-11-01 17:23:01",
                    "segmentValue": 10.80,
                    "serviceProvider": { "en": "SP0001"},
                    "taxIncluded": true,
                    "transactionId": "d9d237be-0432-44eb-9807-91fd01aae835"
                }
            ]
        }
    ],
    "hierarchyElement": {
        "id": "220001",
        "version": 1
    }
}

``` 

Response: `HTTP/1.1 200 OK` 

#### 9. Platform sends Payment Details to Operator

_MISSING_

## Use Case 3: Pay on Departure Ticket Payment


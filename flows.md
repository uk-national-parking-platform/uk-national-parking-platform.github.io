---
title: Sample Flows
layout: default
sort: 3
---
# Sample Flows/Dialogue Sequences
This document provides a translation of typical use cases into concrete dialogue sequences. It is meant to provide guidance to APDS adopters on their way to become NPP users.

## Use Case 0: Parking Availability

### Overview
![Static Information and Dynamic Occupancy](assets/images/usecases/usecase0overview.png)

### Overview of engaged Endpoints

#### 1. SP queries the Platform for available Parking Locations
<span style="color: red; font-size: 22px">&#x278A;</span> **`SP` &rarr; `Platform`**

The following request finds parking locations within a 1000 metres radius of a particular point:
``` 
GET /v1/parking/places?latitude=53.4806&longitude=-2.2428&radius=1000&expand=all
```

#### 2. SP queries the Platform for a Location's Tariffs/Rates
<span style="color: red; font-size: 22px">&#x278B;</span> **`SP` &rarr; `Platform`**

The following request first reads right specification / eligibility information about a particular location and then retrieves the corresponding tariff information.
```
// step 1: get right specification
GET /v1/parking/rights/{rightSpecificationIdForSelectedPlace}?expand=all

// step 3: get rate details
GET /v1/parking/rates/{rateId from previous request}?expand=all

```

#### 3. SP queries the Platform for current Space Availability
<span style="color: red; font-size: 22px">&#x278C;</span> **`SP` &rarr; `Platform`**

Current space availability information is retrieved via the _occupancy_ filter of the _/places_ endpoint:
```
GET /v1/parking/places/{placeId}?expand=occupancy

```

### Detailed Request/Response Examples

#### 1. SP queries the Platform for available Parking Locations

#### 2. SP queries the Platform for a Location's Tariffs/Rates

Request (Step 1): `GET /v1/parking/rights/6091d5d0-5264-4d66-a31a-1a9e2c9eed89?expand=all`

Response (Step 1): `HTTP/1.1 200 OK`

Payload: (Step 1): 
``` json
{
    "id": "6091d5d0-5264-4d66-a31a-1a9e2c9eed89",
    "version": 1,
    "type": "oneTimeUseParking",
    "description": {
        "en": "right specification for Arndale Car Park in Manchester City"
    },
    "expiry": "2025-12-31 23:59:59",
    "hierarchyElements": [
        {
            "id": "805432",
            "version": 1
        }
    ],
    "rateEligibility": [
        {
            "id": "b49794f3-a845-4451-8618-641100f49b2e",
            "version": 1,
            "rate": {
                "id": "0376c349-ed20-4f6f-9551-1a82f9f5f734",
                "version": 1
            }
        }
    ],
    "validity": {
        "validPeriods": [
            {
                "periodName": {
                    "en": "all week long"
                },
                "recurringDayWeekMonthPeriod": {
                    "applicableDay": [
                        "monday",
                        "tuesday",
                        "wednesday",
                        "thursday",
                        "friday",
                        "saturday",
                        "sunday"
                    ]
                }
            }
        ]
    }
}

```

Request (Step 2): `GET /v1/parking/rates/0376c349-ed20-4f6f-9551-1a82f9f5f734?expand=all`

Response (Step 2): `HTTP/1.1 200 OK`

Payload (Step 2):
``` json
{
  "id": "2a00f59e-50b0-4ac1-8733-1b3d23482403",
  "version": 1,
  "rateTableName": {
    "en": "Arndale General Public Tariff"
  },
  "availability": "public",
  "validation": false,
  "rateResponsibleParty": "Manchester City Council",
  "rateLineCollections": [
    {
      "collectionSequence": 1,
      "taxIncluded": true,
      "rateLines": [
        {
          "sequence": 1,
          "description": {
            "en": "up to 1 hour"
          },
          "rateLineType": "incrementingRate",
          "value": 3.7,
          "durationStart": "P0S",
          "durationEnd": "P3599S"
        },
        {
          "sequence": 2,
          "description": {
            "en": "up to 2 hours"
          },
          "rateLineType": "incrementingRate",
          "value": 6.7,
          "durationStart": "P3600S",
          "durationEnd": "P7199S"
        },
        {
          "sequence": 3,
          "description": {
            "en": "up to 3 hours"
          },
          "rateLineType": "incrementingRate",
          "value": 9.2,
          "durationStart": "P7200S",
          "durationEnd": "P10799S"
        },
        {
          "sequence": 4,
          "description": {
            "en": "up to 4 hours"
          },
          "rateLineType": "incrementingRate",
          "value": 12.2,
          "durationStart": "P10800S",
          "durationEnd": "P14399S"
        },
        {
          "sequence": 5,
          "description": {
            "en": "up to 5 hours"
          },
          "rateLineType": "incrementingRate",
          "value": 15.2,
          "durationStart": "P14400S",
          "durationEnd": "P17999S"
        },
        {
          "sequence": 6,
          "description": {
            "en": "up to 24 hours"
          },
          "rateLineType": "incrementingRate",
          "value": 20.5,
          "durationStart": "P18000S",
          "durationEnd": "P86399S"
        }
      ]
    }
  ]
}

```

#### 3. SP queries the Platform for current Space Availability

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
  "actualEnd": "2021-01-16T14:10:02",
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

#### 5. Completed Session sent to Platform
<span style="color: green; font-size: 22px">&#x278F;</span> **`Operator` &rarr; `Platform`**
```
PUT /v1/parking/sessions/{session id from 3. }
```

#### 6. Platform sends Session Details to SP
<span style="color: red; font-size: 22px">&#x2790;</span> **`Platform` &rarr; `SP`**
```
PUT /{SP-provided Notification Endpoint for Session Data Updates}
```

#### 7. SP sends Payment Confirmation
<span style="color: red; font-size: 22px">&#x2791;</span> **`SP` &rarr; `Platform`**
```
PUT /v1/parking/sessions/{session id from 3.}
```

#### 8. Platform sends Payment Details to Operator
<span style="color: green; font-size: 22px">&#x2792;</span> **`Platform` &rarr; `Operator`**

_MISSING\_TBD_

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

#### 3.	Operator sends Details of new Session to Platform (incl. Assigned Right)
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

#### 4. Platform sends new Session to selected SP

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


#### 5. Completed Session sent to Platform (by Operator)
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
                    "segmentValue": 10.80,
                    "serviceProvider": { "en": "SP0001"},
                    "taxIncluded": true
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

#### 6. Platform sends updated Session Details to SP
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
            "financialTransactions": [
                {
                    "segmentValue": 10.80,
                    "serviceProvider": { "en": "SP0001"},
                    "taxIncluded": true
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

#### 7. SP sends Payment Confirmation
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

#### 8. Platform sends Payment Details to Operator

_MISSING\_TBD_

## Use Case 3: Pay on Departure Ticket Payment
### Overall Flow
![Use Case 3 Flow](assets/images/usecases/usecase3flow.png)
 



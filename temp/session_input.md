# Input for Session on 21-02-2022

## Journey
1. Driver lets vehicle/app search parking options close to the destination
2. Driver selects a parking location and retrieves details (e.g. rate information)
3. _(Driver arrives at destination and parks)_
4. _(Vehicle lets driver confirm purchase of parking session)_
5. Vehicle purchases parking session
6. Civil Enforcement Officer checks legitimacy of vehicle parked there
7. Parking session is extended
8. _(Vehicle leaves)_

## Engaged NPP Endpoints, Flow Details
### 1. Search Parking Options
The driver's destination is a "Motel One" in Manchester where he wants to meet somebody (the on-board system knows/retrieves the coordinates: 53.4816507,-2.243939).
The driver wants to avoid a walking distance of more than about a quarter mile.
The vehicle/app subsmits a corresponding request to the NPP:
```
GET https://api-staging.npp.org.uk/v1/parking/places?expand=opening_times,geo,characteristics&latitude=53.4816507&longitude=-2.243939&radius=430

```
_(note: via the "expand" option, the system has limited to verbosity of the response to location, characteristics and opening times)_

The NPP responds with a number of matching locations:
``` json
{
    "meta": {
        "referenceInstant": 1645429274,
        "offset": 0,
        "pageSize": 200,
        "total": 3
    },
    "data": [
        {
            "id": "805429",
            "version": 1,
            "type": "place",
            "layer": 1,
            "name": {
                "en": "King Street West"
            },
            "indicativePointLocation": {
                "type": "Point",
                "coordinates": [
                    -2.249014,
                    53.481971
                ]
            },
            "description": {
                "en": "King Street West Car Park in Manchester City"
            },
            "parent": {
                "id": "b644de15-463f-418e-a147-6db11431daa4",
                "version": 1
            },
            "areaType": "generalParking",
            "characteristics": {
                "accessControlled": false,
                "evChargingPoints": 0,
                "openToPublic": true,
                "spacesTotal": 554,
                "structureGrade": "aboveGround",
                "structureType": "offStreetStructure"
            },
            "rightSpecifications": [
                {
                    "id": "b554a3c4-98de-4156-a48b-1fe52a21fbe1",
                    "version": 1
                }
            ],
            "openingTimes": {
                "available24hours": true,
                "openAllYear": true
            }
        },
        {
            "id": "805430",
            "version": 1,
            "type": "place",
            "layer": 1,
            "name": {
                "en": "Northern Quarter"
            },
            "indicativePointLocation": {
                "type": "Point",
                "coordinates": [
                    -2.2380309,
                    53.4831644
                ]
            },
            "description": {
                "en": "Northern Quarter Car Park in Manchester City"
            },
            "parent": {
                "id": "b644de15-463f-418e-a147-6db11431daa4",
                "version": 1
            },
            "areaType": "generalParking",
            "characteristics": {
                "accessControlled": false,
                "evChargingPoints": 0,
                "openToPublic": true,
                "spacesTotal": 700,
                "structureGrade": "aboveGround",
                "structureType": "offStreetStructure"
            },
            "rightSpecifications": [
                {
                    "id": "0eb95b73-9c2b-4fe3-8528-0403c7734b97",
                    "version": 1
                }
            ],
            "openingTimes": {
                "available24hours": true,
                "openAllYear": true
            }
        },
        {
            "id": "805428",
            "version": 1,
            "type": "place",
            "layer": 1,
            "name": {
                "en": "Deansgate"
            },
            "indicativePointLocation": {
                "type": "Point",
                "coordinates": [
                    -2.247177,
                    53.483596
                ]
            },
            "description": {
                "en": "Deansgate Car Park in Manchester City"
            },
            "parent": {
                "id": "b644de15-463f-418e-a147-6db11431daa4",
                "version": 1
            },
            "areaType": "generalParking",
            "characteristics": {
                "accessControlled": false,
                "evChargingPoints": 0,
                "openToPublic": false,
                "spacesTotal": 340,
                "structureGrade": "aboveGround",
                "structureType": "offStreetStructure"
            },
            "rightSpecifications": [
                {
                    "id": "ab413648-4ead-42f6-ac3c-9c635285e45b",
                    "version": 1
                },
                {
                    "id": "04398203-91b1-4cbc-9fda-04cc7e609113",
                    "version": 1
                }
            ],
            "openingTimes": {
                "available24hours": false,
                "openAllYear": false
            }
        }
    ]
}

```

The vehicle's on-board system/mobility app uses the received information to appropriately display viable options to the driver:

* the coordinates and the name information can be used to mark the parking locations on the map
* the characteristics provide additional details that might be helpful for the driver (e.g. EV charging point availability, structure type)

<u>Note:</u> one of the three returned car parks can currently not be used: "Deansgate" is currently not open to the public and hence is filtered out by the app.

From the two remaining matching options, the driver chooses "Northern Quarter" as this car park as more spaces than the other one.

## Retrieve Details
Based on the driver's choice, the vehicle's on-board system/app now retrieves additional details on the destination. 
The previously received response specifies the right specification(s) applicable at "Northern Quarter" (id _0eb95b73-9c2b-4fe3-8528-0403c7734b97_). The system uses this to query for eligibility information:

```
GET https://api-staging.npp.org.uk/v1/parking/rights/specs/0eb95b73-9c2b-4fe3-8528-0403c7734b97?expand=all

```

The NPP responds with the corresponding details:
``` json
{
    "id": "0eb95b73-9c2b-4fe3-8528-0403c7734b97",
    "version": 1,
    "type": "oneTimeUseParking",
    "description": {
        "en": "right specification for Northern Quarter Car Park in Manchester City"
    },
    "expiry": "2025-12-31 23:59:59",
    "hierarchyElements": [
        {
            "id": "805430",
            "version": 1
        }
    ],
    "rateEligibility": [
        {
            "id": "96b5580d-f7a3-42f8-85c5-ddce73b57998",
            "version": 1,
            "rate": {
                "id": "6ef9cda0-3bf1-4e7e-a2f2-3d306f61ec94",
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

Amongst other things, this first response points the vehicle's on-board system to the rate table that applies at this location (id _6ef9cda0-3bf1-4e7e-a2f2-3d306f61ec94_). The system then sends a second query to the NPP to retrieve the rate details:

```
GET https://api-staging.npp.org.uk/v1/parking/rates/6ef9cda0-3bf1-4e7e-a2f2-3d306f61ec94?expand=all
 
```

The NPP returns the requested rate information:

``` json
{
    "id": "6ef9cda0-3bf1-4e7e-a2f2-3d306f61ec94",
    "version": 1,
    "rateTableName": {
        "en": "rate table for Northern Quarter Car Park in Manchester"
    },
    "rateLineCollections": [
        {
            "applicableCurrency": "GBP",
            "collectionSequence": 0,
            "rateLines": [
                {
                    "sequence": 0,
                    "description": {
                        "en": "Up to 1 hour"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 3.2,
                    "durationStart": "P0S",
                    "durationEnd": "P3599S"
                },
                {
                    "sequence": 1,
                    "description": {
                        "en": "Up to 2 hours"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 6.7,
                    "durationStart": "P3600S",
                    "durationEnd": "P7199S"
                },
                {
                    "sequence": 2,
                    "description": {
                        "en": "Up to 3 hours"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 9.7,
                    "durationStart": "P7200S",
                    "durationEnd": "P10799S"
                },
                {
                    "sequence": 3,
                    "description": {
                        "en": "Up to 4 hours"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 12.2,
                    "durationStart": "P10800S",
                    "durationEnd": "P14399S"
                },
                {
                    "sequence": 4,
                    "description": {
                        "en": "Up to 5 hours"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 15.1,
                    "durationStart": "P14400S",
                    "durationEnd": "P17999S"
                },
                {
                    "sequence": 5,
                    "description": {
                        "en": "Up to 24 hours"
                    },
                    "rateLineType": "incrementingRate",
                    "value": 20.5,
                    "durationStart": "P18000S",
                    "durationEnd": "P86399S"
                }
            ],
            "relativeTimes": true
        }
    ],
    "availability": "public",
    "rateResponsibleParty": "Manchester City Council"
}

```

### 3. Drive to Destination
The driver is satisfied with the information presented by his vehicle and navigates to the destination.

### 4. Arrival at Destination
"Northern Quarter" is a barrier-less multi-storey car park. So, the driver just enters and parks his vehicle in a free space. The driver activates his vehicle's "park here now" function and indicates that he wants to stay for two hours. The on-board unit displays a summary and asks for confirmation.

### 5. Parking Right/Session Purchase
The vehicle now sends the session purchase information to the NPP. The corresponding endpoint is this:

``` 
POST https://api-staging.npp.org.uk/v1/parking/sessions

```

The request payload contains the session details:

``` json
{
    "id": "d49289eb-de8e-4a68-bbac-67a86c7bcfb1",
    "version": 1,
    "actualStart": "2022-02-18 08:21:00",
    "actualEnd": "2022-02-18 10:21:00",
    "credentials": [
        {
            "identifier": { "id":"BD18SMR", "className":"UKNumberPlate"},
            "issuer": { "en": "DVLA"},
            "type":"licensePlate"}
    ],
    "segments": [
        {
            "id": "7205b234-2c82-435a-b5fd-42b69ce41bc6",
            "version": 1,
            "actualStart": "2022-02-18 08:21:00",
            "actualEnd": "2022-02-18 10:21:00",
            "validationType": ["licensePlate"],
            "financialTransactions": [
                {
                    "dateCollected": "2022-02-18 08:20:30",
                    "segmentValue": 6.7,
                    "serviceProvider": {"en":"CAURA"},
                    "taxIncluded": true
                }
            ]
        }
    ],
    "hierarchyElement": {"id":"805430","version":1}
}

```
(<u>note:</u> as the vehicle's on-board system has only provided a session record, the NPP implicitly assumes that it shall auto-generate a corresponding assigned right along with the session).

The NPP confirms receipt of the session data. The vehicle's on-board unit confirms to the driver that he now has an active parking session. The driver activates the app's notification service in order to receive a notification message shortly before the session will expire. The driver leaves the car park and walks over to the meeting location.

### 6. CEO checks Vehicle
Around 9:15am, while the customer is having his meeting at "Motel One", a civil enforcement officer (CEO) checks the parked vehicles in the "Northern Quarter" car park. He takes his handheld device and types in some details to check the customer's vehicle:

- VRM (APDS interface parameter _crendential\_id_)
- location (APDS interface parameter _place_)
- time window to narrow down the search (in this example APDS interface parameter _end\_after_)

```
GET https://api-staging.npp.org.uk/v1/parking/rights/assigned?end_after=1645175700&expand=all&place=805430&credential_id=BD18SMR

```
The NPP responds with one matching record:

``` json

{
    "meta": {
        "referenceInstant": 1645432598,
        "offset": 0,
        "pageSize": 100,
        "total": 1
    },
    "data": [
        {
            "id": "523bd4af-420e-4064-bce8-149f80a560cb",
            "version": 1,
            "rightHolder": {
                "credentials": [
                    {
                        "type": "licensePlate",
                        "identifier": {
                            "id": "BD18SMR",
                            "className": "UKNumberPlate"
                        },
                        "issuer": {
                            "en": "DVLA"
                        }
                    }
                ]
            },
            "rightSpecification": {
                "id": "0eb95b73-9c2b-4fe3-8528-0403c7734b97",
                "version": 1,
                "expiry": "2025-12-31 23:59:59",
                "transferable": false,
                "issuer": "MCC",
                "hierarchyElements": [
                    {
                        "id": "805430",
                        "version": 1,
                        "className": "HierarchyElement"
                    }
                ],
                "credentials": [
                    "licensePlate",
                    "ticket"
                ],
                "type": "oneTimeUseParking"
            },
            "expiry": "2022-02-18 10:21:00",
            "issuanceTime": "2022-02-18 08:21:00",
            "assignedRightIssuer": {
                "id": "CAURA",
                "className": "DigitalServiceProvider"
            },
            "issueMethod": "electronic"
        }
    ]
}
```

This tells the CEO that the vehicle is currently legitimately parked here. No further action required.

### 7. Session Extension
At around 10:06am, the customer receives a push notification telling him that his parking session is about to expire in 15 minutes. His meeting is not over yet, so he decides to purchase a one hour extension for his parking session. The companion app on his mobile phone lets him do this. After he confirms the purchase order, the provider's system contacts the corresponding NPP endpoint:

```
PUT https://api-staging.npp.org.uk/v1/parkings/sessions/d49289eb-de8e-4a68-bbac-67a86c7bcfb1

```

The request payload contains the details of the session extension:

``` json

{
    "id": "d49289eb-de8e-4a68-bbac-67a86c7bcfb1",
    "version": 1,
    "actualStart": "2022-02-18 08:21:00",
    "actualEnd": "2022-02-18 11:21:00",
    "credentials": [
        {
            "type": "licensePlate",
            "identifier": {
                "id": "BD18SMR",
                "className": "UKNumberPlate"
            },
            "issuer": {
                "en": "DVLA"
            }
        }
    ],
    "segments": [
        {
            "id": "7205b234-2c82-435a-b5fd-42b69ce41bc6",
            "version": 1,
            "actualStart": "2022-02-18 08:21:00",
            "actualEnd": "2022-02-18 10:21:00",
            "validationType": ["licensePlate"],
            "financialTransactions": [
                {
                    "dateCollected": "2022-02-18 08:20:30",
                    "segmentValue": 6.7,
                    "serviceProvider": {"en":"CAURA"},
                    "taxIncluded": true
                }
            ]
        },
        {
            "id": "c0cd4e00-a965-44db-be6a-8e6a3a364a0b",
            "version": 1,
            "actualStart": "2022-02-18 10:21:00",
            "actualEnd": "2022-02-18 11:21:00",
            "validationType": [
                "licensePlate"
            ],
            "financialTransactions": [
                {
                    "dateCollected": "2022-02-18 10:07:00",
                    "segmentValue": 3.2,
                    "serviceProvider": {
                        "en": "CAURA"
                    },
                    "taxIncluded": true
                }
            ]
        }
    ],
    "hierarchyElement": {
        "id": "805430",
        "version": 1,
        "className": "HierarchyElement"
    }
}
```
> 
> Note: 
>
> The provider did not create a new session,  
> but added a second **segment** to the existing session.
> 

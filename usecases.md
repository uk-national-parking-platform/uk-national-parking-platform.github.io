---
title: Use Cases 
layout: default
sort: 3
---
# Use Case Overview
This document provides an overview of the use cases implemented during the various phases of the _National Parking Platform_ pilot project. Software developers implementing such flows, detailed examples can be found [here](flows.md).

## Use Case 0: Parking Availability

![Static Information and Dynamic Occupancy](assets/images/usecases/usecase0overview.png)

## Use Case 1: Payment on Arrival
The first use case introduced in phase 2 of the project was _Payment on Arrival_. Besides the parking end customer, the use case involves two platform users: the pay-by-mobile service provider and the enforcement service provider contracted by the operator.

![Payment on Arrival](assets/images/usecases/usecase1overview.png)

### Part 1: Parking
A customer (driver) arrives at the car park / parking area and parks his car. He then opens the mobile application provided by his service provider and enters/confirms relevant information:

* location (often automatically determined by the app)
* license plate number of his vehicle
* expected duration of stay, alternatively: expected time of leaving the car park 
* payment details

Using the provider application, the customer pays the amount due. He has now successfully purchased a right to park here, and a corresponding parking session has been created. The service provider’s system sends the details of this parking session (including payment information) to the platform.


### Part 2: Enforcement
An enforcement service officer sees the customer’s parked car and wants to check the legitimacy of this parking sessions. Using a handheld device, he types in the license plate number of the parked vehicle. The enforcement system then sends a search request to the platform (including location information and the license plate number). The platform will do a look-up and return all matching data or the information that no data could be found. 

Alternative process: if the enforcement service provider’s IT system has the capability to temporarily store parking session information, a corresponding subscription for parking session data can be registered. The platform will then pro-actively send all new session information for a particular car park / parking area to the enforcement system backend.

## Use Case 2: ANPR Frictionless Payment

![ANPR Frictionless Payment](assets/images/usecases/usecase2overview.png)

## Use Case 3: Pay on Departure Ticket Payment

![Payment on Arrival](assets/images/usecases/usecase3overview.png)

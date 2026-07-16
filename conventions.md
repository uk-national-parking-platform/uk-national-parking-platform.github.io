---
layout: home 
sidebar:
  nav: sidemain
title: Conventions
sort: 0
---

## Need for Conventions
The _NPP_ is based on the _APDS_ standard data model and the _APDS_-defined API. There are however situations where the implementing parties need to agree upon conventions. The same applies to the configuration of an operator's inventory (locations, tariffs, charging hours).

Examples are:

* which set of enumeration values to use (where _APDS_ defines more than needed by the _NPP_)
* how to handle edge cases (e.g. boundaries of from/to time ranges)
* which set of classes and API endpoints to use (the _NPP_ e.g. does currently not make use of the _APDS_ _Observation_ and _Quotes_ domains)
* which set of eligibity criteria to use

## Identified Required Conventions
This section provides the already identified and agreed-upon conventions in the _NPP_ context.

### Indicative Point Location
The _APDS_ property _indicativePointLocation_ of a parking location shall be specified/interpreted as the car park's main entrance or centre of street (by length) or centre of parking bay.

### Weeks
The first day of a week is _Monday_. 

### Validity End Times
If determined as same as start of next validity, always 0.001 minute before stated time. E.g. validity 1 is 10:00 to 18:00, validity 2 is 18:00 to 22:00 shall be interpreted as validity 1 from 10:00 to 17:59.999 and validity 2 from 18:00 to 22:00.

### Energy Source Types
More and more operators introduce tariffs that differentiate by a vehicle's energy source. _APDS_ defines a wide range of source types not all of which are used within the _NPP_ context.

* For petrol-powered vehicles, the _NPP_ only differentiates between _**petrol**_ and **_petrolBatteryHybrid_**. Specialisations concerning octane and lead are not used, they all fall under the _**petrol**_ categorisation.
* The same applies to the Diesel energy source. The _NPP_ only differentiates between _**diesel**_ and _**dieselBatteryHybrid**_. Hence, _**biodiesel**_ counts as diesel.
* The _NPP_ only uses _**lpg**_, and _**liquidGas**_ counts as _**lpg**_.
* Fully electric vehicles are categorised as _**battery**_.
* The _**unknown**_ enum is used in all situations where the energy source is either unknown/uncategorised or cannot be otherwise categorised (using the set of _APDS_-defined enumeration values).
* There is currently no _NPP_ use case where the _**all**_ categorisation would be required.
* The _**other**_ category is not used, because it creates contextual dependencies.


### Vehicle Types
The APDS model defines an extensive list of vehicle types not all of which are used in the NPP context. The following table lists the APDS vehicle type enumeration values used for the NPP along with an information of implicitly included APDS enumeration values and a mapping to the nearest DVLA category equivalent.

| NPP category in use  | included additional APDS categories<br/>(i.e. not explicitly used) | nearest DVLA category |
| --- | --- | --- | 
| agriculturalVehicle | | T, R, S |
| bicycle | | |
| bus | articulatedBus, articulatedTrolleyBus, trolleyBus | M3 |
| car | carOrLightVehicle, fourWheelDrive, largeCar, passengerCar | M1 |
| caravan | | O2 |
| carWithCaravan | | |
| carWithTrailer | | |
| heavyGoodsVehicle | heavyDutyTransporter, heavyGoodsVehicleWithTrailer, heavyVehicle,<br/>highSidedVehicle, longHeavyLorry, lorry, tanker | N3 |
| largeGoodsVehicle | |N2|
| minibus | | M2 |
| motorcycle | moped, motorScooter, twoWheeledVehicle | L1, L3 |
| motorcycleWithSideCar | | L4 |
| motorhome | | M1 "motor caravan" |
| smallCar | | L6, L7 |
| threeWheeledVehicle | | L5, L2 |
| trailer | | O |
| van | | N1 |
| vehicleWithCaravan | | |
| vehicleWithTrailer | | |


### Order of Priority
There is always a remaining risk of slight inconsistencies in the inventory. To provide guidance on how to handle such situations, the following **order of priority** has been defined:
1. `Place.operatingRestrictions` (operating restrictions defined for a parking location)
2. `RightSpecification.validity` (validity as specified in the _hours groups_ definition)
3. `RightSpecification.eligibility` (specified eligibilities)
4. `RateTable.validity` (validity as specified in the _tariff_ - if any)
5. `RateTable.rateLineCollections.maxTime` (max. duration of stay)
6. `RateTable.rateLineCollections.rateLines` (increments in the tariff)

A higher-level element (according to this order) always overrules subsequent items.

## Transactions and Reconciliation
### VAT Amounts
#### VAT calculation
VAT should be calculated for each transaction, based on the appropriate rate for the location. Individul Right Specifications should therefore never refer to multiple locations with differing VAT rates (i.e. on and off street).
#### Rounding
VAT amounts shall be rounded to the nearest 1p by rounding up any amount of 0.5p and higher and rounding down any amount lower than 0.5p as defined in the VAT Traders’ Records Manual <a href="https://www.gov.uk/hmrc-internal-manuals/vat-trader-records/vatrec12030" target="_blank">“VATREC12030”</a>.
### Commission Amounts
#### Commission Calculation
To avoid introducing significant rounding errors into commission fees, amounts of Commission to be charged to Operators shall be calculated on the total parking tariff (including VAT where appropriate) collected on behalf of a Parking Operator in the Reconciliation Period. Whilst indicative Commission shall be provided in transaction data, this should not be used to calculate the commission charged.
#### Rounding
Indicative Commission shall be rounded to the nearest 0.1p by rounding up any amount of 0.05p and higher and rounding down any amount lower than 0.05p

Commission amounts calculated on the total parking tariff shall be rounded to the nearest 1p by rounding up any amount of 0.5p and higher and rounding down any amount lower than 0.5p.


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

•	For petrol-powered vehicles, the NPP only differentiates between _*petrol*_ and *_petrolBatteryHybrid_*. Specialisations concerning octane and lead are not used, they all fall under the _*petrol*_ categorisation.
•	The same applies to the Diesel energy source. The NPP only differentiates between _*diesel*_ and _*dieselBatteryHybrid*_. Hence, _*biodiesel*_ counts as diesel.
•	The NPP only uses _*lpg*_, and _*liquidGas*_ counts as _*lpg*_.
•	Fully electric vehicles are categorised as _*battery*_.
•	The _*unknown*_ enum is used in all situations where the energy source is either unknown/uncategorised or cannot be otherwise categorised (using the set of _APDS_-defined enumeration values).
•	There is currently no NPP use case where the _*all*_ categorisation would be required.
•	The _*other*_ category is not used, because it creates contextual dependencies.


---
title: NPP Use Cases 
layout: home
sidebar:
  nav: sidemain
sort: 1
---

## Use Cases - Current Scope
### Inventory Data
* _Operator_: uploads new static inventory information, updates existing inventory information (locations, charging hours, tariffs)
* _Service Provider_: loads all location, charging hours, tariff details to feed their rate engine
* _Connected Supplier_: loads list of locations to be offered on handheld units (including grouping information)

### Occupancy Information
* _Operator_: continuously updates occupancy information (via their PMS/sensor system)
* _Connected Supplier_, (Service Provider): reads occupancy information to feed their app/website

### Pay on Arrival
* _Operator_: reads session information to pre-check challenged PCNs (via the session viewer application)
* _Service Provider_: reports issued parking rights as well as new and updated session information

### Check in/out  
* _Operator_: reads session information to pre-check challenged PCNs (via the session viewer application)
* _Service Provider_: reports issued parking rights as well as new and updated session information. Motorists don't prepay for a fixed period, the amount due is calculated, charged and reported upon departure (check out)

### Enforcement
* _Connected Supplier_: reads assigned right (and session) information to assess the legitimisation of a parked vehicle

### Reconciliation
* _Operator_: downloads consolidated information for reconciliation process
* _Service Provider_: submits details required for the reconciliation process
  
  
  
---

## Intended Future Use Cases
### Adjacent Session Handling
* _Service Provider_: retrieves (trimmed) session information to prevent policy circumvention via "provider hopping"

### PODBA (Pay On Departure By App)
_Service Provider_: reads session information and posts payment information to support PODBA to their end users
_Operator (via PMS)_: provides sessions and assigned right information to support PODBA flow

### Permit Parking
_Service Provider_: reports issued parking rights (of type "permit", i.e. multi-use parking rights), does NOT provide session information
_Connected Supplier_: checks the existence of a parking right

### Frictionless Parking
_Service Provider_: offers frictionless parking based on pre-registered user accounts
_Operator (via PMS)_: supports frictionless flow

### Observability / Health and Performance
_All_: receive events/alerts, inquire platform status information
_Operator_: receive statistics




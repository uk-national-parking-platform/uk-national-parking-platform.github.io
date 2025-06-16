---
title: NPP Use Cases 
layout: home
sidebar:
  nav: sidemain
sort: 1
---

## Use Cases - Current Scope
### Inventory Data
* Operator: uploads new static inventory information, updates existing inventory information (locations, charging hours, tariffs)
* Service Provider: loads all location, charging hours, tariff details to feed their rate engine
* Connected Supplier: loads list of locations to be offered on handheld units (including grouping information)

### Occupancy Information
* Operator: continuously updates occupancy information (via their PMS/sensor system)
* Connected Supplier, (Service Provider): reads occupancy information to feed their app/website

### Pay on Arrival
* Operator: reads session information to pre-check challenged PCNs (via the session viewer application)
* Service Provider: reports issued parking rights as well as new and updated session information

### Enforcement
* Connected Supplier: reads assigned right (and session) information to assess the legitimisation of a parked vehicle

### Reconciliation
* Operator: downloads consolidated information for reconciliation process
* Service Provider: submits details required for the reconciliation process

  

---

## Intended Future Use Cases
### Adjacent Session Handling
* Service Provider: retrieves (trimmed) session information to prevent policy circumvention via "provider hopping"

### PODBA (Pay On Departure By App)
Service Provider: reads session information and posts payment information to support PODBA to their end users
Operator (via PMS): provides sessions and assigned right information to support PODBA flow

### Permit Parking
Service Provider: reports issued parking rights (of type "permit", i.e. multi-use parking rights), does NOT provide session information
Connected Supplier: checks the existence of a parking right

### Frictionless Parking
Service Provider: offers frictionless parking based on pre-registered user accounts
Operator (via PMS): supports frictionless flow

### Observability / Health and Performance
All: receive events/alerts, inquire platform status information
Operator: receive statistics




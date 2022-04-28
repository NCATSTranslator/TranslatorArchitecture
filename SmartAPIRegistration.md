# SmartAPI Registrations


## Background

The SmartAPI registry is Translator's discovery registry.  Translator tools create a yaml registration that can be queried through the SmartAPI registry. 

This document defines the Translator requirements for such registrations.

Within a registration, each 

## Requirements

### Granularity

There must be exactly one registration per infores / TRAPI version.  

### Server Count

There must exist only one server per infores / x-maturity level

### Environments

There are four Translator environments: ITRB production, ITRB test, ITRB CI, and non-ITRB.   These map to the four allowed values of x-maturity:

| x-maturity | Environment |
|----------|----------|
| production | ITRB Prod|
| testing | ITRB Test |
| staging | ITRB CI |
| development | Not-ITRB |

### Name

Each registration contain a human-readable name, which is displayed in the human-readable web interface to the registry.   This name should be the same for all registrations with the same infores value.   The registration interface may chose to display the name with other information such as the TRAPI version.

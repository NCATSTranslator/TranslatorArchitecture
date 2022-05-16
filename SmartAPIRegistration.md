# SmartAPI Registrations


## Background

The SmartAPI registry is Translator's discovery registry.  Translator tools create a yaml registration that can be queried through the SmartAPI registry. 

This document defines the Translator requirements for such registrations.

Translator components are registered using OpenAPI metadata as described [here](https://smart-api.info/guide).   In addition to those general requirements, specific additions are required for Translator components.

## Requirements

### Translator tag

"translator" must be included as a top-level OpenAPI tag.

### x-translator element

The registration must contain a compliant x-translator element as described [here](https://github.com/NCATSTranslator/translator_extensions/).  Among other things, the x-translator element defines the infores identifier for this component: an identifier that is common across environments such as dev, test, and production.

### x-trapi element

If the component implements a TRAPI interface, then the registration must contain a compliant x-trapi element as described [here](https://github.com/NCATSTranslator/translator_extensions/).

### Uptime checking

Registrations must enable uptime checking as described [here](https://smart-api.info/faq#api-monitor).

### Granularity

There must be exactly one registration per (infores,TRAPI version) pair.  If multiple x-maturity levels exist for the same (infores, TRAPI version) pair, they must all come under a single registration with multiple server elements.

### Server Count

There must exist only one server per (infores, x-maturity).  It is not permitted, for instance, to register two dev versions of the same infores component.

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

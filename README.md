# TranslatorArchitecture

## Process

This repository tracks the decision making for the Translator architecture.

This README documents the current strawman architecture.  Changes must be made via pull requests.   Questions or discussion around a topic that is not easily related to a specific pull request occurs in github issues.

## Definitions

  * Message: A Message object as defined in the Translator Reasoner API [here](https://github.com/NCATS-Tangerine/NCATS-ReasonerStdAPI/blob/master/API/TranslatorReasonersAPI.yaml#L88)
  * KP (Knowledge Provider): a Translator software component, not a project team
  * ARA (Automated Relay Agent): a Translator software component, not a project team
  * KS (Knowledge Source): a non-Translator source of information that can be ingested to produce a KP.

## Architecture Principles

1. The goal is to create a single integrated product from federated services and data
2. Which components communicate with one another?
    1. ARS broadcasts query (Message) to one or more ARAs
    2. ARAs respond to ARS with Message
    3. ARA sends query messages to KPs
    4. KPs respond to ARAs with Message
3. Interfaces:
   1. All communication between the ARS and ARAs conforms to the ReasonerAPI Message spec
   2. KP can expose their information using these methods:
      1. ReasonerAPI Message
      2. Any SmartAPI-annotated interface
      3. A file dump conforming to KGX standards
   3. The Translator consortium will develop tools to automatically 
      1. proxy ReasonerAPI calls to smartAPI calls and 
      2. deploy ReasonerAPIs of KGX file dumps 
   4. Subsequent requirements on KPs in this document will specify their application to ReasonerAPI, SmartAPI, and/or KGX interfaces.
4. Entities in any ReasonerAPI message (ARS/ARA or ARA/KP) or KGX file-based communication are represented using compact URIs (CURIES), which must be expandable to full IRIs using a biolink-model provided json-ld context file.  Entities returned from a non-ReasonerAPI smartAPI-registered KP must provide sufficient information in the registry to allow an automated conversion of the entity identifier to a biolink-model CURIE.
5. Node Identifiers
   1. KPs must expose machine readable information about the types of node identifiers that they consume and produce.
   2. ARAs or other integration tools such as KGX will perform node identifier equivalence translations.
   3. The consortium will produce or adopt equivalent id sets, which will be shared across Translator tools.  Multiple Translator teams will contribute expertise to these sets, but that expertise will produce centralized results.
   4. SRI will provide tools for disseminating these equivalent identifiers, drawing on the prior work of multiple Translator teams.
6. ARAs and KPs may both score answers (provide scores in the message); ARAs are required to score answers.
7. KPs should not call other KPs.
8. ARAs obtain biomedical data only via KPs (or other ARAs), not from locally-cached aggregated graphs or non-Translator data sources.
9. Aggregated graphs must be created at the consortium level and exposed as a KP.
10. Components that do not fulfill the responsibilities of KPs and ARAs can still be stand-alone elements of the architecture to provide particular functionality; such tools will use the Translator ReasonerAPI whenever possible.
11. Answer persistence will be the responsibility of the ARS.
12. A system-wide UI will (eventually) exist, and will allow users to interpret answers, and reformulate questions.
13. A Translator Registry will expose programmatically accessible metadata about KPs and ARAs, and will provide testing and reports as part of a continuous integration framework.
    1. All KPs must be registered in the Translator Registry
    2. KPs must expose machine-readable metadata describing the node and edge types that they provide, initially via a /predicates endpoint
    3. KPs must expose machine-readable metadata describing the operations that they implement.  The method for exposing this metadata may be dependent upon the interface method (SmartAPI, ReasonerAPI, or KGX file).
    4. Non-KP, Non-ARA components will also be collected in the registry, in a manner yet to be determined.
14. Both KPs and ARAs should acquire and transmit provenance information to the fullest possible extent.

## Diagram

![ArchitectureDiagram](Architecture.png)

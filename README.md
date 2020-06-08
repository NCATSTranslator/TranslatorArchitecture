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
3. All communication between components conforms to the ReasonerAPI Message spec
4. All nodes and edges in all messages (query and response) are normalized
    1. SRI will provide tools for normalization
    2. KPs must use https://nodenormalization-sri.renci.org/apidocs/ and https://edgenormalization-sri.renci.org/apidocs/
5. ARAs and KPs may both score answers (provide scores in the message); ARAs are required to score answers
6. KPs should not call other KPs.
7. ARAs obtain biomedical data only via KPs (or other ARAs), not from locally-cached aggregated graphs or non-Translator data sources.
8. Aggregated graphs must be created at the consortium level and exposed as a KP.
9. Components that do not fulfill the responsibilities of KPs and ARAs can still be stand-alone elements of the architecture to provide particular functionality; such tools will use the Translator Message API whenever possible.
10. Answer persistence will be the responsibility of the ARS
11. A system-wide UI will (eventually) exist, and will allow users to interpret answers, and reformulate questions.
12. A Translator Registry will expose programmatically accessible metadata about KPs and ARAs, and will provide testing and reports as part of a continuous integration framework.
    1. All KPs must be registered in the Translator Registry
    2. KPs must expose machine-readable metadata describing the node and edge types that they provide, initially via a /predicates endpoint
    3. Non-KP, Non-ARA components will also be collected in the registry, in a manner yet to be determined.
13. Both KPs and ARAs should acquire and transmit provenance information to the fullest possible extent

## Diagram

![ArchitectureDiagram](Architecture.png)


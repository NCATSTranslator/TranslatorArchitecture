# Registry Metadata

## Introduction

We have 3 data exchange mechanisms as described in the [Architecture Principles](README.md).  In as much as possible, we want to create the same metadata for each method, and expose that metadata through a common query device: The SmartAPI registry.   The SmartAPI contains a metaKG that describes the edges that each service can provide.   Most metadata relates to services that are going to expose information via the metaKG.  Below, these methods are abbreviated as S (for SmartAPI), T (for Translator Reasoner API), and G (for Knowledge Graph Exchange).  

Additionally, the registry will contain information about services, such as ARAs, that do not expose edges via the metaKG.  ARAs also implement the TRAPI interface, but will not expose the same metadata as KP-related TRAPI interfaces.  These ARA-style interfaces are abbreviated below as A (for ARA).

This document describes, broadly, the particular metadata that Translator data exchange methods must or should expose. The technical details of how this metadata is specified is located [here](https://github.com/NCATSTranslator/translator_extensions) for SmartAPI-type interfaces, [here](https://github.com/NCATSTranslator/ReasonerAPI) for Translator Reasoner APIs,  [here](https://github.com/biolink/kgx) for Knowledge Graph Exchange file formats and [here](https://github.com/NCATSTranslator/Knowledge_Graph_Exchange_Registry) for the Knowledge Graph Exchange Registry.  

## Change Management

Changes to this document must be made via pull requests.   Questions or discussion around a topic that is not easily related to a specific pull request occurs in github issues.

## Provider Metadata

1. Access information (STGA) 
1. Operations (TA)
1. Request and Response structure (S)

## Content Metadata

1. Node types (STG)
1. Predicates (STG)
1. Identifier schemes (STG)
1. Evidence / Provenance (STG)
1. Node Properties (STG)
1. Edge Properties (STG)
1. Global Statistics (G)

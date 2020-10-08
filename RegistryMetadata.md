# Registry Metadata

## Introduction

We have 3 data exchange mechanisms as described in the [Architecture Principles](README.md).  In as much as possible, we want to create the same metadata for each method, and expose that metadata through a common query device: The SmartAPI registry.   Below, these methods are abbreviated as S (for SmartAPI), T (for Translator Reasoner API), and G (for Knowledge Graph Exchange).  

This document describes, broadly, the particular metadata that Translator data exchange methods must or should expose. The technical details of how this metadata is specified is located [here]() for SmartAPI-type interfaces, [here]() for Translator Reasoner APIs, and [here]() for Knowledge Graph Exchange files.  

## Change Management

Changes to this document must be made via pull requests.   Questions or discussion around a topic that is not easily related to a specific pull request occurs in github issues.

## Metadata

1. Access information (STG) 
1. Node types (STG)
1. Predicates (STG)
1. Subject_node_type; predicate; object_node_type [count] (STG)
1. Identifier schemes (STG)
1. Evidence / Provenance (STG)
1. Node Properties (STG)
1. Edge Properties (STG)
1. Operations (T)
1. Request and Response structure (S)
1. Global Statistics (G)
1. A GUID (DOI?) for the backing data (STG)

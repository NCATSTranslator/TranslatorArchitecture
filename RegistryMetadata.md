# Registry Metadata

## Introduction

We have 3 data exchange mechanisms as described in the [Architecture Principles](README.md).  In as much as possible, we want to create the same metadata for each data exchange mechanism and expose that metadata through a common query device: the SmartAPI registry.   The SmartAPI Registry currently contains a metaKG that describes the associations between biomedical entities (also referred to as edges) that each knowledge provider (KP) can provide, how to query the KP's API to retrieve those associations, and (on a basic level) how to parse the API response (JSON) and registry metadata to get information in a standard format.  

We plan to build on this current structure by describing the operations Translator services (KPs, autonomous relay agents (ARAs), etc.) can do.  This will likely depend on their data exchange mechanism and what kind of operation is done.  We therefore annotate the tasks below the following way:  
* KP APIs with the SmartAPI data exchange mechanism (S)
* KP APIs with the Translator Reasoner API data exchange mechanism, TRAPI (T)
* KP files using Knowledge Graph eXchange data exchange mechanism, KGX (G)
* ARA APIs using TRAPI (A).

This document describes, broadly, the particular metadata that Translator data exchange methods should expose.  The technical details of how this metadata is specified is located [here](https://github.com/NCATSTranslator/translator_extensions) for SmartAPI-type interfaces, [here](https://github.com/NCATSTranslator/ReasonerAPI) for Translator Reasoner APIs, [here](https://github.com/biolink/kgx) for Knowledge Graph Exchange file formats, and [here](https://github.com/NCATSTranslator/Knowledge_Graph_Exchange_Registry) for the Knowledge Graph Exchange Registry.  

## Change Management

Changes to this document must be made via pull requests.   Questions or discussion around a topic that is not easily related to a specific pull request occurs in github issues.  

## Metadata Overview

There is a metadata section for each operation a service provides.  For a KP, the retrieval of associations between particular biomedical entity types may be described with multiple operations: one operation for each unique combo of input-node-type, predicate, provenance, and output-node-type. 

### API-level Metadata
See the x-translator extension [here](https://github.com/NCATSTranslator/translator_extensions/blob/main/x-translator/smartapi_x-translator_schema.json) for current Translator-specific, API-level metadata.  Each API registry file has one x-translator object with the following fields:  

1. Component (KP, ARA, ARS, Utility). Required.
2. Team (list of one or more names of Translator teams directly involved in creating this API). Required.

### Provider Metadata
Used to facilitate querying the API.  

1. Supports Batch (STA): whether this API supports batch querying for this operation.
   - Required
   - (S) currently uses SmartAPI / OpenAPI specification and x-bte-kgs-operation extension (new x-bte extension is reorganized to put extension's info under /queryInfo)
2. Access / authentication information (STGA): credentials to access API/Service
   - Optional
   - Info that may be useful: [SmartAPI / OpenAPI specification](https://swagger.io/docs/specification/authentication/)
3. Request structure (S): used to set up API call.
   - Required
   - (S) currently uses SmartAPI / OpenAPI specification and x-bte-kgs-operation extension (new x-bte extension is reorganized to put extension's info under /queryInfo)
4. Response structure (S): used for basic parsing of API (JSON) response.
   - Required
   - Currently handled using SmartAPI / OpenAPI specification and x-bte-kgs-operation  / x-bte-response-mapping extensions in registry files
   - new x-bte extension (S) is refactored to handle this within Content Metadata, see below

### Content Metadata
More information on what the API operation requires and how to parse the API (JSON) response.

#### Required

1. Operation type (STGA):  
   - Enum (short list of accepted strings)
   - potential options:
      - association-retrieval (most KP operations fall into this category)
      - overlay (KP / ARA operations adding scores as node / edge properties to KG input)
      - node normalization (utility to match string names to CURIEs, convert between ID spaces)
      - edge normalization (utility to match outside-predicates to biolink relation hierarchy)
      - KG dump (utility to provide KG dump files)    
2. Inputs (STA): describe API operation input type  
   - (S) currently uses x-bte-kgs-operations/inputs: a list of objects (ID namespace prefix, biolink semantic class), one object per input node type
   - new x-bte extension (S) builds on x-bte-kgs-operations/inputs and adds:    
      - requiresPrefix: whether the API requires node CURIEs (prefix:ID) or just the IDs    
3. Outputs (STA): describe API operation output type and corresponding JSON response field  
   - (S) currently uses x-bte-kgs-operations/outputs (like inputs described above, for output node(s)) and x-bte-kgs-operations/response_mapping (JSON response field for output node IDs)
   - new x-bte extension (S) is refactored. It has /outputs as a list of objects. Each object describes an output node type:
      - ID namespace prefix
      - biolink semantic class
      - responseField: JSON response field for output node IDs
      - containsPrefix: whether the JSON response contains node CURIEs (prefix:ID) or just the IDs      
4. Node types (G): list ID namespaces and biolink semantic classes of nodes in the KG
5. Global statistics (G)   
6. Predicates / Relations (STG):  
   - Required for association-retrieval operations
   - (G) may be a list of biolink predicates, relations in the KG file
   - new x-bte extension (S) has /predicateInfo, which gives where to find and how to parse the relationship between biomedical entities. These fields can provide static values (set in the registry metadata), the specific JSON response field for the values, or code for setting the values in a more complex way (mapping or calling services).  The fields are:
      - biolink: predicate from [biolink related_to hierarchy](https://biolink.github.io/biolink-model/docs/related_to.html)  
      - id: predicate from [outside vocabularies / ontologies (OWL ObjectProperty)](https://biolink.github.io/biolink-model/docs/relation) that best describes the association.  Assigned by Translator team member or found in the original data.
      - label: human-readable label for the id field above  
      
#### Optional but recommended / use as-needed      
      
1. References (ST): supporting publications and webpages.
   - Optional but strongly recommended for association-retrieval operations
   - (G) may be a list of the edge properties that are references, and what ID namespaces they use
   - new x-bte extension (S) has /references, which gives where to find and how to parse the references of the retrieved associations.  These fields can provide the specific JSON response field for the values or code for setting the values in a more complex way (mapping or calling services).  The fields are:
      - publications: object where keys are the ID namespace (prefix) and values are the publication ID(s)
      - websites: object that provides the instructions, code, template for creating the webpage URL strings   
2. Provenance (STG): where the association came from and how it was made  
   - Optional but strongly recommended for association-retrieval operations
   - new x-bte extension (S) has /provenance.  This can be static (set in the registry metadata) or can be set a more complex way (mapping or calling services).  The final data is a list of objects, where each object describes a source of the association:
      - name and sourceType are required fields.
      - version, versionType, method, sourceReferences, descriptiveInfo, sourceContext are optional fields
3. Numeric Measures (ST): numeric variables associated with the association   
   - Optional (used as-needed) for association-retrieval operations
   - new x-bte extension (S) has /numericMeasures.  It's a list of objects, where each object describes a numeric measure:
      - name, responseField, directionMeaning are required fields.
      - ontologyTerm, missingValueMeaning, measureReferences, range, units are optional fields   
4. Categorical Measures (ST): categorical variables associated with association   
   - Optional (used as-needed) for association-retrieval operations
   - new x-bte extension (S) has /categoricalMeasures.  It's a list of objects, where each object describes a categorical measure:
      - name, responseField, categories are required fields.
      - ontologyTerm, missingValueMeaning, measureReferences, directionInfo are optional fields      
5. Context/Relevance: information that restricts the use or interpretation of the output   
   - Optional (used as-needed) for association-retrieval operations
   - new x-bte extension (S) has /contextRelevance, which gives where to find and how to parse context/relevance info of the retrieved associations.  It can provide static values (set in the registry metadata), the specific JSON response field for the values, or code for setting the values in a more complex way (mapping or calling services).  The final data is an object, where keys are types of context (taxon, species, experimental, disease) and values are CURIEs describing the context (e.g. the NCBITaxon CURIE of the species that the association is specific to).
  
### Unresolved issues:   
- handling node properties. Perhaps include in metadata for operations that focus on node properties (overlay, scoring, ARA operations), and exclude for other operations (like association-retrieval).
- handling association/edge properties that don't fit in the categories above (references, provenance, numeric measures, categorical measures, context/relevance)
   * some properties may be specific to the way the associations were made (e.g. using NLP or from clinical data)
   * is standardized registry metadata needed for the parsing / interpretation of those properties?
- how to handle contextRelevance
   * when a context-related parameter must be set in the query for association-retrieval, specifying where to get the context value from, how to set up the query
   * when an API operation is restricted in input (e.g. only valid inputs are cancer disease IDs) or output (all associations were made using NLP on coronavirus-infection-related literature), specifying this in the registry metadata. Perhaps ARAs / ARS can then use this info to decide what APIs to call
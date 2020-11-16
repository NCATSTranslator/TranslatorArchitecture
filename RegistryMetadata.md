# Registry Metadata

## Introduction

We have 3 data exchange mechanisms as described in the [Architecture Principles](README.md).  In as much as possible, we want to create the same metadata for each data exchange mechanism and expose that metadata through a common query device: the SmartAPI registry.   The SmartAPI Registry currently contains a metaKG that describes the associations (also referred to as edges) that each knowledge provider (KP) can provide, how to query the KP's API to retrieve those associations, and (on a basic level) how to parse the API response (JSON) to extract information from the associations.   

We plan to build on this current structure by describing the tasks Translator services (KPs, autonomous relay agents (ARAs), etc.) can do. This will likely depend on their data exchange mechanism and what kind of task is done. We therefore annotate the tasks below the following way: 
* KP APIs with the SmartAPI data exchange mechanism (S)
* KP APIs with the Translator Reasoner API data exchange mechanism, TRAPI (T)
* KP files using Knowledge Graph eXchange data exchange mechanism, KGX (G)
* ARA APIs using TRAPI (A).

This document describes, broadly, the particular metadata that Translator data exchange methods must or should expose. The technical details of how this metadata is specified is located [here](https://github.com/NCATSTranslator/translator_extensions) for SmartAPI-type interfaces, [here](https://github.com/NCATSTranslator/ReasonerAPI) for Translator Reasoner APIs,  [here](https://github.com/biolink/kgx) for Knowledge Graph Exchange file formats and [here](https://github.com/NCATSTranslator/Knowledge_Graph_Exchange_Registry) for the Knowledge Graph Exchange Registry.  

## Change Management

Changes to this document must be made via pull requests.   Questions or discussion around a topic that is not easily related to a specific pull request occurs in github issues.

## Metadata Overview

There is a metadata section for each task a service provides.  For a KP, returning associations could be multiple tasks: one task for each unique combo of input-node-type, predicate, provenance, and output-node-type. 

### Service Metadata
See the x-translator extension [here](https://github.com/NCATSTranslator/translator_extensions/pull/1) for a potential starting point. One for each Service.
1. Service Type (KP, ARA, ARS, SRI_Service). Required. 
1. Translator Team (list of one or more Translator teams directly involved in creating this service). Required. 

### Level-0 Content Metadata
Only used to facilitate metaKG querying and support for doing the task.  One per task within a Service.  

1. Supports Batch (STA): whether batch tasks/querying can be done for this specific task. Required. 
1. Request structure (S): used to set up API call. Required. 
1. Response structure (S): used to parse API response. Required. 
1. Access information (STGA): used to access API/Service, if needed. Optional.   

### Level-1 Content Metadata
Other information the task needs as input or the task returns.  One per task within a Service.  

1. Task type (STGA). Short list of accepted strings. Required. Potential options:
    * "one-hop query for associations" between nodes/node-types (most knowledge providers do this)
    * "overlay" (some KPs can take a knowledge graph input and add node/edge properties (weights/scores))
    * "node normalization" (other services to find IDs from plain strings, convert between ID spaces)
    * "edge normalization"    
    * "transfer entire KG as a file" to another Service
    
1. Inputs (STA) 
    * Proposed: describe node type(s) (ex: TRAPI message graph, biolink model DiseaseOrPhenotypicFeature) and ID space(s), if applicable, for valid inputs. 
1. Outputs (STA) 
    * Proposed: describe node type(s) (ex: TRAPI message graph, biolink model DiseaseOrPhenotypicFeature) and ID space(s), if applicable, for valid outputs. 
1. Node types (G)
    * Proposed: describe type(s) (ex: biolink model DiseaseOrPhenotypicFeature) and ID space(s) for nodes within the KG. 
    
1. Node Properties (STG): list of properties the task may return in its response's nodes (ST); OR list of all node properties inside the file, organized by node type (G).  
    * Issue: all or just the ones that don't fit into Provenance, Measures, Context/Relevance below?  
    * Whether or not the service can take a node property as a parameter to the task done is described in Parameters, below.
1. Edge Properties (STG): list of properties the task may return in its response's edges (ST); OR list of all edge properties inside the file, potentially organized by some kind of edge type/predicates/relations/provenance/context/relevance (G). 
    * Issue: all or just the ones that don't fit into Provenance, Measures, Context/Relevance below?  
    * Whether or not the service can take an edge property as a parameter to the task done is described in Parameters, below.     
1. Predicates/Relations (STG). 
    * Proposed but very likely to be revised: object containing the predicate and relation for the association that the task returns, either as a node or an edge (with a task defined as having only one predicate and one relation) (ST); or list of predicate-relation pairs that are present in associations/edges in the KG file (G). 
    * Predicates are from the [biolink related_to hierarchy](https://biolink.github.io/biolink-model/docs/related_to.html); relations are more specific descriptions of the association, from outside vocabularies, see [here](https://biolink.github.io/biolink-model/docs/relation). 
    * Whether or not the service can take a node property as a parameter to the task done is described in Parameters, below.     
1. Provenance (STGA): list of properties the task may return in its response that are related to provenance, organized by whether they are attached to nodes, edges, graphs (as input or output), or Services. What entities have provenance, and how that provenance is described (through schema) may vary quite a bit between task types/STGA. Ideally all conform to a basic kind of schema/naming-convention so that they are easier for downstream Services to process.  
    * Whether or not the service can take an provenance-related property as a parameter to the task done is described in Parameters, below.     
1. Measures (STGA): list of the properties that provide some kind of measure for the association/edge or node and metadata for that measure. This may vary quite a bit between task types and ST, G, and A. Whether or not the service can take an provenance-related property as a parameter to the task done is described in Parameters, below.  
     * However, metadata for a measure should include (1) its name, (2) a standard_label (for example, measures from various Services may be scores for an association, so they would all have the standard_label "association_score"), (3) range or full list of category values (ordered if applicable), (4) direction information (ex: what it means if a value is negative, if the category list is read from first-entry to last-entry). 
     * Other information could include the ontology_term that the measure maps to and linkouts to more information on the measure and how it was calculated (webpages or publications). 
    * Examples: responses from an A task may return nodes with properties describing whether a node is an answer or not, the scoring for answer nodes, and information about the scoring (range, direction of better score).  
    * Examples: Tasks from ST may return edges with properties describing numeric and category measures of the association. These could be related to how specific this association is between this pair of nodes, how often the nodes in this association co-occur in literature, how much evidence there is for this association, etc.
    * Examples: G may provide the properties on nodes and edges corresponding to numeric and category measures. 
1. Context / Relevance (STGA): list of properties the task may return in its response that are related to context/relevance needed to interpret the response.
    * Proposed: disease / species / experimental setup / cohort that this task's response is specific to 
1. Parameters (STA). Would list input parameters for the task that the user can change to adjust the task. The parameters themselves can be required or optional. 
    * May want to include options or a suggested range of values. May want to include preset default values. 
    * Examples: predicate/relation, node properties, edge properties, maximum number of final results, whether to expand the input's values to include child nodes (using an ontology) or super/sub-predicates, and a disease / species/ cohort /experimental setup context 
1. Constraints (STA). Would describe Service parameters/fields that are set and the user cannot change them. 
    * some APIs may be require fields to have certain values in order to do the specific task 
    
1. Global Statistics (G).    
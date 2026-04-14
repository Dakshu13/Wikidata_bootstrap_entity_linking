# Wikidata_bootstrap_entity_linking
Wikidata Bootstrap: Simple Entity Linking
A hands-on NLP pipeline that bootstraps a local knowledge base from Wikidata and links named entities in text to structured Wikidata URIs — built during the Entity Linking Industry Lab hosted by Quantexa at Dublin City University.
---
What is Entity Linking?
Given a piece of text, automatically identify mentions of people, places, or things and link each mention to a unique identifier in a structured knowledge base (in this case, Wikidata URIs).
Example:
```
Input:  "Mary Lou addressed supporters in Dublin."
Output: "Mary Lou" → https://www.wikidata.org/wiki/Q234456
```
---
Pipeline Overview
The notebook walks through a progression from a simple baseline to a full vector-retrieval system:
```
SPARQL Query → Wikidata KB → Surface-Form Index → Text Annotation
                                     ↓
                            Alias Expansion
                                     ↓
                          Vector Retrieval (sentence-transformers)
                                     ↓
                          Agentic LLM Linker (template)
```
Stage 1 — Bootstrap a local knowledge base
Load a SPARQL query from a `.sparql` file
Fetch matching entities from the Wikidata endpoint
Store as a lightweight local KB (list of `{uri, label}` dicts)
Stage 2 — Build a surface-form index
Map entity labels to their Wikidata URIs
Enables fast exact-string lookup during annotation
Stage 3 — Annotate text
Run `annotate_text()` over any input string
Returns spans with matched Wikidata URIs
Stage 4 — Alias expansion
Add manual or programmatic aliases per entity (e.g. "Mary Lou" → `Q234456`)
Rebuild the index and re-annotate
Stage 5 — Vector retrieval
Swap in a real embedding provider (`all-MiniLM-L6-v2` via `sentence-transformers`)
Index all KB entities as dense vectors
Retrieve top-k candidates by cosine similarity for fuzzy/partial mentions
Stage 6 — Agentic linker template
Build a prompt combining mention context + candidate list
Plug in any LLM backend to perform context-aware disambiguation
---
Default Domain
The default SPARQL query targets living people currently holding a political office in Ireland.
To switch domain, edit the `.sparql` file or point `query_path` at a new one and rerun. No other code changes needed.
---
Getting Started
Prerequisites
```bash
pip install sentence-transformers
```
Run the notebook
```bash
jupyter notebook 01_wikidata_bootstrap_entity_linking.ipynb
```
Project structure
```
├── src/
│   └── wikidata_lab/
│       ├── wikidata.py                       # Core KB + annotation logic
│       ├── candidate_retrieval.py            # Vector store template
│       ├── sentence_transformer_embeddings.py # Real embedding provider
│       └── agentic_linking.py                # LLM linker template
├── data/
│   └── example_current_irish_office_holders.txt
├── queries/
│   └── *.sparql                              # SPARQL query files
└── 01_wikidata_bootstrap_entity_linking.ipynb
```
---
Exercises
The notebook includes suggested extensions to try:
Change the SPARQL query to a new domain (e.g. Irish musicians)
Add alias expansion (ASCII variants, shortened names, party titles)
Improve the spotter to handle punctuation and accents
Add a better candidate retrieval stage
Plug in an LLM backend for context-aware disambiguation
Create a small evaluation set and score your linker
---
Key Concepts Demonstrated
Concept	Implementation
Knowledge graph querying	SPARQL against Wikidata endpoint
Named entity linking	Surface-form index + exact match
Alias expansion	Manual + programmatic extra surface forms
Dense vector retrieval	`sentence-transformers` (`all-MiniLM-L6-v2`)
Agentic NLP pipeline	Candidate retrieval → context extraction → LLM prompt
Domain adaptation	Swap SPARQL file, zero code changes
---
Acknowledgements
Built during the Entity Linking Industry Lab hosted by Quantexa at Dublin City University. Thanks to Lauren Cassidy and Chris Hokamp for designing and delivering the lab, and Anya Belz for organising it.

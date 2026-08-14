Notes on RAG


# Chunking

## Methods

### Fixed-size chunking. 
Fixed-size chunking cuts the text every N characters or tokens. It's simple and fast, but has no awareness of context or structure — which means it splits sentences and paragraphs mid-idea. An overlap parameter counteracts this: it gives a cut-off idea a second chance to appear intact somewhere. It's commonly used as a baseline.

### Recursive / structure-aware chunking.
Instead of cutting at an arbitrary character count, recursive chunking splits along more natural, meaningful boundaries. It tries to break on paragraphs first, then on sentences, then on words, falling back through the list until each piece fits the size limit. This respects the text's structure and keeps ideas whole far more often than fixed-size chunking.

### Document-structure / layout-aware chunking.
his method differs from the previous ones by splitting along the actual structure of the document such as markdown headers, PDF sections, HTML tags, code functions. The heading hierarchy is often attached to each chunk as metadata, giving a richer representation of where the chunk came from (e.g. "Chapter 3 > Installation > Requirements"). Ideal for well-structured documents like technical docs, contracts, or code; much less useful on plain text with no markup.

### Sentence-window / small-to-big retrieval.
Combines both worlds: it indexes small units (single sentences) so retrieval stays precise, but when a sentence is retrieved it feeds the LLM a wider window around it — the neighboring sentences or the parent section. Small chunks retrieve accurately; large chunks give the model enough context to answer.

### Semantic chunking
Rather than splitting on structure or length, it splits on meaning shifts where consecutive sentences stop being similar to each other. Each chunk ends up as one coherent thought, regardless of its length. It's more expensive, since you run embeddings just to decide where to cut, but it can be worth trying when chunk quality matters more than indexing speed.

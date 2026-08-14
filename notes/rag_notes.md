Notes on RAG


# Chunking

## Methods

### Fixed-size chunking. 
Fixed-size chunking cuts the text every N characters or tokens. It's simple and fast, but has no awareness of context or structure — which means it splits sentences and paragraphs mid-idea. An overlap parameter counteracts this: it gives a cut-off idea a second chance to appear intact somewhere. It's commonly used as a baseline.

### Recursive / structure-aware chunking.
Instead of cutting at an arbitrary character count, recursive chunking splits along more natural, meaningful boundaries. It tries to break on paragraphs first, then on sentences, then on words, falling back through the list until each piece fits the size limit. This respects the text's structure and keeps ideas whole far more often than fixed-size chunking.

![RAG Workflow](https://github.com/giKallergis/RAG_intro/blob/main/notes/rag_pipeline_workflow.png)


# 1. Chunking
Splitting documents into smaller passages before embedding, because retrieval and LLMs work better on focused pieces than whole documents. The main tradeoff is size: small chunks retrieve precisely but may lack context; large chunks carry more context but dilute relevance. Overlap (repeating a bit of text between adjacent chunks) reduces damage at boundaries. Methods, from crudest to most refined: fixed-size, recursive/structure-aware, layout-aware, sentence-window (small-to-big), semantic, and LLM-based. 

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

# 2. Metadata to every chunk. 
As you chunk, tag each one with source, section/heading, and any date. This is nearly free at index time and near-impossible to add well later. It's the thing people most regret skipping, because it's what enables filtering and citations downstream.


# 3. Embedding
A model reads a chunk and outputs a fixed-length vector — a high-dimensional representation of its meaning. Texts with similar meaning end up close to each other in this representation space. One hard rule: the model you embed chunks with must be the same model you embed queries with, otherwise the vectors aren't comparable. A well-regarded general-purpose embedding model is the usual choice.

The main decisions here are: local vs. API embedding models, the embedding dimension, and general vs. domain-specific models. A subtler concept is symmetric vs. asymmetric embeddings, which refers to the asymmetry in length between a short query and a longer document; some models are trained specifically for that query-to-passage case.

# 4. Store in vector database
Initially, similarity between the query vector and every chunk vector was computed and sorted, which fails at scale. In a vector database, each chunk is stored with three things: the embedding vector, the original chunk text, and the metadata. The vector is what you search by; the text is what you eventually hand to the LLM; the metadata is what you filter by. Vector databases use index structures to find the approximately closest vectors without checking all of them, thus, trading a bit of accuracy for a large speed gain. They also give persistence (add new chunks without re-embedding the whole corpus), metadata filtering, and incremental updates.

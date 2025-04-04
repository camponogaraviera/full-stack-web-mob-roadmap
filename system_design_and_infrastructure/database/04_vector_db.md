<div align='center'>
  <h1>Vector Databases</h1>
</div>

# Table of Contents

- [About](#About)
- [Motivation](#motivation)
- [How to Search/Query in a Large Database](#how-to-searchquery-in-a-large-database)

# About

"A vector database management system (VDBMS) or simply vector database or vector store is a database that can store vectors (fixed-length lists of numbers) along with other data items. Vector databases typically implement one or more Approximate Nearest Neighbor (ANN) algorithms, so that one can search the database with a query vector to retrieve the closest matching database records." —Wikipedia.

# Motivation

Unstructured data such as image files can be stored in either a SQL or NoSQL database as per their:

1. Binary data: raw image data (pixels) are actually stored in a data lake (e.g., AWS S3) and only the URL location stays in the database.

2. Metadata: image metadata (e.g., title, URL location, etc.) is stored in database tables.

3. Tags: used to separate semantic elements in the image (e.g., colors, textures, patterns, objects) are stored in database tables and used for filtering.

However, querying images with similar characteristics pose a problem. A SQL query such as `select * where color = green` does not capture the multidimensional features of the data.

Therefore, a `vector database` (e.g., FAISS) can be used to represent each data sample as a vector embedding and `used to store and index vector embeddings` of images.

# How to Search/Query in a Large Database

Searching over a dataset with 10M+ embeddings can be exhaustive if the query embedding (query vector) has to be compared to every other embedding (vector) in the dataset. In this scenario, a vector database must be used.

- Vector embeddings for similarity search can be generated using the following deep learning architectures:
    - `Image embeddings`: Autoencoder (dimensionality reduction), ResNet, EfficientNet, VGG, and [Contrastive Language-Image Pretraining (CLIP)](https://openai.com/index/clip/) (for both image-to-image and text-to-image searches).
    - `Text embeddings`: 
        - BERT for text classification, hidden words prediction, and document categorization.
        - FCNN for absolute positional embedding in GPT.

- [FAISS](https://github.com/facebookresearch/faiss) is a popular library for `computing similarity between vectors` that are identified by an integer index. `Similar vectors have the lowest L2 distance and the highest dot product`. 

- Searching for similar vectors in high-dimensional vector databases can be achieved using `indexing techniques` for `Approximate Nearest Neighbor Search (ANN)` algorithms, such as [Hierarchical Navigable Small World (HNSW)](https://en.wikipedia.org/wiki/Hierarchical_navigable_small_world) or [Inverted File with Flat Index (IVFFlat)](https://github.com/pgvector/pgvector?tab=readme-ov-file#ivfflat) (used in [PostgreSQL's pgvector extension](https://github.com/pgvector/pgvector)).

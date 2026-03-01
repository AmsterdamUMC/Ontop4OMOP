# System Architecture

## CLI Mode

``` mermaid
flowchart LR
    A[OMOP Parquet Files] --> B[Ontop CLI]
    B --> C[Virtual RDF Graph]
    C --> D[SPARQL Endpoint :8081]
```

## GraphDB Desktop Mode

``` mermaid
flowchart LR
    A[OMOP Parquet Files]
    B[DuckDB Database]
    C[Persistent SQL Views]
    D[GraphDB Desktop]
    E[Embedded Ontop]
    F[SPARQL Repository :7200]

    A --> B --> C --> E --> D --> F
```

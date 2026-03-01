---
authors:
- name: Rowdy de Groot
  orcid: 0000-0002-1248-1986
- name: Andra Waagmeester
  orcid: 0000-0001-9773-4008
bibliography: paper.bib
date: 2026
title: OMOP → SPARQL using Ontop and DuckDB
---

# Summary

This software exposes OMOP Common Data Model (CDM) data stored as
Parquet files as a SPARQL endpoint using Ontop and DuckDB. It avoids RDF
materialization and enables lightweight Virtual Knowledge Graph
deployment.

# Statement of Need

OMOP data is widely used but difficult to expose via SPARQL without
costly RDF transformation pipelines. This repository demonstrates a
reproducible, minimal solution.

# Architecture

Two deployment modes are supported:

1.  Ontop CLI with direct Parquet access.
2.  Embedded Ontop in GraphDB using persistent DuckDB SQL views.

# Reproducibility

The setup is cross-platform and requires only Java, Ontop, and DuckDB.

# License

MIT.

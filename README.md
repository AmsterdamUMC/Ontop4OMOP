# OMOP → Ontop → DuckDB (Parquet) SPARQL Endpoint

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Ontop Version](https://img.shields.io/badge/Ontop-5.x-blue)
![Java](https://img.shields.io/badge/Java-17%2B-orange)
![DOI](https://img.shields.io/badge/DOI-pending-lightgrey)

This repository provides a cross-platform, reproducible setup for
exposing OMOP CDM data stored in Parquet files as a SPARQL endpoint
using:

-   DuckDB (embedded)
-   Ontop (virtual RDF mapping)
-   OBDA mappings

The setup works on Windows, macOS, and Linux.

------------------------------------------------------------------------

## Overview

OMOP Parquet → DuckDB (embedded) → Ontop → SPARQL endpoint

No database server required. No OS-specific paths required. No
pre-registration of tables required.

DuckDB reads Parquet files directly using `read_parquet()`. Ontop
exposes the data virtually via SPARQL.

------------------------------------------------------------------------

## Quick Start

Clone the repository:

git clone https://github.com/AmsterdamUMC/Ontop4OMOP.git cd Ontop4OMOP

Start the endpoint (macOS / Linux):

/path/to/ontop endpoint\
--ontology=OMOP.ttl\
--mapping=OMOP.obda\
--properties=OMOP.properties\
--port=8081

Windows PowerShell:

path`\to`{=tex}`\ontop`{=tex}.bat endpoint \^ --ontology=OMOP.ttl \^
--mapping=OMOP.obda \^ --properties=OMOP.properties \^ --port=8081

Open: http://localhost:8081/sparql

------------------------------------------------------------------------

## Example SPARQL Query

SELECT (COUNT(?p) AS ?count) WHERE { ?p a
<http://www.ohdsi.org/omop#Person> }

------------------------------------------------------------------------

## Persistent Identifiers

Rowdy de Groot\
ORCID: https://orcid.org/0000-0002-1248-1986\
Affiliation (ROR): https://ror.org/05grdyy37

Andra Waagmeester\
ORCID: https://orcid.org/0000-0001-9773-4008\
Affiliation (ROR): https://ror.org/05grdyy37

------------------------------------------------------------------------

## License

MIT License

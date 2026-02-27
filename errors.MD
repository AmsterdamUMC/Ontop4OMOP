# OMOP → Ontop → DuckDB (Parquet) Debug Log

This document records the transition from a non-functional OMOP--Ontop
setup to a working DuckDB (Parquet-based) SPARQL endpoint.

------------------------------------------------------------------------

## 1️⃣ Invalid IRI Escape Error

Error: InvalidMappingException: IRI includes string escapes

Fix: Removed illegal backslash escape sequences in IRI templates.

------------------------------------------------------------------------

## 2️⃣ Windows JDBC Path on macOS/Linux

Error: Cannot open file ".../C:/Users/.../OMOP.duckdb"

Cause: Windows absolute path in OMOP.properties.

Fix: Use embedded DuckDB:

jdbc.url=jdbc:duckdb: jdbc.driver=org.duckdb.DuckDBDriver

------------------------------------------------------------------------

## 3️⃣ Cannot Find Relation "person"

Error: Cannot find relation "main"."person"

Cause: DuckDB started empty. Parquet files not registered as tables.

Fix: Query Parquet directly: SELECT person_id FROM
read_parquet('person.parquet')

------------------------------------------------------------------------

## 4️⃣ Default Prefix ':' Not Recognized

Error: The prefix name is unknown: :

Fix: Replace default prefix with explicit prefix:

ex: http://example.org/omop/

------------------------------------------------------------------------

## 5️⃣ Port Already in Use

Error: Port 8080 is already in use

Fix: Start on different port: --port=8081

------------------------------------------------------------------------

## 6️⃣ DuckDB Parser Error Near ';'

Error: Parser Error: syntax error at or near ";"

Cause: Semicolons in OBDA source SQL.

Fix: Remove all semicolons from source queries.

------------------------------------------------------------------------

## 7️⃣ JDBC Stability Improvement

Final robust SQL pattern:

SELECT column FROM read_parquet('file.parquet')

------------------------------------------------------------------------

# Final Result

Working pipeline:

OMOP Parquet → DuckDB (embedded) → Ontop → SPARQL endpoint

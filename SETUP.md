# Minimal Reproducible Setup (Windows / macOS / Linux)

This setup works on Windows, macOS, and Linux.

------------------------------------------------------------------------

## 1️⃣ Clone Repository

git clone `<REPOSITORY_URL>`{=html} cd `<REPOSITORY_FOLDER>`{=html}

------------------------------------------------------------------------

## 2️⃣ Requirements

-   Java 17+
-   Ontop CLI 5.x

Check Java:

java -version

------------------------------------------------------------------------

## 3️⃣ Directory Structure

omop-demo/ OMOP.obda OMOP.ttl OMOP.properties person.parquet
visit_occurrence.parquet condition_occurrence.parquet
drug_exposure.parquet measurement.parquet

All files must be in the same directory.

------------------------------------------------------------------------

## 4️⃣ OMOP.properties

jdbc.url=jdbc:duckdb: jdbc.driver=org.duckdb.DuckDBDriver

No absolute paths. No OS-specific separators.

------------------------------------------------------------------------

## 5️⃣ OBDA SQL Pattern

Use:

SELECT column FROM read_parquet('file.parquet')

Rules: - No semicolons - No default ':' prefix - Use explicit prefix
like ex:

------------------------------------------------------------------------

## 6️⃣ Start Ontop

macOS / Linux:

/path/to/ontop endpoint\
--ontology=OMOP.ttl\
--mapping=OMOP.obda\
--properties=OMOP.properties\
--port=8081

Windows PowerShell:

path`\to`{=tex}`\ontop`{=tex}.bat endpoint \^ --ontology=OMOP.ttl \^
--mapping=OMOP.obda \^ --properties=OMOP.properties \^ --port=8081

Endpoint: http://localhost:8081/sparql

------------------------------------------------------------------------

## 7️⃣ Test Query

SELECT (COUNT(?p) AS ?count) WHERE { ?p a
<http://www.ohdsi.org/omop#Person> }

If this returns a number, the setup works.

# GraphDB Desktop Tutorial

1.  Create DuckDB database and persistent SQL views.
2.  Install DuckDB JDBC driver into GraphDB `lib/ext` directory.
3.  Create an Ontop Virtual Repository.

Driver class: org.duckdb.DuckDBDriver

JDBC URL: jdbc:duckdb:/absolute/path/to/db/OMOP.duckdb


---

1. open Ontop cli
2. Run command: ontop materialize --mapping=OMOP.obda --properties=OMOP.properties --output=OMOP_RDF.ttl
3. Open GraphDB
4. Create a new GraphDB repository
5. Click on "import" on the left and click on "upload RDF files"
6. Pick the "OMOP_RDF.RDF" file that was just created
7. In the explore menu, click on "Visual graph"
8. Copy and past http://example.org/omop/person/1 in the search field and press "show" or enter to test if the data is correctly imported.

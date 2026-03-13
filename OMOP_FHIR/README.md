1. Download the ontology files in the RDF subsection from this website: https://fhir.hl7.org/fhir/downloads.html
2. Extract the files from the .zip file and place them in the same folder where your OMOP.obda and OMOP.properties files are.
3. Replace OMOP.obda with OMOP_FHIR.obda and OMOP.properties with the files from this folder. In OMOP.properties, replace the placeholder with the directory where OMOP.duckdb is.
4. Start Ontop with this new command:

Windows cli:
ontop endpoint ^
  --mapping=OMOP_FHIR.obda ^
  --ontology=fhir.ttl ^
  --ontology=rim.ttl ^
  --ontology=w5.ttl ^
  --ontology=OMOP.ttl ^
  --properties=input/OMOP.properties ^
  --port=8080

Mac cli:
ontop endpoint \
  --mapping=OMOP_FHIR.obda \
  --ontology=fhir.ttl \
  --ontology=rim.ttl \
  --ontology=w5.ttl \
  --ontology=OMOP.ttl \
  --properties=OMOP.properties \
  --port=8080


6. Run the testqueries from the SPARQL test query FHIR.txt file to check if it works.

# HemaFAIR OMOP — SPARQL Queries

These queries run against the Ontop SPARQL endpoint at `http://localhost:8081`.
Paste each query into the Ontop web UI query editor.

All filters use standard OMOP concept IDs. Human-readable labels are resolved at
query time via `rdfs:label` on concept nodes (also mapped to `omop:name` for ontology conformance).

**Start Ontop** (from the project root):
```
ontop endpoint \
  --ontology=ontology/OMOP.ttl \
  --mapping=hemafair/OMOP-postgresql.obda \
  --properties=hemafair/OMOP-postgresql.properties \
  --port=8081
```

---

## Prefixes

```
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd:          <http://www.w3.org/2001/XMLSchema#>
```

---

## Overview — Row counts across clinical tables

```sparql
PREFIX omop: <https://w3id.org/omop/ontology/>

SELECT ?table_name (COUNT(?x) AS ?row_count) WHERE {
  { ?x a omop:Person .              BIND("person"               AS ?table_name) }
  UNION
  { ?x a omop:VisitOccurrence .     BIND("visit_occurrence"     AS ?table_name) }
  UNION
  { ?x a omop:ConditionOccurrence . BIND("condition_occurrence" AS ?table_name) }
  UNION
  { ?x a omop:Measurement .         BIND("measurement"          AS ?table_name) }
  UNION
  { ?x a omop:Observation .         BIND("observation"          AS ?table_name) }
  UNION
  { ?x a omop:ProcedureOccurrence . BIND("procedure_occurrence" AS ?table_name) }
  UNION
  { ?x a omop:DrugExposure .        BIND("drug_exposure"        AS ?table_name) }
} GROUP BY ?table_name ORDER BY ?table_name
```

---

## Q1 — How many patients have each diagnosis?

Counts distinct patients per primary diagnosis concept.
Filters on the three thalassaemia/sickle cell concept IDs; label comes from the concept table.

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?diagnosis (COUNT(DISTINCT ?person) AS ?n_patients) WHERE {
  ?person omop:has_condition_occurrence ?condition .
  ?condition omop:has_concept ?concept .
  ?concept rdfs:label ?diagnosis .
  FILTER(?concept IN (omop_concept:4287844, omop_concept:4278669, omop_concept:25518))
} GROUP BY ?diagnosis ORDER BY DESC(?n_patients)
```

Expected result:
| diagnosis | n_patients |
|---|---|
| Beta thalassemia | 739 |
| Alpha thalassemia | 258 |
| Sickle cell trait | 2 |

---

## Q2 — What is the ferritin level by sex?

SPARQL does not have a MEDIAN aggregate, so two variants are provided.

### Q2a — Average ferritin by sex (SPARQL aggregate)

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?sex (AVG(?value) AS ?avg_ferritin) (COUNT(?meas) AS ?n_measurements) WHERE {
  ?person omop:has_measurement ?meas .
  ?meas omop:has_concept omop_concept:37208753 ;
        omop:value_as_number ?value .
  ?person omop:has_gender ?genderConcept .
  ?genderConcept rdfs:label ?sex .
} GROUP BY ?sex
```

### Q2b — Raw ferritin values with sex (for median computation elsewhere)

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?sex ?value WHERE {
  ?person omop:has_measurement ?meas .
  ?meas omop:has_concept omop_concept:37208753 ;
        omop:value_as_number ?value .
  ?person omop:has_gender ?genderConcept .
  ?genderConcept rdfs:label ?sex .
}
```

Reference median (from SQL): Female ≈ 885.4 · Male ≈ 764.3

---

## Q3 — Transfusion status distribution per diagnosis

Joins condition_occurrence (diagnosis) with observation (transfusion status).
Transfusion status label is resolved from `value_as_concept_id` via the concept table — no source strings used.

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?diagnosis ?transfusion_status (COUNT(DISTINCT ?person) AS ?n_patients) WHERE {
  ?person omop:has_condition_occurrence ?condition .
  ?condition omop:has_concept ?diagConcept .
  ?diagConcept rdfs:label ?diagnosis .
  FILTER(?diagConcept IN (omop_concept:4287844, omop_concept:4278669, omop_concept:25518))

  ?person omop:has_observation ?obs .
  ?obs omop:has_concept omop_concept:40758326 ;
       omop:has_value_as_concept ?valConcept .
  ?valConcept rdfs:label ?transfusion_status .
} GROUP BY ?diagnosis ?transfusion_status ORDER BY ?diagnosis ?transfusion_status
```

---

## Q4 — Most common comorbidities

Excludes the three primary diagnoses and unmapped concepts (concept_id = 0).

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?comorbidity (COUNT(DISTINCT ?person) AS ?n_patients) WHERE {
  ?person omop:has_condition_occurrence ?condition .
  ?condition omop:has_concept ?concept .
  ?concept rdfs:label ?comorbidity .
  FILTER(?concept NOT IN (omop_concept:4287844, omop_concept:4278669, omop_concept:25518, omop_concept:0))
} GROUP BY ?comorbidity ORDER BY DESC(?n_patients)
```

Percentage = n_patients / 999 × 100.

Expected top results:
| comorbidity | n_patients |
|---|---|
| Vitamin D deficiency | 605 |
| Osteopenia | 503 |
| Osteoporosis | 399 |

---

## Q5 — Patients on chelation with no transfusion history

Joins chelation procedures with transfusion observations, keeping only patients
whose recorded transfusion status is "No" — matching the original SQL inner join logic.

```sparql
PREFIX omop:         <https://w3id.org/omop/ontology/>
PREFIX omop_concept: <http://example.org/omop/concept/>
PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>

SELECT (COUNT(DISTINCT ?person) AS ?n_chelation_no_transfusion) WHERE {
  ?person omop:has_procedure_occurrence ?proc .
  ?proc omop:has_concept omop_concept:4068544 .
  ?person omop:has_observation ?obs .
  ?obs omop:has_concept omop_concept:40758326 ;
       omop:has_value_as_concept ?valConcept .
  ?valConcept rdfs:label "No" .
}
```

> If the count is unexpected, verify the exact concept labels used for transfusion
> status in this dataset:
> ```sparql
> PREFIX omop:         <https://w3id.org/omop/ontology/>
> PREFIX omop_concept: <http://example.org/omop/concept/>
> PREFIX rdfs:         <http://www.w3.org/2000/01/rdf-schema#>
> SELECT DISTINCT ?status WHERE {
>   ?obs a omop:Observation ;
>        omop:has_concept omop_concept:40758326 ;
>        omop:has_value_as_concept ?valConcept .
>   ?valConcept rdfs:label ?status .
> }
> ```

Expected result: 249

---

## Q6 — Measurement completeness

For each numeric measurement type, counts how many distinct patients have a recorded value.
Patients with no value do not appear in the virtual graph (NULL rows are excluded by the mapping),
so n_missing = 999 − n_with_value.

```sparql
PREFIX omop: <https://w3id.org/omop/ontology/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?measurement (COUNT(DISTINCT ?person) AS ?n_with_value) WHERE {
  ?person omop:has_measurement ?meas .
  ?meas omop:has_concept ?concept ;
        omop:value_as_number ?value .
  ?concept rdfs:label ?measurement .
} GROUP BY ?measurement ORDER BY DESC(?n_with_value)
```

Expected top results:
| measurement | n_with_value |
|---|---|
| Ferritin mass concentration in serum | 863 |
| Serum iron measurement | 15 |

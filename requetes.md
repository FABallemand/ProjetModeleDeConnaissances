# Projet Modèles de Connaissances et Web Sémantique

## Partie 2: Requêtes

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX ch: <http://www.semanticweb.org/fabien/ontologies/2022/11/AllemandCouture_Chansons#>

1.
SELECT ?a
    WHERE { ?a rdf:type ch:Album ; ch:contientChanson ch:Smells_Like_Teen_Spirit }

2.

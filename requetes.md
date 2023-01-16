# Projet Modèles de Connaissances et Web Sémantique

## Partie 2: Requêtes

PREFIX ch: <http://www.semanticweb.org/fabien/ontologies/2022/11/AllemandCouture_Chansons#>

1. 


2. 
SELECT ?a
    WHERE { ?a rdf:type ch:Album ; ch:contientChanson ch:Smells_Like_Teen_Spirit }

3. 
ASK { ?f rdf:type ch:Musicien ;
         ch:particpeDansGroupe ?p ;
         ch:aGenre ch:Femme .
      ?p ch:aGroupe ch:Garbage }

4. 
SELECT (count(distinct ?c))
    WHERE { ?c rdf:type ch:Chanson .
            ?c ch:estDansAlbum ?a .
            ?a ch:publiePar ch:Nirvana }

5. 
SELECT ?c
    WHERE { ?c rdf:type ch:Chansons ;
               ch:aTypeDeMusique ?t .
            FILTER (! bound(?t))}
        
6. 
SELECT 

7. 
SELECT ?g (count(distinct ?a) as ?count)
    WHERE { ?g rdf:type ch:Groupe .
            ?g ch:aPublie ?a }
    HAVING ( ?count < 4 )

8. 
SELECT ?n
    WHERE { ?m rdf:type ch:Musicien .
            ?m ch:aNom ?n .
            FILTER regex(?n, ^c, "i")}
    ORDER BY ?n

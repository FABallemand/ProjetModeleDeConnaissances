# Projet Modèles de Connaissances et Web Sémantique

## Partie 2: Requêtes

```
PREFIX ch: <http://www.semanticweb.org/fabien/ontologies/2022/11/AllemandCouture_Chansons#>
```

1. [OK]  
Uniquement les chansons dans un album:
```
SELECT ?titre ?groupe ?type
WHERE { ?titre ch:estDansAlbum ?a .
        ?a ch:publiePar ?groupe . # publiePar inclut les groupes ou les musiciens
        OPTIONAL { ?titre ch:aTypeDeMusique ?type } } # Le type de musique est optionnel pour que toutes les chansons apparaissent
```

Avec les chansons publiées en solo, mais avec éventuelle répétitions (distinct ne fonctionne pas ?):
```
SELECT ?titre ?gom ?type
WHERE { ?titre rdf:type ch:Chanson .
        ?titre ch:aInterprete ?gom 
	    OPTIONAL { ?titre ch:aTypeDeMusique ?type } }
```

2. [OK]  
```
SELECT ?a
    WHERE { ?a ch:contientChanson ch:Smells_Like_Teen_Spirit }
```

En précisant le type:
```
SELECT ?a
WHERE { ?a rdf:type ch:Album ;
           ch:contientChanson ch:Smells_Like_Teen_Spirit }
```

3. [OK]  
```
ASK { ?musicien ch:participeDansGroupe ?p .
      ?p ch:aGroupe ch:SDIA .
      ?musicien ch:aGenre ?genre .
      FILTER ( ?genre = ch:Femme ) }
```

Pour tester:
```
SELECT ?musicien ?genre
WHERE { ?musicien ch:participeDansGroupe ?p .
        ?p ch:aGroupe ch:SDIA .
        ?musicien ch:aGenre ?genre
FILTER ( ?genre = ch:Femme ) }
```

4. [OK]  
```
SELECT (count(distinct ?c) as ?count)
WHERE { ?c ch:estDansAlbum ?a .
        ?a ch:publiePar ch:Nirvana }
```

5. [OK]  
```
SELECT ?c ?t
WHERE { ?c rdf:type ch:Chanson
	OPTIONAL { ?c ch:aTypeDeMusique ?t }
        	FILTER ( !bound(?t) ) }
```

Supposition: Les requêtes suivantes ne fonctionnent pas car ch:aTypeDeMusique ne peut pas être exécuté...
```
SELECT ?c
WHERE { ?c ch:aTypeDeMusique ?t .
        FILTER ( !bound(?t) ) }
```

Remarque:
```
SELECT ?c ?t
WHERE { ?c ch:aTypeDeMusique ?t .
        FILTER ( bound(?t) = true ) }
```

6. [OK]  
```
SELECT ?r ?gom ?a # ?gom: groupe ou musicien
WHERE { ?gom ch:aRécompense ?r .
	    ?r ch:aDateRécompense ?a
        { ?gom rdf:type ch:Musicien}
        union # Union pour distinguer les types (un album peut aussi obtenir une récompense)
        { ?gom rdf:type ch:Groupe}
        FILTER (?a = 2022 || ?a = 2023)
}
```

7. [OK]  
```
SELECT ?g (count(distinct ?a) as ?count)
WHERE {?a rdf:type ch:Album .
       ?g ch:aPublié ?a }
GROUP BY (?g)
HAVING (?count < 4)
```

8. [OK]  
```
SELECT ?n
WHERE { ?m ch:aNom ?n .
        FILTER regex(?n, "^c", "i")} # "^c": commence par c | "i": Insensible à la casse
ORDER BY ?n
```

9. [OK]  
```
Musicien(?m) ^ participeDansGroupe(?m, ?p) ^ aGroupe(?p, ?g) -> sqwrl:select(?m, ?g)
```

10. [OK]  
```
Musicien(?m) ^ aGenre(?m, Femme) ^ participeDansGroupe(?m, ?g) -> sqwrl:count(?g)
```
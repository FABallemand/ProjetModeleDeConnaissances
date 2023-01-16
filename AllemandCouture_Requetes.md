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
        OPTIONAL { ?titre ch:aTypeDeMusique ?type } } # Type de musique optionnel -> toutes les chansons apparaissent
```

Avec les chansons publiées en solo, mais avec éventuelle répétitions si plusieurs interprètes "solo" de la même chanson:
```
SELECT ?titre ?gom ?type # ?gom: groupe ou musicien
WHERE { ?titre rdf:type ch:Chanson .
        ?titre ch:aInterprete ?gom 
	OPTIONAL { ?titre ch:aTypeDeMusique ?type } } # Type de musique optionnel -> toutes les chansons apparaissent
```

2. [OK]  
```
SELECT ?a
WHERE { ?a ch:contientChanson ?c .
        ?c ch:aTitre ?t
        FILTER ( ?t = "Smells Like Teen Spirit" ) }
```

La requête suivante ne vérifie pas la valeur du titre...
```
SELECT ?a
    WHERE { ?a ch:contientChanson ch:Smells_Like_Teen_Spirit }
```

3. [OK]  
Requête ASK pour obtenir une valeur booléenne
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
        ?a ch:publiePar ?g .
        ?g ch:aNomDeGroupe ?n
        FILTER (?n = "Nirvana" ) }
```

La requête suivante ne vérifie pas la valeur du nom de groupe...
```
SELECT (count(distinct ?c) as ?count)
WHERE { ?c ch:estDansAlbum ?a .
        ?a ch:publiePar ch:Nirvana }
```

5. [OK]  
```
SELECT ?c ?t
WHERE { ?c rdf:type ch:Chanson
	OPTIONAL { ?c ch:aTypeDeMusique ?t } # Récupérer le type de musique s'il existe
        FILTER ( !bound(?t) ) } # Sélectionner uniquement les chansons qui n'ont pas de type de musique
```

Supposition: Les requêtes suivantes ne fonctionnent pas car ch:aTypeDeMusique ne peut pas être exécuté...
```
SELECT ?c
WHERE { ?c ch:aTypeDeMusique ?t .
        FILTER ( !bound(?t) ) }
```

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
GROUP BY (?g) # Regrouper le résultat par groupe
HAVING (?count < 4) # Conserver les groupes dont le résultat est inférieur à 4 
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
Musicien(?m) ^ participeDansGroupe(?m, ?p) ^ aGroupe(?p, ?g) ^ aNom(?m, ?n) ^ aNomDeGroupe(?g, ?ng) -> sqwrl:select(?n, ?ng)
```

La requête suivante ne travaille pas sur le nom du groupe...
```
Musicien(?m) ^ participeDansGroupe(?m, ?p) ^ aGroupe(?p, ?g) -> sqwrl:select(?m, ?g)
```

10. [OK]  
```
Musicien(?m) ^ aGenre(?m, Femme) ^ participeDansGroupe(?m, ?g) -> sqwrl:count(?g)
```
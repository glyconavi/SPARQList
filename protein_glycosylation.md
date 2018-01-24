# protein_glycosylation

## Parameters

* `up` Source UniProt ID
  * default: P05556
  * examples: P01218, P05556, P02765, P17301, P35222
 
## Endpoint

https://sparql.uniprot.org/sparql/

## `result` 


```sparql
PREFIX up:<http://purl.uniprot.org/core/>  
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#> 
PREFIX faldo:<http://biohackathon.org/resource/faldo#> 
PREFIX ec:<http://purl.uniprot.org/enzyme/> 
PREFIX rdfs:<http://www.w3.org/2000/01/rdf-schema#> 
PREFIX skos: <http://www.w3.org/2004/02/skos/core#> 
SELECT DISTINCT ?protein ?annotation ?begin ?end  ?description ?aa_sequence ?up ?organism ?name ?genename ?enzyme_name
WHERE
{
    VALUES ?up { "{{params.up}}" }
    BIND (iri(concat("http://purl.uniprot.org/uniprot/", ?up)) AS ?protein)
    ?protein a up:Protein ;
	up:annotation ?annotation .
    ?protein up:organism ?organism . 
    ?protein up:recommendedName ?recommended .
    ?recommended up:fullName ?name .
	?protein up:encodedBy ?gene .
    ?gene skos:prefLabel ?genename .
        
  	OPTIONAL {
    	?annotation rdfs:comment ?description .
		?annotation a up:Glycosylation_Annotation ;
			up:range/faldo:begin
				[ faldo:position ?begin ;
				faldo:reference ?sequence ] .
		?annotation a up:Glycosylation_Annotation ;
			up:range/faldo:end
				[ faldo:position ?end ;
				faldo:reference ?sequence ] .
         }
        OPTIONAL {
			?protein up:enzyme ?enzyme. 
			?enzyme rdfs:subClassOf <http://purl.uniprot.org/core/Enzyme> .
            ?enzyme skos:prefLabel ?enzyme_name .
         }
		?sequence rdf:value ?aa_sequence .
}
ORDER BY ?begin
```

## Output

```javascript
({
   json ({result}) { 

var id = "";
var url  = "";
var sequence = "";
var organism = "";
var name = "";
var gene = "";
// var enzyme = "";
var begin = "";
var end = "";
var annotation = "";
var description = "";
var features = [];
var array = {};

result.results.bindings.map((row) => {
             id = row.up.value , 
            url = row.protein.value , 
            sequence = row.aa_sequence.value , 
            organism = row.organism.value , 
            name = row.name.value , 
            gene = row.genename.value 
});

result.results.bindings.map((row) => {
             id = row.up.value , 
            url = row.protein.value , 
            sequence = row.aa_sequence.value , 
            organism = row.organism.value , 
            name = row.name.value , 
            gene = row.genename.value , 
//            "enzyme": row.enzyme_name.value , 
            begin = row.begin.value , 
            end = row.begin.value , 
            annotation = row.annotation.value , 
            description = row.description.value
//    });

var data =
    {
      "type" : "Glycoform",
      "category" : "PTM",
      "description" : description,
      "begin": begin,
      "end": end,
      "color": "#00F5B8",
      "annotation" : annotation
};


features.push( data );

    });

array = { 
   "id" : id ,
    "url" : url,
    "sequence" : sequence,
    "gene" : gene,
    "organism" : organism,
    "name" : name,
    "features": features  };

return array;

  },
  text({array}) {
      return array.json("\n");
    }

})
```


# protein_glycoform

## Parameters

* `up` Source UniProt ID
  * default: P01218
  * examples: P05556, P02765, P17301, P35222

## Endpoint

https://sparql.glyconavi.org/sparql/

## `result`


```sparql
# <http://glyconavi.org/glycobio/BR_2/RC_2/GA_1/GF_6>
# <http://glyconavi.org/glycobio/BR_1/RC_1/GA_1/GF_1>

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX glycan: <http://purl.jp/bio/12/glyco/glycan#>
PREFIX sio: <http://semanticscience.org/resource/>
PREFIX gs: <http://glyconavi.org/glycosample/>
PREFIX ga: <http://glyconavi.org/glyabun/>
PREFIX exterms: <http://www.example.org/terms/>
PREFIX chebi: <http://bio2rdf.org/chebi:>
PREFIX faldo: <http://biohackathon.org/resource/faldo#>
PREFIX up: <http://purl.uniprot.org/core/>

SELECT distinct ?pos ?order ?from ?to  ?wurcs ?glycan_image ?mod_wurcs ?mod_glycan_image ?glytoucan ?gsid ?uniprotid
#FROM <http://glyconavi.org/database/GlycoAbun>
FROM <http://glyconavi.org/database/glycoabun>
WHERE {

?gs glycan:has_resource_entry ?br .
?gs gs:glycosample_id ?gsid .

?br sio:has-component-part ?rc .
?rc sio:has-component-part ?pep .
?pep ga:uniprot_id ?uniprotid .
# VALUES ?uniprotid { "P01218" }
# P01218
VALUES ?uniprotid { "{{params.up}}" }

?rc ga:has_abundance ?ga .
?ga dcterms:identifier ?gaid .

#VALUES ?gaid { "{{params.gaid}}" }

?ga sio:has-direct-part ?gf .

# abundance
?gf sio:has-proper-part ?gi .
?gf ga:order ?order .
#VALUES ?gf { <http://glyconavi.org/glycobio/BR_1/RC_1/GA_1/GF_1> }

#glycosylation site
OPTIONAL {
?gf sio:is-covalently-connected-to ?loc .
?loc faldo:begin ?begin .
?begin faldo:position ?pos .
}

# glycan structure
?gi sio:has-part ?gcomp .
?gcomp chebi:32854 ?glycan .
?glycan ga:wurcs ?wurcs .
?glycan foaf:depiction ?glycan_image .


OPTIONAL {
?glycan ga:modified ?mod .
?mod ga:modified_glycan ?mod_glycan .
?mod_glycan ga:wurcs ?mod_wurcs .
?mod_glycan foaf:depiction ?mod_glycan_image .
?mod ga:method ?method .

OPTIONAL {
?mod_glycan dcterms:identifier ?glytoucan .
}

}



#?method rdf:type ?method_type .
# abundance ratio
OPTIONAL {
?gi exterms:percentage ?par .
?par ga:from ?par_from .
?par_from sio:has-value ?from .
?par ga:to ?par_to .
?par_to sio:has-value ?to .
}

}
order by ?pos
```

## Output
```javascript
({
  json({result}) {
    return result.results.bindings.map((row) => {
         return {
         "GlycoSample":row.gsid.value , 
         "Uniprot":row.uniprotid.value , 
			"site": row.pos.value , 
//			"order": row.order.value , 
			"from": row.from.value , 
			"to": row.to.value , 
            "glytoucan":row.glytoucan.value , 

			"wurcs": row.wurcs.value,
			"glycan_image": row.glycan_image.value 
//			"mod_wurcs": row.mod_wurcs.value , 
//			"mod_glycan_image": row.mod_glycan_image.value
      }
    });
  },
	text({result}) {
		return result.results.bindings.map(row => row.value).join("\n");
	}
})
```


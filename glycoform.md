# Glycoform: GlycoNAVI  -GlycoAbun

## Parameters

* `id` Source RC ID
  * default: BR1RC1
  * examples: 

## Endpoint

https://sparql.glyconavi.org/sparql/

## `result`


```sparql

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dc: <http://purl.org/dc/elements/1.1/>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX void: <http://rdfs.org/ns/void#>
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX glycan: <http://purl.jp/bio/12/glyco/glycan#>
PREFIX up: <http://purl.uniprot.org/core/>
PREFIX faldo: <http://biohackathon.org/resource/faldo#>
PREFIX UO: <http://purl.obolibrary.org/obo/>
PREFIX gs: <http://glyconavi.org/glycosample/>
PREFIX ga: <http://glyconavi.org/glyabun/>
PREFIX gb: <http://glyconavi.org/glycobio/>
PREFIX sio: <http://semanticscience.org/resource/>
PREFIX chebi: <http://bio2rdf.org/chebi:>
PREFIX exterms: <http://www.example.org/terms/>
PREFIX pav:   <http://purl.org/pav/>
PREFIX dcat:  <http://www.w3.org/ns/dcat#>

SELECT distinct ?gf ( ?Upos  )AS ?pos ?order ?from ?to  ?wurcs ?glycan_image ?mod_wurcs ?mod_glycan_image ?glytoucan ?gsid ?uniprotid ?seq ( ?Apos ) as ?Author_site  ?brid ?rcid ?glytoucanURL ?mod_glycan_label

FROM <http://glyconavi.org/database/glycoabun/20180221>
WHERE {

    VALUES ?rcid { "{{params.id}}" }

    ?gs glycan:has_resource_entry ?br .
    ?br gb:brid ?brid .
    ?gs gs:glycosample_id ?gsid .
    ?br sio:has-component-part ?rc .
    ?rc gb:rcid ?rcid .
    ?rc sio:has-component-part ?pep .
    ?pep ga:aa_sequence ?seq .
    ?pep ga:uniprot_id ?uniprotid .

    ?rc ga:has_abundance ?ga .
#    ?ga dcterms:identifier ?gaid .
    ?ga sio:has-direct-part ?gf .

    # abundance
    ?gf sio:has-proper-part ?gi .
    ?gf ga:order ?order .

    #glycosylation site
    OPTIONAL {
        ?gf sio:is-covalently-connected-to ?loc .
        ?loc faldo:location ?beginA .
        ?loc faldo:location ?beginU .
        ?beginA faldo:position ?Apos .
        ?beginU faldo:position ?Upos .
        ?beginA rdf:type ga:AuthorPosition .
        ?beginU rdf:type faldo:ExactPosition .
    }

    # glycan structure
    ?gi sio:has-part ?gcomp .
    ?glycan chebi:32854 ?gcomp .
    ?glycan ga:wurcs ?wurcs .
    ?glycan foaf:depiction ?glycan_image .


    OPTIONAL {
        ?glycan ga:modified ?mod .
        ?mod ga:modified_glycan ?mod_glycan .
        ?mod_glycan ga:wurcs ?mod_wurcs .
        ?mod_glycan foaf:depiction ?mod_glycan_image .
        ?mod_glycan rdfs:seeAlso ?glytoucanURL .
        ?mod ga:method ?method .

        OPTIONAL {
            ?mod_glycan dcterms:identifier ?glytoucan .
        }

        OPTIONAL {
            ?mod_glycan rdfs:label ?mod_glycan_label .
        }


    }

    # abundance ratio
    OPTIONAL {
        ?gi exterms:percentage ?par .
        ?par ga:from ?par_from .
        ?par_from sio:has-value ?from .
        ?par ga:to ?par_to .
        ?par_to sio:has-value ?to .
    }

}
# order by ?Apos


```

## Output

```javascript
({
  json({result}) {


  var uniprotid = "";
  var sample = "";
  var sequence = "" ;
  var brid = "";
  var rcid = "";
  var begin = "";
  var end = "";
  var pos = "";
  var authorsite = "";
  var wurcs = "";
  var glyimg = "";
  var modGlycanLabel = "" ;
  var glytoucanid = "";
  var glytoucanURL = "";
  var from = "";
  var to = "" ;
  var features = [];
  var array = {};


  result.results.bindings.map((row) => {

        uniprotid = row.uniprotid.value ,
        sample = row.gsid.value ,
        brid = row.brid.value ,
        rcid = row.rcid.value ,
        sequence = row.seq.value ,
        site = row.pos.value ,
        glytoucanid  = row.glytoucan.value ,
        wurcs = row.mod_wurcs.value ,
        glyimg = row.mod_glycan_image.value,
        glytoucanURL = row.glytoucanURL.value ,
        from = row.from.value,
        to = row.to.value,
       authorsite = row.Author_site.value ,
       modGlycanLabel = row.mod_glycan_label

        var data = {
              "type" : "Glycoform",
              "category" : "PTM",
              "site" : site ,
              "author site" : authorsite ,
              "from" : from ,
              "to" : to ,
              "GlyTouCanID" : glytoucanid ,
              "GlyTouCan URL" : glytoucanURL ,
              "WURCS" : wurcs ,
              "Glycan Label" : modGlycanLabel ,
              "glycan_image" : glyimg
        };

        features.push(data);
  });

  array = {
        "sample id" : sample,
		"br id" : brid,
        "rc id" : rcid,
		"uniprot id" : uniprotid ,
        "br id" : brid,
        "rc id" : rcid,
        "sequence" : sequence,
        "features" : features
  };

  return array;


  },
	text({array}) {
		return array.json("\n");
	}
})

```



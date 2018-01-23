# test_glycoprotein  - sample JSON data for ProtVista

## Parameters

* `up` Source UniProt ID
  * default: P05556
  * examples: P01218, P05556, P02765, P17301, P35222
 
## Endpoint

http://sparql.glyconavi.org/sparql/


## Output
```javascript
({

   json () { 
   var jsondata = 
 {
  "sequence": "MLPGLALLLLAAWTARALEVPTDGNAGLLAEPQIAMFCGRLNMHMNVQNGKWDS",
  "features": [
    {
      "type": "Carbohyd",
      "category": "PTM",
      "description": "Cytoplasmic",
      "begin": "24",
      "end": "24",
      "evidences": [
        {
          "code": "ECO:0000269"
        }
      ]
    },
    {
      "type": "Carbohyd",
      "category": "PTM",
      "begin": "30",
      "end": "30"
    },
    {
      "type": "Carbohyd",
      "category": "PTM",
      "begin": "35",
      "end": "35"
    },
    {
      "type": "Carbohyd",
      "category": "PTM",
      "begin": "41",
      "end": "41",
      "color": "#00F5B8"
    },
    {
      "type": "Carbohyd",
      "category": "PTM",
      "begin": "43",
      "end": "43",
      "color": "#FF7094"
    },
    {
      "type": "Carbohyd",
      "category": "PTM",
      "description": "",
      "begin": "48",
      "end": "48",
      "color:": "#FF3366",
      "evidences": [
        {
          "code": "ECO:0000269",
          "source": {
            "name": "PubMed",
            "id": "12665801",
            "url": "http://www.ncbi.nlm.nih.gov/pubmed/12665801"
          }
        },
        {
          "code": "ECO:0000269",
          "source": {
            "name": "PubMed",
            "id": "2900137",
            "url": "http://www.ncbi.nlm.nih.gov/pubmed/2900137"
          }
        }
      ]
    }
  ]
}

   
   return jsondata
   }
})
```


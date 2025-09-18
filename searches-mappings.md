POST likes/_doc
{
  "dep" : "24",
  "likes" : 7000
}


# aggregations
GET likes/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "par_departement" : {
      "terms": {
        "field": "dep.keyword"
      },
      "aggs": {
        "likes": {
          "avg": {
            "field": "likes"
          }
        }
      }
    }
  }
}

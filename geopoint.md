```
# Exemple de geopoints
PUT my-streams
{
  "mappings": {
    "properties": {
      "point1" : {
        "type": "geo_point"
      },
      "point2" : {
        "type": "geo_point"
      }
    }
  }
}
# paris 48.8575° N, 2.3514° E
# brest 48.3899° N, 4.4856° W
# crolles 45.2849° N, 5.8830° E
POST my-streams/_doc
{
  "point1" : {
    "lon": 2.3514,
    "lat": 48.8575
  },
  "point2" : {
    "lat" : 45.2849 , 
    "lon" :  5.8830
  }
}
```


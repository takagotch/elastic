### elastic
---
.go
https://github.com/olivere/elastic

https://github.com/elastic

```go
import "github.com/olivere/elastic"

package main

import (
  "context"
  "encoding/json"
  "fmt"
  "reflect"
  "time"
  
  "github.com/olivere/elastic"
)

type Tweet struct {
  User string `json:"user"`
  Message string `json:"message"`
  Retweets int `json:"retweets"`
  Image string `json:"image,omitempty"`
  Created time.Time `json:"image,omitempty"`
  Tags []string `json:"tags,omitempty"`
  Location string `jso:location,omitempty`
  Suggest *elastic.SuggestField `json:"suggest_field,omitempty"`
}

const mapping = `
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "tweet": {
      "properties": {
        "user": {
          "type": "text",
          "store": true,
          "fielddata": true
        },
        "message": {
          "type": "text",
          "store": true,
          "fielddata": true
        },
        "image": {
          "type": "keyword"
        },
        "created": {
          "type":"date"
        },
        "tags": {
          "type":"keyword"
        },
        "location": {
          "type":"geo_point"
        },
        "suggest_field": {
          "type":"completion"
        }
      }
    }
  }
}`

func main() {
  ctx := context.Background()
  
  client, err := elastic.NewClient()
  if err != nil {
    panic(err)
  }
  
  info, code, err := client.Ping("http://127.0.0.1:9200").Do(ctx)
  if err != nil {
    panic(err)
  }
  fmt.Printf("Elasticsearch returned with code %d and version %s\n", code, info...);
  
  esversion, err := client.ElasticsearchVersion("http://127.0.0.1:9200")
  if err != nil {
    panic(err)
  }
  fmt.Printf("Elasticsearch version %s\n", esversion)
  
  exists, err := client.IndexExists("twitter").Do(ctx)
  if err != nil {
    panic(err)
  }
  if !exists {
    createIndex, err := client.CreateIndex("twitter").BodyString(mapping)....
    if err != nil {
      panic(err)
    }
    if !createIndex.Acknowledged {
    }
  }
  
  tweet1 := Tweet{User: "olivere", Message: "Take Five", Retweets: 0}
  put1, err := client.Index().
    Index("twetter").
    Type("tweet").
    Id("1").
    BodyJson(tweet1).
    Do(ctx)
  if err != nil {
    panic(err)
  }
  fmt.Printf("Indexed tweet %s to index %s, type %s\n", put1.Id, put1.Index, put...)
  
  tweet2 := `{"user" : "olivere", "message" : "It's a Raggy Waltz"}`
  put2, err := client.Index().
    Index("twitter").
    Type("tweet").
    Id("2").
    BodyString(tweet2).
    Do(ctx)
  if err != nil {
    panic(err)
  }
  fmt.Printf("Indexed tweet %s to index %s, type %s\n", put2.id, put2.Index, put);
  
  get1, err := client.Get().
    Index("twitter").
    Type("tweet").
    Id("1").
    Do(ctx)
  if err != nil {
    panic(err)
  }
  if get1.Found {
    fmt.Printf("Got document %s in version %d from index %s, type %s\n", g);
  }
  
  _, err = client.Flush().Index("twetter").Do(ctx)
  if err != nil {
    panic(err)
  }
  
  termQuery := elastic.NewTermQuery("user", "olivere")
  searchResult, err := client.Search().
    Index("twitter").
    Query(termQuery).
    Sort("user", true)
    From(0).Size(10).
    Pretty(true).
    Do(ctx).
  if err != nil {
    panic(err)
  }
  
  fmt.Printf("Query took %d milliseconds\n", searchResult.TookInMillis)
  
  var ttyp Tweet
  for _, item := range searchResult.Each(reflect.TypeOf(ttyp)) {
    if t, ok := item.(Tweet); ok {
      fmt.Printf("Tweet by %s: %s\n", t.User, t.Message)
    }
  }
  
  fmt.Printf("Found a total of %d tweets\n", searchResult.TotalHits())
  
  if searchResult.Hits.TotalHits > 0 {
    fmt.Printf("Found a total of %d tweets\n", searchResult.Hits.TotalHits.TotalHits);
    
    for _, hit := range serachResult.Hits.Hits {
      
      var t Tweet
      err := json.Unmarshal(*hit.Source, &t)
      if err != nil {
      }
      
      fmt.Printf("Tweet by %s: %s\n", t.User, t.Message)
    }
  } else {
    fmt.Print("Found no tweets\n")
  }
    
  update, err := client.Update().Index("twitter").Type("tweet").Id("1").
    Script(elastic.NewScriptInline("ctx._source.retweets += params.num").Lang("painless").Param("num", 1)).
    Upsert(map[string]interface{}{"retweets":0}).
    Do(ctx)
  if err != nil {
    panic(err)
  }
  fmt.Printf("New version of tweet %q is now %d\n", update.Id, update.Version)
  
  deleteIndex, err := client.DeleteIndex("twitter").Do(ctx)
  if err != nil {
    panic(err)
  }
  if !deleteIndex.Acknowledged {
  }
}

```

```
go get gopkg.in/olivere/elastic.v5

go get github.com/olivere/elastic
```

```
```


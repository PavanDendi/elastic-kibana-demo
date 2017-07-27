# elastic-kibana-demo

Create the elasticsearch index/type and define critical field mappings:
```
curl -XPUT "http://localhost:9200/nyc311" -H 'Content-Type: application/json' -d'
{
    "mappings": {
      "2015": {
        "properties": {
          "closed_tdt": {
            "type": "date"
          },
          "created_tdt": {
            "type": "date"
          },
          "location_p": {
            "type": "geo_point"
          }
        }
      }
    }
}'
```

Run spark-shell with the appropriate dependencies for csv and elasticsearch as well as correct authentication if using security:
```
spark-shell --packages com.databricks:spark-csv_2.11:1.5.0,org.elasticsearch:elasticsearch-spark-20_2.11:5.5.1 --conf spark.es.net.http.auth.user=USER--conf spark.es.net.http.auth.pass=PASSWORD
```

Inside spark-shell, perform necessary imports, read csv as SparkSQL DataFrame, and index into elasticsearch:
```
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.SQLContext._

import org.elasticsearch.spark.sql._


val testdf = sqlContext.read.format("com.databricks.spark.csv").
	option("header","true").
	option("inferSchema","true").
	load("nyc311_clean.csv")
  
  
testdf.saveToEs("nyc311/2015")
```

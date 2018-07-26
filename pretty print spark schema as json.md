How to pretty print schema for a spark dataframe
```
import json
xyz_df = spark.read.parquet("s3://xyz")
print(json.dumps(json.loads(xyz_df.schema.json()),indent=2, sort_keys=True))
```

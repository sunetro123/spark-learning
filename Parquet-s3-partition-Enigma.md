# Adventures in Parquet

Problem :  A 302 Gig of parquet is being read in an EMR. The reading takes lots of time

Try 1 :  First checked partitions created during read for 1400~ parquet parts of average 116 MB Spark created 1793 partitions why ?

## Learning :
How to Check all the Configurations 

###  runtime *common* environment  : 
   ``` print( "\n".join([ i[0].encode("utf-8") + " ----->   "  \ ```
                                       +  i[1].encode("utf-8") for i in spark.sparkContext.getConf().getAll() ]) ) ```
###  How to get Hadoop Environment : 

Ok. This show me spark session configs but how to go deeper ?
How would I solve why --> *1793* <-- partitions ?
Read through SO and original source code and got two pieces of information
sparkContext is the underlying interface for everything # TODO clear diff in sparkConf, sparkContext, sparkSession

```
spark.sparkContext.version

spark.sparkContext.getConf()

sc1 = spark.sparkContext

sc1._jsc.hadoopConfiguration().get("fs.s3.impl")   # Learning use of _jsc

sc1._jsc.hadoopConfiguration().get("fs.s3.block.size") 




sc1._jsc.hadoopConfiguration().get("yarn.scheduler.maximum-allocation-mb")

#spark.sql("SET -v").show(300, False)


sc1._jsc.hadoopConfiguration().get("yarn.nodemanager.resource.memory-mb")


```

## Is the Partition determined by fs.s3.block.size ?
What is the value : 67108864  == 64 MB. Does not make sense.

# How to see spark.sql configurations ?
   ###  Life Saving Hack 
   ```spark.sql("SET -v").show(300, False)```
 # Introducing TWO major players controlling Partitions during Read
  ```spark.conf.set("spark.sql.files.maxPartitionBytes", 134217728 * 2 ) # By default 128 Mb ```
     ``` spark.conf.set("spark.sql.shuffle.partitions", 1000) # By default 200 ```
# So Mystery Remains :
 Spark is creating TWO stages for reading same parquet in follwing code
 ```
 raw_df = spark.read.parquet("s3://bis-randd-poc-landing/airflow_test_upload/FCT_GAME_PLAY_ACCT_DEVICE_DISK_LTD_SMP_20180328")

print(" Raw Parquet Partitions ---> {}  <---- ").format(raw_df.rdd.getNumPartitions())

raw_df.schema 

eFilter = (F.col('DEVICE_ID') > 0) & (F.col('SCE_REGION_ID') > 0 ) & (F.col('FIRST_PLAY_START_DTTM').isNotNull()) & ( F.col('FIRST_PLAY_START_DTTM') >= '2006-01-01 00:00:00' ) &  \
        (F.col('LAST_PLAY_START_DTTM').isNotNull()) & \
        ( F.col('LAST_PLAY_START_DTTM') >= '2006-01-01 00:00:00' )

fgp_ltd_df = raw_df \
            .select ( 'SOURCE_TITLE_ID' , 'DISK_ID', 'ACCT_ID' , 'DEVICE_ID' , 'SCE_REGION_ID','FIRST_PLAY_START_DTTM','LAST_PLAY_START_DTTM').filter(eFilter).repartition(F.col("SOURCE_TITLE_ID") , \
                                                                                                                                                                        F.col("ACCT_ID"),F.col("DISK_ID"), F.col("DEVICE_ID"), F.col('SCE_REGION_ID'))

print(" fgp_ltd_df Parquet Partitions ---> {}  <---- ").format(fgp_ltd_df.rdd.getNumPartitions())


fgp_ltd_df.createOrReplaceTempView("fgp_ltd")
ada_wrk_title_disks_updated_df = spark.sql("select SOURCE_TITLE_ID, DISK_ID from fgp_ltd group by 1,2")

print("First Group By Partitions ---> {}   <---").format(ada_wrk_title_disks_updated_df.rdd.getNumPartitions())


ada_wrk_title_disks_updated_df.createOrReplaceTempView("ada_wrk_title_disks_updated")

 ```
 
 # Need to check why :
 
 Some good place to start : 
 
 https://umbertogriffo.gitbooks.io/apache-spark-best-practices-and-tuning/content/sparksqlshufflepartitions_draft.html

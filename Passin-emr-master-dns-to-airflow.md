# When EMR dies because of SPOT Instance
### And you have a job that does spark-submit using *Templated* bash operator as ``` ssh -i xyz.pem hadoop@<emr-address>

## What to Do ?

AIRFLOW Provides a way of changing variable through UI
use that variable and then call it like below  var.value.xx

```
templated_command = get_templated_command('{{var.value.emr_master}}', '{{params.jar_location}}','{{params.python_file}}')
t16 = BashOperator(
    task_id = 'Calculate_Dim_Account_ODS_Delta',
    bash_command= templated_command,
    params={'cluster_id':'10.243.69.50','jar_location':'/usr/lib/zeppelin/interpreter/nzjdbc.jar',
            'python_file':'s3://bis-randd-lakes/sbanerjee/dim-dags/spark_dim_acct_delta.py' },
    queue='transfer_queue',
    dag=dag )

```

Source Code : https://github.com/apache/incubator-airflow/blob/12ab796b11c001f5cc7c5bd294616200b4159dea/airflow/models.py#L1774

#TODO : make the command change

# How to Automate this whole Thing ?

using aws cli ---> Lost the command shall put back

08-may-2018 :
Finally made good progress with Jmespath <-- A bad way of parsing JSON

However, following shall
#### Find all the instances where Owner tag is missing

```
aws ec2 describe-instances --region us-west-2 --filter "Name = instance-state-name ,Values = 'running'" --query 'Reservations[*].Instances[?!not_null(Tags[?Key == `Owner`].Value)][].[InstanceId, Tags[?Key == `Name`]|[0],Tags[?Key == `Owner`]|[0], State.Name]'
```

Output Sample :

```
[
        "i-0xyxyxyxyxyxyxy6",
        {
            "Value": "My EC2 Name",
            "Key": "Name"
        },
        null,
        "running"
    ],
```

https://www.onica.com/blog/using-aws-cli-to-find-untagged-instances/ . <--- One and only one useful blog in the sea of bullocks

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
    params={'cluster_id':'x.y.z.c','jar_location':'/usr/lib/zeppelin/interpreter/nzjdbc.jar',
            'python_file':'s3://yy/xx/abc.py' },
    queue='transfer_queue',
    dag=dag )

```

Source Code : https://github.com/apache/incubator-airflow/blob/12ab796b11c001f5cc7c5bd294616200b4159dea/airflow/models.py#L1774

#TODO : make the command change

# Learning how to use Airflow variables 

## A utility to propose

use aws cli/boto3 to get current running EMR master, set a variable using ``` airflow variables -s [ 'emr-master' , 'xyz'] ```

ref : https://airflow.apache.org/concepts.html#variables

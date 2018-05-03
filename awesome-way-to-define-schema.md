# Problem : If there are Japanese/Chinese Characters in a column along with Roman literals, Spark Can not Translate UTF8


What is there in Pyspark source code for StringType()

```
_type_mappings = {
    type(None): NullType,
    bool: BooleanType,
    int: LongType,
    float: DoubleType,
    str: StringType,
    bytearray: BinaryType,
    decimal.Decimal: DecimalType,
    datetime.date: DateType,
    datetime.datetime: TimestampType,
    datetime.time: TimestampType,
}

if sys.version < "3":
    _type_mappings.update({
        unicode: StringType,
        long: LongType,
    })


```
And my Version in Zeppelin
```
import sys
print(sys.version)
```
>> 2.7.13 (default, Jan 31 2018, 00:17:36) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-11)]

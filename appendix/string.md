## Properties of &lt;string&gt; Field
The **&lt;string&gt;** field has all the [common](fields.md) properties as
well as ones listed below. Refer to [&lt;string&gt; Field](../fields/string.md) chapter
for detailed description. 

|Property Name|Allowed type / value|DSL Version|Required|Default Value|Description|
|:-----------:|:------------------:|:---------:|:------:|:-----------:|-----------|
|**defaultValue**|string|1|no||Default value.|
|**length**|[unsigned](../intro/numeric.md)|1|no|0|Fixed serialization length. **0** means no length limit. Cannot be used tegether with **lengthPrefix** or **zeroTermSuffix**.|
|**lengthPrefix**|[field](../fields/fields.md) or [reference](../intro/references.md)|1|no||Prefix field containing length of the string. Cannot be used tegether with **length** or **zeroTermSuffix**.|
|**zeroTermSuffix**|[bool](../intro/boolean.md)|1|no|false|Terminate string with **0**. Cannot be used tegether with **length** or **lengthPrefix**.|



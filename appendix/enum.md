## Properties of &lt;enum&gt; Field
The **&lt;enum&gt;** field has all the [common](fields.md) properties as
well as ones listed below. Refer to [&lt;enum&gt; Field](../fields/enum.md) chapter
for detailed description. 

|Property Name|Allowed type / value|DSL Version|Required|Default Value|Description|
|:-----------:|:------------------:|:---------:|:------:|:-----------:|-----------|
|**type**|"int8", "uint8", "int16", "uint16", "int32", "uint32", "int64", "uint64", "intvar", "uintvar"|1|yes||Underlying primitive type.|
|**defaultValue**|[numeric](../intro/numeric.md) or [name](../intro/names.md)|1|no|0|Default value. Must fit the underlying **type**.|
|**endian**|"big" or "little"|1|no|endian of [schema](../schema/schema.md)|Endian of the field.|
|**length**|[unsigned](../intro/numeric.md)|1|no|length of **type**|Forced serialization length.|
|**bitLength**|[unsigned](../intro/numeric.md)|1|no|length of **type** in bits|Serialization length in bits, applicable only to a member of [&lt;bitfield&gt;](../fields/bitfield.md).|
|**hexAssign**|[bool](../intro/boolean.md)|1|no|false|Assign generated enum values using hexadecimal numbers.|
|**nonUniqueAllowed**|[bool](../intro/boolean.md)|1|no|false|Allow non unique **&lt;validValue&gt;**-es.|
|**validCheckVersion**|[bool](../intro/boolean.md)|1|no|false|Take into account protocol version when generating code for field's value validity check.|

#### Properties of &lt;validValue&gt; Child Element of &lt;enum&gt; Field
|Property Name|Allowed type / value|DSL Version|Required|Default Value|Description|
|:-----------:|:------------------:|:---------:|:------:|:-----------:|-----------|
|**name**|[name](../intro/names.md) string|1|yes||Name of the value.|
|**val**|[numeric](../intro/numeric.md)|1|yes||Numeric value.|
|**description**|string|1|no||Human readable description of the value.|
|**displayName**|string|1|no||Human readable name of the value to display in various analysis tools.|
|**sinceVersion**|[unsigned](../intro/numeric.md)|1|no|0|Version of the protocol in which value was introduced.|
|**deprecated**|[unsigned](../intro/numeric.md)|1|no|max unsigned|Version of the protocol in which value was deprecated.<br />Must be greater than value of **sinceVersion**.|

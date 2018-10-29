## &lt;float&gt; Field
This field stores and abstracts away value of floating poing type with IEEE 754 encoding. 
The **&lt;float&gt;** field has all the [common](common.md) properties
as well as extra properties and elements described below.

#### Underlying Type
Every **&lt;float&gt;** field must provide its underlying storage type using 
**type** [property](../intro/properties.md). Available 
values are:
- **float** - 4 byte floating point representation.
- **double** - double precision 8 bytes floating point representation.

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="F1" type="float" />
        <float name="F2" type="double" />
    </fields>
</schema>
```

#### Special Values
Some protocol may set a special meaning for some values. 
Such values (if exist) must be listed as **&lt;special&gt;** child of the 
**&lt;float&gt;** XML element.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double">
            <special name="Special1" val="1.0" />
            <special name="Special2" val="5.0" />
        </int>
    </fields>
</schema>
```
The code generator is expected to generate extra convenience functions that 
check whether stored value has special value as well as updating the stored value
with special one.

Every **&lt;special&gt;** must define a valid [name](../intro/names.md) 
(using **name** [property](../intro/properties.md)) as 
well as floating point value (using **val** 
[property](../intro/properties.md)), that fits chosen 
[underlying type](#underlying-type). The **&lt;special&gt;**-s may be listed
in any order, not necessarily sorted.

In addition to floating point numbers, the **val** property may also cantain
the following case-insensitive strings.
- **nan** - Represents NaN value.
- **inf** - Represents positivie infinity.
- **-inf** - Represents negative infinity.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double">
            <special name="Invalid" val="nan" />
        </int>
    </fields>
</schema>
```

Every **&lt;special&gt;** supports extra properties:
- **description** - Extra description and documentation on how to use the value.
- **sinceVersion** - Version of the protocol when the special name / meaning was introduced.
- **deprecated** - Version of the protocol when the special name / meaning was deprecated.

All these extra properties are described in detail in 
[Common Properties of Fields](common.md).

#### Default Value
The default value of the **&lt;float&gt;** field when constructed can be specified
using **defaultValue** property. If not specified, defaults to **0.0**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" defaultValue="5.0" />
    </fields>
</schema>
```
The default value can also be specified using the name of one of the 
**&lt;special&gt;**-s:
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" defaultValue="Special2">
            <special name="Special1" val="nan" />
            <special name="Special2" val="-inf" />
        </float>
    </fields>
</schema>
```
Just like with [special values](#special-values), the value of the 
**defaultValue** [property](../intro/properties.md) can also be either **nan**, 
**inf** or **-inf**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" defaultValue="nan" />
    </fields>
</schema>
```

#### Endian
The default serialization endian of the protocol is specified in **endian**
property of the [schema](../schema/schema.md). It is possible to override the
default endian value with extra **endian** property.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big">
    <fields>
        <float name="SomeFloatField" type="double" endian="little" />
    </fields>
</schema>
```

#### Units
Protocols quite often specify what units are being transfered. The **CommsDSL**
provides **units** [property](../intro/properties.md) to specify this information.
The code generator may use this information to generate a functionality that allows 
retrieval of proper value for requested units, while doing all the conversion 
math internally. Such behavior will allow developers, that use generated
protocol code, to focus on their business logic without getting into details
on how value was transfered.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" units="mm" />
    </fields>
</schema>
```
For list of supported **units** values, refer to appended [units](../appendix/units.md) 
table.

#### Valid Values
Many protocols specify ranges of values the field is allowed to have and how
client code is expected to behave on reception of invalid values. The code
generator is expected to generate code that checks whether field's value
is valid. The **CommsDSL** provides multiple properties to help with such
task.

One of such properties if **validRange**. The format of it's value is 
"[*min_value*, *max_value*]".
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" validRange="[-100.0, 100.0]" />
    </fields>
</schema>
```
It is possible to have multiple valid ranges for the same field. However XML
does NOT allow having multiple attributes with the same name. As the result
it is required to put extra valid ranges as **&lt;validRange&gt;** children
elements.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double">
             <validRange value="[-100.0, 100.0]" />
             <validRange value="[250.0, 444.56]" />
        </float>
    </fields>
</schema>
```
Another property is **validValue**, which adds single value (not range) to 
already defined valid ranges / values. Just like with **validRange**, multiple
values need to be added as XML children elements.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" validRange="[-100, 100]" validValue="200.0">
            <validValue value="nan" />
        </float>
    </fields>
</schema>
```
The **validValue** property allows adding special values (**nan**, **inf**, 
and **-inf**) to available valid values / ranges.

There are also **validMin** and **validMax**, which specify single 
floating point value and are equivalent to having
**validRange="[*provided_min_value*, *max_value_allowed_by_type*]"** and
**validRange="[*min_value_allowed_by_type*, *provided_max_value*]"** respectively.

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" validMin="-20.0" />
        <float name="SomeOtherFloatField" type="double" validMax="100.0" />
    </fields>
</schema>
```
The specified valid ranges and values are allowed to intersect. The code 
generator may warn about such cases and/or unify them to limit number of
**if** conditions in the generated code for better performance.

If none of the mentioned above validity related options has been used, the
whole range of available values is considered to be valid, including
extra values **nan**, **inf**, and **-inf**.

In case **nan**, **inf**, and **-inf** need to be excluded from a range of valid
values, but all the available floating point values are considered to be valid,
then **validFullRange** [property](../intro/properties.md) with
[boolean](../intro/boolean.md) value needs to be used.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" validFullRange="true" />
    </fields>
</schema>
```

All the validity related [properties](../intro/properties.md) mentioned in this
section (**validRange**, **validValue**, **validMin**, **validMax**) may
also add information about version they were introduced / deprecated in. 
Adding such information is possible only when the property is defined as
XML child element.
```
<?xml version="1.0" encoding="UTF-8"? version="10">
<schema ...>
    <fields>
        <float name="SomeFloatField" type="float">
             <validRange value="[0.0, 10.0]" />
             <validValue value="25.0" sinceVersion="2" deprecated="5" />
             <validRange value="[55.0, 80.0]" sinceVersion="7" />
        </float>
    </fields>
</schema>
```
The **sinceVersion** and **deprecated** properties are described in detail as 
[Common Properties of Fields](common.md).

#### Version Based Validity
The code generator is expected to generate functionality checking that 
**&lt;float&gt;** field contains a valid value. By default if the field's value 
is within any of the specified ranges / values, then the it is considered to be valid
regardless of version the containing range was
introduced and/or deprecated. However, it is possible to force code generator to
generate validity check code that takes into account reported version of the
protocol by using **validCheckVersion** [property](../intro/properties.md), which
is set to **true**.
```
<?xml version="1.0" encoding="UTF-8"? version="10">
<schema ...>
    <fields>
        <float name="SomeFloatField" type="float" validCheckVersion="true">
             <validRange value="[0.0, 10.0]" />
             <validValue value="25" sinceVersion="2" deprecated="5" />
             <validRange value="[55, 80]" sinceVersion="7" />
        </float>
    </fields>
</schema>
```

#### Extra Display Properties
When displaying the floating point value, held by the **&lt;float&gt;** field,
GUI analysis tools require knowledge on how many digits after decimal point
need to be displayed. To provide this information, use **displayDecimals** [property](../intro/properties.md) 
with numeric value.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="float" displayDecimals="4" />
    </fields>
</schema>
```
If value of **displayDecimals** is **0**, then it is up to the GUI tool
to choose how many digits after decimal point to display.

Use [properties table](../appendix/float.md) for future references.
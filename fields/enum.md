## &lt;enum&gt; Field
This field stores and abstracts away value of integral 
[enumerated type](https://en.wikipedia.org/wiki/Enumerated_type), where every
valid value has its name. The **&lt;enum&gt;** field has all the [common](common.md) properties
as well as extra properties and elements described below.

#### Underlying Type
Every **&lt;enum&gt;** field must provide its underlying storage type using 
**type** [property](../intro/properties.md). Available 
values are:
- **int8** - 1 byte signed integer.
- **uint8** - 1 byte unsigned integer.
- **int16** - 2 bytes signed integer.
- **uint16** - 2 bytes unsigned integer.
- **int32** - 4 bytes signed integer.
- **uint32** - 4 bytes unsigned integer.
- **int64** - 8 bytes signed integer.
- **uint64** - 8 bytes unsigned integer.
- **intvar** - up to 8 bytes variable length signed integer
- **uintvar** - up to 8 bytes variable length unsigned integer

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8">
            ...
        </enum>
    </fields>
</schema>
```
The variable length types are encoded using **Base-128** form, such as
[LEB128](https://en.wikipedia.org/wiki/LEB128) for *little* endian or similar for
big endian.

#### Valid Values
All the valid values must be listed as **&lt;validValue&gt;** child of the 
**&lt;enum&gt;** XML element.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="0x1b" />
        </enum>
    </fields>
</schema>
```
Every **&lt;validValue&gt;** must define a valid [name](../intro/names.md) 
(using **name** [property](../intro/properties.md)) as 
well as [numeric](../intro/numeric.md) value (using **val** 
[property](../intro/properties.md)), that fits chosen 
[underlying type](#underlying-type). The **&lt;validValue&gt;**-es may be listed
in any order, not necessarily sorted.

Every **&lt;validValue&gt;** supports extra properties:
- **description** - Extra description and documentation on how to use the value.
- **displayName** - String specifying how to name the value in various analysis tools.
- **sinceVersion** - Version of the protocol when the value was introduced.
- **deprecated** - Version of the protocol when the value was deprecated.

All these extra properties are described in detail in 
[Common Properties of Fields](common.md).

#### Default Value
The default value of the **&lt;enum&gt;** field when constructed can be specified
using **defaultValue** property. If not specified, defaults to **0**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8" defaultValue="5">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="0x1b" />
        </enum>
    </fields>
</schema>
```
The default value can also be specified using the name of one of the 
**&lt;validValue&gt;**-es:
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8" defaultValue="Val2">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="0x1b" />
        </enum>
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
        <enum name="SomeEnumField" type="uint16" endian="little">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="0x1b" />
        </enum>
    </fields>
</schema>
```

#### Serialization Length
The [underlying type](#underlying-type) dictates the serialization length
of the **&lt;enum&gt;** field. However there may be protocols, that limit serialization
length of the field to non-standard lengths, such as **3** bytes. In this case
use **length** property to specify custom serialization length.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big">
    <fields>
        <enum name="SomeEnumField" type="uint32" length="3">
            <validValue name="Val1" val="0x0" />
            <validValue name="Val2" val="0x0a0b0c" />
            <validValue name="Val3" val="0xffffff" />
        </enum>
    </fields>
</schema>
```
<span style="color:red">**IMPORTANT**</span>: When **length** property is used with variable length 
[underlying types](#underlying-type) (**intvar** and **uintvar**), 
it means **maximum** allowed length.

#### Length in Bits
**&lt;enum&gt;** field can be a member of [&lt;bitfield&gt;](bitfield.md) field.
In this case the serialization length may be specified in bits using **bitLength**
[property](../intro/properties.md).
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big">
    <fields>
        <bitfield name="SomeBitfield">
            <enum name="SomeEnumMember" type="uint8" bitLength="3">
                <validValue name="Val1" val="0" />
                <validValue name="Val2" val="1" />
                <validValue name="Val3" val="2" />
            </enum>

            <enum name="SomeOtherEnumMember" type="uint8" bitLength="5">
                <validValue name="Val1" val="5" />
                <validValue name="Val2" val="12" />
                <validValue name="Val3" val="20" />
            </enum>
        </bitfield>
    </fields>
</schema>
```

#### Hex Assignment
The code generator is expected to generate appropriate **enum** types using
decimal values assigned to enumeration names. However, some protocols may list 
valid values using hexadecimal format. To make the reading of the generated code
more convenient, use **hexAssign** [property](../intro/properties.md) with
[boolean](../intro/boolean.md) value to force code generator make the assignments
using hexadecimal values.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big">
    <fields>
        <enum name="SomeEnumField" type="uint8" hexAssign="true">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="0x1b" />
        </enum>
    </fields>
</schema>
```
The generated enum type is expected to look something like this:
```cpp
enum class SomeEnumFieldVal
{
    Val1 = 0x00,
    Val2 = 0x05,
    Val3 = 0x1b
};
```
instead of 
```cpp
enum class SomeEnumFieldVal
{
    Val1 = 0,
    Val2 = 5,
    Val3 = 27
};
```

#### Allow Non-Unique Values
By default, non-unqiue values are not allowed, the code generator must report
an error if two different **&lt;validValue&gt;**-es use the same value of **val**
property. It is done as protection against copy-paste errors. However,
**CommsDSL** allows usage of non-unique values in case **nonUniqueAllowed** 
[property](../intro/properties.md) has been set to **true**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big">
    <fields>
        <enum name="SomeEnumField" type="uint8" nonUniqueAllowed="true">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="OtherNameForVal2" val="5" />
        </enum>
    </fields>
</schema>
```

#### Version Based Validity
The code generator is expected to generate functionality checking that 
**&lt;enum&gt;** field contains a valid value. By default any specified 
**&lt;validValue&gt;** is considered to be valid regardless of version it was
introduced and/or deprecated. However, it is possible to force code generator to
generate validity check code that takes into account reported version of the
protocol by using **validCheckVersion** [property](../intro/properties.md), which
is set to **true**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema name="MyProtocol" endian="big" version="5">
    <fields>
        <enum name="SomeEnumField" type="uint8" validCheckVersion="true">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="10" sinceVersion="2" />
            <validValue name="Val4" val="15" sinceVersion="3" deprecated="4"/>
        </enum>
    </fields>
</schema>
```
In the example above values **0** and **5** will always be considered valid. However
value **10** will be considered valid only if reported protocol version is
greater than or equal to **2**. The value **15** will be considered valid only
for protocol version **3**.

#### Referencing Other **&lt;enum&gt;** Values
Any specified **&lt;validValue&gt;** can be referenced by other fields 
(not only **&lt;enum&gt;**) when specifying [numeric](../intro/numeric.md) 
value of some property. To reference it the **&lt;enum&gt;** name 
must be specified followed by a **.** (dot) and name of the chosen **&lt;validValue&gt;**

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="10"/>
        </enum>
        
        <int name="SomeIntField" type="uint32" defaltValue="SomeEnumField.Val2" />
    </fields>
</schema>
```
If **&lt;enum&gt;** field resides in a namespace, add it to the reference string
as well.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <ns name="ns1"
        <fields>
            <enum name="SomeEnumField" type="uint8">
                <validValue name="Val1" val="0" />
                <validValue name="Val2" val="5" />
                <validValue name="Val3" val="10"/>
            </enum>
            
            <int name="SomeIntField" type="uint32" defaltValue="ns1.SomeEnumField.Val2" />
        </fields>
    </ns>
</schema>
```

Use [properties table](../appendix/enum.md) for future references.
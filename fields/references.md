## Referencing Values of Other Fields
Quite often there is a need to reuse (or reference) some other values already
defined and used for some other fields. The **v1** of this specification
allowed referencing the external [&lt;enum&gt;](enum.md) **validValue**-s only, while
**v2** of this specification extends such functionality to other fields as well.
In general, when the other field is referenced its **defaultValue** is taken, 
unless inner value is referenced, such as **validValue** of the [&lt;enum&gt;](enum.md)
field or **special** value of the [&lt;int&gt;](int.md) field.

#### Referencing Values Defined in **&lt;enum&gt;**-s
Any specified **&lt;validValue&gt;** can be referenced by other numeric fields 
(not only **&lt;enum&gt;**) when specifying [numeric](../intro/numeric.md) 
value of some property. To reference it, the **&lt;enum&gt;** name 
must be specified followed by a **.** (dot) and name of the chosen **&lt;validValue&gt;**

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8" defaultValue="Val2">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="10"/>
        </enum>
        
        <int name="SomeIntField" type="uint32" defaultValue="SomeEnumField.Val2" />
    </fields>
</schema>
```
In the example above the **defaultValue** of the *SomeIntField* will be **5**.

When [&lt;enum&gt;](enum.md) is referenced by its name, its **defaultValue** 
is taken.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8" defaultValue="Val2">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="10"/>
        </enum>
        
        <int name="SomeIntField" type="uint32" defaultValue="SomeEnumField.Val3">
            <special name="S1" val="SomeEnumField" />
            <special name="S2" val="SomeEnumField.Val1" />
        </int>
    </fields>
</schema>
```
In the example above the **defaultValue** of the *SomeIntField* is **10**, the
value of the *S1* special is **5** (equals to **defaultValue** of *SomeEnumField*),
and value of the *S2* special is **0**.

Floating point fields can also reference values defined in 
[&lt;enum&gt;](enum.md) fields.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <enum name="SomeEnumField" type="uint8" defaultValue="Val2">
            <validValue name="Val1" val="0" />
            <validValue name="Val2" val="5" />
            <validValue name="Val3" val="10"/>
        </enum>
        
        <float name="SomeFloatField" type="double" defaultValue="SomeEnumField.Val1">
            <special name="S1" val="SomeEnumField.Val2" />
        </float>
    </fields>
</schema>
```

#### Referencing Values Defined in **&lt;int&gt;**-s
Similar to [&lt;enum&gt;](enum.md) the inner value of [&lt;int&gt;](int.md)
field can be referenced by other numeric fields.

```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <int name="SomeIntField" type="uint32" defaultValue="10">
            <special name="S1" val="0" />
            <special name="S2" val="5" />
        </int>
    
        <enum name="SomeEnumField" type="uint8" defaultValue="SomeIntField.S1">
            <validValue name="Val1" val="SomeIntField.S2" />
            <validValue name="Val2" val="SomeIntField" />
        </enum>
        
        <float name="SomeFloatField" type="double" defaultValue="SomeIntField.S2">
            <special name="S1" val="SomeIntField" />
        </float>
    </fields>
</schema>
```
In the example above **defaultValue** of *SomeEnumField* is **0**, 
**validValue** *Val1* equals to **5**, and **validValue** *Val2* equals to
**10**.

Also the **defaultValue** of *SomeFloatField* is **5.0**, while
value of its *S1* special is **10.0**.

#### Referencing Values Defined in **&lt;set&gt;**-s
The **defaultValue** property of any element of the [&lt;set&gt;](set.md) field
can also be referenced.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <set name="SomeSetField" length="1" defaultValue="false">
            <bit name="B0" idx="0" defaultValue="true" />
            <bin name="B1" idx="1" />
        </set>

        <set name="SomeOtherSetField" length="1" defaultValue="SomeSetField" reservedValue="SomeSetField.B0">
            <bit name="B5" idx="5" defaultValue="SomeSetField.B1" />
            ...
        </set>
    </fields>
</schema>
```
In the example above the **defaultValue** of *SomeOtherSetField* is **false**
(same as **defaultValue** of *SomeSetField*), the **reservedValue** of
*SetOtherField* is **true** (same as **defaultValue** of *SomeSetField.B0*), and
the **defaultValue** of *SomeOtherSetField.B5" is **false** (same as 
**defaultValue** of *SomeSetField.B1*).

Other numeric fields, such as [&lt;enum&gt;](enum.md), [&lt;int&gt;](int.md),
and [&lt;float&gt;](float.md) can also reference boolean values of [&lt;set&gt;](set.md),
which will result in numeric values been either **0** or **1**.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <set name="SomeSetField" length="1" defaultValue="false">
            <bit name="B0" idx="0" defaultValue="true" />
            <bin name="B1" idx="1" />
        </set>

        <float name="SomeFloatField" type="float" defaultValue="SomeSetField.B0" />
    </fields>
</schema>
```
The definition above will result in **defaultValue** of *SomeFloatField* to be
**1.0**.

#### Referencing Values Defined in **&lt;float&gt;**-s
Similar to [&lt;int&gt;](int.md) it is possible to reference [&lt;float&gt;](float.md) values 
used in **defaultValue** property and/or as **&lt;special&gt;** value.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <float name="SomeFloatField" type="double" defaultValue="nan">
            <special name="S1" val="inf" />
        </float>

        <float name="SomeOtherFloatField" type="double" defaultValue="SomeFloatField.S1">
            <special name="S1" val="SomeFloatField" />
        </float>
        
    </fields>
</schema>
```
In the example above **defaultValue** of *SomeOtherFloatField* is **inf**, 
while value of *SomeOtherFloatField.S1* special is **nan**.

#### Referencing Values Defined in **&lt;string&gt;**-s
When referencing values of [&lt;string&gt;](string.md) fields there is a need
to differentiate between a reference to external field and a genuine string
value. To do so the `^` prefix was introduced. If a property value, that 
requires a string, starts with `^` it means external reference and error
must be reported if referenced field is not found.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <string name="SomeString" defaultValue="hello" />
        <string name="SomeOtherString" defaultValue="^SomeString" />
    </fields>
</schema>
```
In the example above the **defaultValue** of *SomeOtherString* field is
**hello**.

If there is a need to define a genuine string value that starts with `^` character,
then there is a need to escape it with `\`.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <string name="SomeOtherString" defaultValue="\^SomeString" />
    </fields>
</schema>
```
In the example above the **defaultValue** of *SomeOtherString* field is
**^SomeString**.

The question may arise what if a genuine value string needs to start with `\^`.
In this case just add additional `\` at the front.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <string name="SomeOtherString" defaultValue="\\^SomeString" />
    </fields>
</schema>
```
In the example above the **defaultValue** of *SomeOtherString* field is
**\^SomeString**. 

The bottom line: any **prefix** sequence of `\` followed by the `^` will
result in drop of one `\` in the final string value. In case there is any
other character used in the middle, the string value remains as is.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <string name="String1" defaultValue="\\SomeString" />
        <string name="String2" defaultValue="\.\^SomeString" />
    </fields>
</schema>
```
In the example above the **defaultValue** of *String1* field is
**\\SomeString** because there is no `^` character after `\` and 
the **defaultValue** of *String2* field is **\.\^SomeString** because
the sequence of `\` is interrupted by `.`. 

**NOTE**, that string referenced can be useful when [&lt;enum&gt;](enum.md)
field is used to specify numeric message IDs.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <string name="Msg1Name" defaultValue="Message 1" />
        <string name="Msg2Name" defaultValue="Message 2" />
        
        <enum name="MsgId" type="uint8" semanticType="messageId">
            <validValue name="Msg1" val="1" displayName="^Msg1Name" />
            <validValue name="Msg2" val="2" displayName="^Msg2Name" />
        </enum>
        
        <message name="Msg1" id="MsgId.Msg1" displayName="^Msg1Name">
            ...
        </message>
        
        <message name="Msg2" id="MsgId.Msg2" displayName="^Msg2Name">
            ...
        </message>
    </fields>
</schema>
```
In the example above the **displayName** property of a message is
expected to be the same as **displayName** property of appropriate
**&lg;validValue&gt;** of *MsgId* enum. Referencing common value insures
that the change to the name (if happens) propagates to appropriate fields.

#### Referencing Values Defined in **&lt;data&gt;**-s
When referencing values of [&lt;data&gt;](data.md) fields there is also a need
to differentiate between a reference to external field and a genuine data
value. For example the string `abcd` can be interpreted as valid field name
as well as valid hexadecimal bytes. As the result there is also a need 
to use `^` prefix (just like with **&lt;string&gt;** values) to indicate
external reference. 
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <data name="SomeData" defaultValue="12 34 56" />
        <string name="SomeOtherData" defaultValue="^SomeData" />
    </fields>
</schema>
```
The **defaultValue** of *SomeOtherData* will be `0x12 0x34 0x56`.

#### Referencing Values via **&lt;ref&gt;**-s
The [&lt;ref&gt;](ref.md) field is there to create an alias to other field. The
**CommsDSL** allows retrieving value for the [&lt;ref&gt;](ref.md) field as
if it was retrieved from the referenced field.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <int name="SomeInt" type="uint8" defaultValue="1">
            <special name="S1" val="5" />
        </int>
        <ref name="SomeRef" field="SomeInt" />
        <int name="SomeOtherInt" type="uint18" defaultValue="SomeRef.S1" />
    </fields>
</schema>
```
In the example above the **defaultValue** of *SomeOtherInt* is **5**, same as
the value of *SomeInt.S1*.

#### Referencing Values in Namespaces
In case referenced field resides in a namespace, add it to the reference string
as well. The same [referencing](../intro/references.md) rules apply.
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
            
            <int name="SomeIntField" type="uint32" defaultValue="ns1.SomeEnumField.Val2" />
        </fields>
    </ns>
</schema>
```

#### Referencing Values in &lt;bitfield&gt;-s or &lt;bundle&gt;-s
The **CommsDSL** also allows referencing values from member fields of a
[&lt;bitfield&gt;](bitfield.md) or a [&lt;bundle&gt;](bundle.md).
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <ns name="ns1"
        <fields>
            <bundle name="SomeBundle">
                <enum name="SomeEnumMember" type="uint8">
                    <validValue name="Val1" val="0" />
                    <validValue name="Val2" val="5" />
                    <validValue name="Val3" val="10"/>
                </enum>
                <int name="SomeIntMember" type="uint8" />
            </bundle>            
            
            <int name="SomeIntField" type="uint32" defaultValue="ns1.SomeBundle.SomeEnumField.Val2" />
        </fields>
    </ns>
</schema>
```
In the example above the **defaultValue** of *SomeIntField* is **5**.

#### Referencing Values in &lt;optional&gt;-s;
There are two forms of [&lt;optional&gt;](optional.md) fields. One references
external field, another defines it as a member.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <int name="SomeInt" type="uint8" defaultValue="1">
            <special name="S1" val="5" />
        </int>
        <optional name="Opt1" field="SomeInt" />
        <optional name="Opt2">
            <int name="SomeOptInt" type="uint8" defaultValue="1">
                <special name="S1" val="5" />
            </int>
        </optional>
    </fields>
</schema>
```
In case the [&lt;optional&gt;](optional.md) field references external field it
can **NOT** be used for value reference. The one that defines optional field
internally as a child element, can.
```
<?xml version="1.0" encoding="UTF-8"?>
<schema ...>
    <fields>
        <optional name="Opt2">
            <int name="SomeOptInt" type="uint8" defaultValue="1">
                <special name="S1" val="5" />
            </int>
        </optional>
        <int name="SomeOtherInt" type="int16" defaultValue="Opt2.SomeOptInt.S1" />
    </fields>
</schema>
```
**NOTE** that there is a need to reference internal member field by name.
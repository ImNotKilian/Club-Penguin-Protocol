`sys` XML Packet Data Type
==========================

__Data Type__              : `sys`

__Data Type description__ : `system`

Different files in this section represents different values for `action` send by the client.

## General Structure

```xml
<msg t='sys'>
    <body action = "ACTION*" [ t = '' ] r = '-1'> 
      [ <k><![CDATA[DATA]]></k> ]
      [ <vars> <var [n=''] t=''><![CDATA[DATA]]></var> ... </vars> ]
	</body>
</msg>
```

`...*` denotes the item is mandatory

`[...]` denotes the item is optional

Action `verChk`
==============

__Action name__ : `verChk`

__Action description__ : `API Version Check`


## Flow of XML Packet
1. Checks the API version of the server against the one supported by the client.
2. Responses to the client with a message - `apiOK` or `apiKO` respectively if API is supported or else.

## Packet Structure
```XML
<msg t='sys'>
  <body action='apiOK/apiKO' r='0'>
  </body>
</msg>
```
__NOTE__ : Here the `action` parameter takes the value - either 'apiOK', if API version is supported, or 'apiKO', if not supported.

### Latest API Version being supported : `153`

## Example of usage
```py
def APIVersionCheck (client, ver):
    if ver == 153: 
        client.sendXML (action = 'apiOK', r = 0)
    else:
        client.sendXML (action = 'apiKO', r = 0)
```

## Structure Example
```xml
<msg t='sys'>
  <body action='apiOK' r='0'>
  </body>
</msg>
```

```xml
<msg t='sys'>
  <body action='apiKO' r='0'>
  </body>
</msg>
```

Server Packet Protocol
======================

## General Packet Structure
* ### Server Authentication / XML Packets
    These are [XML](https://www.w3schools.com/xml/) packets sent by the server, in response to client's XML/Authentication request.
    
    __Structure__ 
    ```
    <msg t='sys'><body action='ACTION' r='-1'><var><![CDATA[INFORMATION]]></var></body></msg>
    ```
    
    Where,
    <table><tr> <th> ITEM </th> <th>DESCRIPTION</th> </tr><tr> <td> msg </td> <td> message sent, with appropriate type </td> </tr><tr> <td> t </td> <td> type of data sent </td> </tr><tr> <td> body  </td> <td> body of the message, with Syntax matching the type of <code>msg</code> declared </td> </tr><tr> <td> action </td> <td> main event to be performed </td> </tr><tr> <td> r </td> <td> room-id of the server, which response to received XML data</td> </tr><tr> <td> var </td> <td> variable parameter containing extra information, if any, strictly following syntax of type mentioned in <code>body</code>, if present, else type mentioned in <code>msg</code> </td> </tr></table> 
    
* ### Server Communication / XT Packets
    These are XT packets following special syntax as designed by CP developers. XT Packets are not as stable as XML and keeps changing from place to place, depending on what they are used to communicate.

    __Structure__ 
    
    ```
    %xt%c%ri%DATA%\0
    ```
    
    Where,
    <table><tr> <th> ITEM </th> <th>DESCRIPTION</th> </tr> <tr> <td> % </td> <td> XT packets delimiter </td> </tr>
	  <tr> <td> c </td> <td> category, which the data belongs to </td> </tr> <tr> <td> ri  </td> <td> Internal room-id, the unique number given by server to the virtual room client is in  according to the server </td> </tr> <tr> <td> DATA </td> <td> The information to be sent, which follows the Syntax of the <code>category </code></td> </tr> <tr> <td> \0 </td> <td>A null byte present at the end of every XT packets, is mandatory and denotes the end of the packet</td> </tr>  </table> 

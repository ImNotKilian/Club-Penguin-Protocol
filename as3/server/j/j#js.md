Action `j#js`
============

__Category name__ : `j#js`

__Category description__ : `Join client to server`

## Flow of XT Packet
1. Receive client ID, client's hashed password.
2. Check it against those generated during [World Login](https://github.com/Times-0/cp-protocol/blob/master/as3/server/login/sys/login/WorldLogin.md)
3. Disconnect with error packet `e` with error code `101`, if verification failed, else respond with `js` packet
4. If verified connect the client to server so that it can interact, send other xt packets and play.

## Successful Verification : `js` Packet Structure
```python
%xt%js%-1% MEMBER_STATUS % EPF_STATUS % MODERATOR_STATUS %
```
Where, 
<table>
  <tr> <th> PARAM </th> <th> TYPE </th> <th> DESCRIPTION  </th> </tr>
  <tr> <td> MEMBER_STATUS </td> <td> BOOLEAN INTEGER </td> <th> 1 if player has subscribed membership else 0 </th> </tr>
  <tr> <td> EPF_STATUS </td> <td> BOOLEAN INTEGER </td> <th> 1 if player is an EPF member else 0 </th> </tr>
  <tr> <td> MODERATOR_STATUS </td> <td> BOOLEAN INTEGER </td> <th> 1 if player is a moderator else 0 </th> </tr>
</table>

__NOTE__ : This being the only `j` packet with room internal id as `-1`

## Unsuccessful Verification : Error Packet Structure
```python
%xt%e%-1%101%
```

## Usage Example
```python
def handleJoinServerRequest (client, id, password):
    if not id == client.id or not client.checkDetails (password = password):
        client.sendXT ('e', 101)
        return client.disconnect("Incorrect credentials")
    
    JoinedServer = client.joinServer ()
    JoinedServer.addCallback (lambda *x: client.sendXT ('js', client.membershipStatus (), client.epf.getStatus (), client.mod_level))
    JoinedServer.addErrback (lambda x: client.disconnect (x.getErrorMessage ()))
    
    #Rest folllows, if any thatyou want to add once login
    
```

## Structure Example
```python
%xt%js%-1%1%1%1%
```
```python
%xt%js%-1%0%1%0%
```
```python
%xt%js%-1%1%0%1%
```
```python
%xt%js%-1%1%0%0%
```
```python
%xt%e%-1%1%101%
```

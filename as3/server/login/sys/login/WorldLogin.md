Action `login`, World Login
===========================

__Action__ : `Authenticates details sent and logs the client in`

## Flow of XML PACKET
1. You get the username, hashed password, SWID, ID, and login key from client
2. Verify it against the one you stored before during [Primary Login](https://github.com/Times-0/cp-protocol/blob/master/as3/server/login/sys/login/PrimaryLogin.md)
3. Throw error packet, `e` if not verified, else respond with `l` packet

## Successful Authentication : `l` Packet Structure
```python
%xt%l%-1%1%
```

## Unsuccessful Authentication : Error codes
When there are some errors during authentication, the server responds with the following message
```python
%xt%e%-1%ERROR_CODE%DETAILS%
```
Where `ERROR_CODE`takes the following valpes
<table>
  <tr> <th> ERROR </th> <th> DESCRIPTION  </th> </tr>
  <tr> <td> 101 </td> <td> Username doesn't exists, or Password sent is incorrect. Here  <code>DETAILS </code> column is empty</td> </tr>
  <tr> <td> 601 </td> <td> Penguin is banned. Here <code>DETAILS</code> is the no of hours remaining in penguin's ban. Example, 2 hours </td> </tr>
  <tr> <td> 3 </td> <td> Penguin already logged in the same server selected. </td>
</table>

## Usage Example
```python
def handleLogin (client, username, password, swid, loginkey):
    client.username = username
    if not client.database.userExists (username):
        client.sendXT ('e', 101)
        return client.disconnect ("Username doesn't exist")
    
    if client.isLoggedIn ():
        client.sendXT ('e', 3)
        return client.disconnect ("Penguin already in")
    
    if client.isBanned ():
        client.sendXT (601, client.hoursBanned ())
        return client.disconnect ("Penguin Banned")
         
    client.generateDetails ()
    
    if not client.checkDetails (password, swid, loginkey):
        client.sendXT ('e', 101)
        return client.disconnect  ("Incorrect credentials")
        
    client.login ()
    client.sendXT ('l', 1)
    
```

## Structure Example
```python
%xt%l%-1%1%
```

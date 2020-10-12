Action `login`, Primary Login
=============================

__Action__ : `Authenticates the client details sent`

## Flow of XML Packet
1. You get the username, hashed password from  client
2. Verify if the username exists
3. Authenticate the hashed password sent by client, against the one in the database
4. Respond with `l` Packet, if 2, 3 turns out to be true, else respond with `e` Packet. 

## Successful Authentication : `l` Packet Structure
```python
%xt%l%-1%PENGUIN_DETAILS%LOGIN_KEY%FRIENDS_KEY%WORLD_SERVER_DETAILS%PENGUIN_EMAIL%
```
Where,
<table>
  <tr> <th> ITEM </th> <th> VALUE </th> <th> DESCRIPTION  </th> </tr>
  <tr> <td> PENGUIN_DETAILS </td> <td> <code>ID|SWID|Username|Password|NULL|45|2</code> </td><td>ID, SWID are details from db, Username is client's username, Password is the hashed password. NULL, 45, and 2 are constants denoting languages.</td></tr>
  <tr> <td> LOGIN_KEY </td> <td> LOGIN_KEY </td> <td> A unique key given to client upon successful login, used during world server authentication </td> </tr>
  <tr> <td> FRIENDS_KEY </td> <td> FRIENDS_KEY  </td> <td> A special key given to each client upon registration. Unique and constant for same client.  </td> </tr>
  <tr> <td> WORLD_SERVER_DETAILS </td> <td> <code>SERVER_ID,USER_COUNT|SERVER_ID,USER_COUNT|...</code>  </td> <td> Follows a pattern, representing no of users active in the world server with respective server id. Example usage <code>101,3|345,120|207,0</code> </td> </tr>
  <tr> <td> PENGUIN_EMAIL </td> <td> PENGUIN_EMAIL </td>  <td> First letter of email followed by 3 '*' and then the email service provider. Example, <code>p***@gmail.com</code> </td> </tr>
</table>

## Unsuccessful Authentication : Error codes
When there are some errors during authentication, the server responds with the following message
```
%xt%e%-1%ERROR_CODE%DETAILS%
```
Where `ERROR_CODE`takes the following values

<table>
  <tr> <th> ERROR </th> <th> DESCRIPTION  </th> </tr>
  <tr> <td> 101 </td> <td> Username doesn't exists, or Password sent is incorrect. Here  <code>DETAILS </code> column is empty</td> </tr>
  <tr> <td> 601 </td> <td> Penguin is banned. Here <code>DETAILS</code> is the no of hours remaining in penguin's ban. Example, 2 hours </td> </tr>
</table>

## Usage Example
```python
def handlePrimaryLogin (client, username, password):
    if not client.database.userExists (username):
        client.sendXT ('e', 101)
        return client.disconnect ("Username doesn't exists")
    
    client.username = username
    
    if not client.passwordHashVerify (password):
        client.sendXT ('e', 101)
        return client.disconnect ("Incorrect password")
    
    client.generateLoginDetails ()
    
    if client.isBanned ():
        client.sendXT ('e', 601, client.hoursBanned ())
        return client.disconnect ("Penguin Banned")
    
    PENGUIN_DETAILS = '|'.join ([client.id, client.swid, client.username, password, 'NULL', '45, '2'])
    WORLD_SERVER_DETAILS = '|'.join ('{0},{1}'.format(server.id, server.users) for server in AVAILABLE_SERVERS)
    
    client.sendXT ('l', PENGUIN_DETAILS, client.loginKey, client.friendsKey, WORLD_SERVER_DETAILS, client.email())
```

## Structure Example
```php
%xt%l%-1%1001|{A23D-5718-56DF-55FA}|Rick|f261819e3322898as88923bdf21673aa|NULL|45|2%16689w777937A618%122834%100,20|101,0|102,30%p***@gmail.com%
```
```python
%xt%e%-1%101%
```
```python
%xt%e%-1%601%1.5%
```

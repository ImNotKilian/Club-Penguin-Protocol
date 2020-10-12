Action `j#jr`
============

__Category name__ : `j#jr`

__Category description__ : `Join client to room`

## Flow of XT Packet
1. Receive room id, and if present player coordinates requested
2. Remove player from his previous room, if any
3. Put player into a state of waddle 
4. Verify details, add client to requested room and remove from waddle if verified, else stay back to previous room
5. If not verified send error `e` packet if possible. 

## Successful Verification : `jr` Packet Structure
```python
%xt%jr% ROOM_INTERNAL_ID* % ROOM_EXTERNAL_ID* % ROOM_DETAILS** %
```
Where,

|PARAM|TYPE|DESCRIPTION|
------|----|-----------|
|ROOM_EXTERNAL_ID|INTEGER|(99 <) Unique number (< 1000) given to the requested room, as specified in crumbs|
|ROOM_INTERNAL_ID|INTEGER|Unique number given by server to each room, usually constant for same room|
|ROOM_DETAILS|PLAYER_DATA|Series of PLAYER_DATA active in room|
|PLAYER_DATA|PATTERN OF PLAYER DETAILS| ```ID\|Username\|Language id\|Color\|Head\|Face\|Neck\|Body\|Hand\|Feet\|Photo\|Pin\|x-axis\|y-axis\|Frame\|Membership status\|Subscribed membership days remaining\|Avatar\|NULL\|Party info\|Walking pufile id (if walking with puffle)\|Walking pufile head id (if walking with puffle)```|

```python
%xt%ap% ROOM_INTERNAL_ID* % PLAYER_DATA %
```
Where,

|PARAM|TYPE|DESCRIPTION|
|-----|----|-----------|
|ROOM_INTERNAL_ID|INTEGER|Unique number given by server to each room, usually constant for same room|
|PLAYER_DATA|PATTERN OF PLAYER DETAILS| ```ID\|Username\|Language id\|Color\|Head\|Face\|Neck\|Body\|Hand\|Feet\|Photo\|Pin\|x-axis\|y-axis\|Frame\|Membership status\|Subscribed membership days remaining\|Avatar\|NULL\|Party info\|Walking pufile id (if walking with puffle)\|Walking pufile head id (if walking with puffle)```|

__NOTE__ : 
* `...*` - Must be necessarily same as in server. 
* `...**` - Number of times this param occurs varies, here it is the number of clients in room
* `ap` Should be sent only to all the clients active in the requested room
* `jr` Should be sent only to the client which requested to join the room

## Unsuccessful Verification: `e` Packet Structure
```python
%xt%e% ERROR_CODE %
```
Where `ERROR_CODE`takes the following values
<table>
  <tr> <th>ERROR CODE</th> <th>DESCRIPTION</th> </tr>
  <tr> <td>210</td> <td>Maximum users limit for room exceed</td> </tr>
  <tr> <td>999</td> <td>Non-member trying to access subscribed membership room</td> </tr>
  <tr> <td>213</td> <td>Requested room doesn't exists</td> </tr>
  <tr> <td>200</td> <td>Player already in same room as requested</td> </tr>
</table>

## Usage Example
```python
def handleJoinRoomRequest (client, room, x = 0, y = 0):
    room = client.server.GetRoom (externalID = room) 
    if not room:
        return client.sendXT ('e', 213)
    
    room += client # Shortcut for room.Add (client)
```

## Structure Example
```ruby
%xt%jr%10%100%200076|Rocket|45|2|13|134|988|234|456|78|1000|2346|2|234|12|1|31|12|NULL||||%1335|Jack|45|2|23|634|98|64|198|87|1090|236|2|234|12|1|31|12|NULL||||%
```
```ruby
%xt%ap%10%1335|Jack|45|2|23|634|98|64|198|87|1090|236|2|234|12|1|31|12|NULL||||%
```
```python
%xt%e%-1%210%
```

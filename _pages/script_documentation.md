---
layout: default
title: script-docs
permalink: /script-docs/
---

# SCRIPTING

All scripts should go in the ./scripts folder. Scripts should be named "user.scriptname.js", and can be uploaded with #up scriptname. EG if I am logged in as user "example", I make example.hello_world.js in ./scripts, and #up hello_world

Scripts follow the format

```javascript
function(context, args)
{

}
```

## Implemented scripts

## cash

```javascript
#hs.cash.balance()

#ms.cash.xfer_to({user:"user", amount:number})

#fs.cash.xfer_to_caller({amount:number}) 

    Transfers money to the script caller instead of you

#fs.cash.expose({user:"user"})

    Shows you the amount of cash a target has

#fs.cash.steal({user:"user", amount:number})

    Steals cash from a target with a breached breach node
```

## scripts

```javascript
#fs.scripts.get_level({name:"user.scriptname"})

#fs.scripts.core()

    Optional: {array:1}

#ms.scripts.me()

    Optional: {array:1}

#fs.scripts.public()

    Optional: {array:1} or {sec:number}
```

## msg

```javascript
#hs.msg.send({channel:"name", msg:"message"})

#hs.msg.tell({user:"name", msg:"hello"}) 

    Sends a private message to a user

#ms.msg.recent({channel:"name"})

    Defaults: {channel:"0000"}
    
    Optional: {count:number} or {array:1} or {tell:true}
    
    {tell:true} will instead retrieve tells

#ms.msg.manage()

    Takes 1 of: {join:channel} or {create:channel} or leave:channel
```

## users

```javascript
#ns.users.me()

    Optional: {array:1}
```

## item

```javascript
#fs.item.steal({user:"target", idx:0})

    Steals the users item at idx:id, costs 10 cash

    Optional: {confirm:true} to finalise payments

#fs.item.expose({user:"target"})

    Lists a users current items if they are breached

#ls.item.xfer_to({user:"user", idx:item_index})

#ms.item.manage()

    Optional: {array:1} or {full:1}
    
    Optional: Take 1 of: {load:item_index} or {unload:item_index} 
    
    {full:1} gives detailed item information

#ls.item.bundle_script({name:"scriptname", idx:0, tag:"shrt_tag"})

    Inserts the source of a script (host.scriptname) into a bundle at idx:id. Tag modifies the item name, must be 8 characters or less

#ls.item.cull({idx:0})

    Unloads and deletes the item at idx:id

#ns.item.register_bundle({name:"arbitraryname", idx:0})

    Registers a bundle at idx:id to be run as host.arbitraryname()
```

## nodes

```javascript
#ls.nodes.manage()

    Displays nodes and attached locks

#ls.nodes.view_log({user:"user", NID:id})

    Optional: {array:1}. Must have a clear breach path to the node in question
```

## net
    
    
```javascript
#ls.net.view({user:"name"})

    Views the network connections associated with a user or npc

#ls.net.map({user:"name", n:7})

    Views the network connections associated with a user or npc in 2d, with a max depth of n

#ns.net.access({user:"name"})

    Access an NPCs command interface
    
    Optional: Takes 1 of: {add_user:"name"} or {remove_user:"name"} or {view_users:true}
    
    Optional: {confirm:true} to finalise any payments
    
    Costs 200 cash to execute a command on an npc you have not added yourself to, in which case you must pass {confirm:true} to confirm payment

#fs.net.hack({user:"name", extra_args:"example"})

    Hacks a user at user:"name", passes args forwards

#ns.net.switch({user:"name"})

    Switches your terminal input to be run through this npc instead. Currently only works for npcs, and your main user that you are running on (from the user <username>) command
```

## Autocompletes

```javascript
#autos(test:"hello", example:"hithere", test3:1);

    This specifies autocompletes for other people to use

    The parser isnt that fancy so you may end up with slightly incorrect behaviour if you include colons in strings or use '\"'s

    Warning: You MUST terminate an #autos statement with a semicolon otherwise itll break
```
    
## DB

```javascript
#db.f({example:"query"})

    Returns a cursor-like object, use .array() or .first() to get the results. Takes an optional second projection argument

#db.u({doot:{$exists:true}}, {$set:{doot:"nope"}}) 

    Finds all documents which have a key doot, and sets their key doot to 'nope'

#db.i({key:"something", key2:"somethingelse"})

    Inserts a new document

#db.r({key:"something"})

    Deletes all documents that have a key:"something"
```

## Misc

```javascript
#D("hello_there")

    Overwrites your return value with any #D strings, which are appended together with newlines
    
    Works even if the script breaks except for timeouts but will overwrite errors. Only works for caller

print("some_string")

    Gets printed onto the terminal prior to any return values
    
    Works even if the script breaks except for timeouts, will not overwrite errors. Works for everyone

timeout_yield()

    Deprecated, has no explicit functionality

```
    
## Other (non scriptable)

```javascript
#up scriptname

    Uploads a script to the server

#dry scriptname

    Tests uploading a script to the server, discarding changes

#private scriptname

    Makes a script private

#public scriptname

    Makes a script public

#remove scriptname 

    Removes a script from the server

#

    Lists all local scripts for your user

#dir

    Opens the script directory (shared between users)

#edit scriptname

    Creates or opens a script for editing, defaults to es6

#edit_es6 scriptname

    Creates or opens a script in es6/typescript mode. Will convert an es5 file to es6

#edit_es5 scriptname

    Same as above, but will convert an es6 file to es5

#open scriptname 

    Opens a file for editing, but does not create

#clear_autos

    Clears autocompletes

#shutdown

    Shuts down the client

#cls

    Clears the terminal and chat

#clear_term 

    Clears the main terminal window

#clear_chat

    Clears the main chat window

user <username>

    Changes user, create automatically (will be changed in a future update)

#delete_user <username> 

    Deletes a user, does not confirm or warn

```
    
## Calling Scripts from a String

While `#ns.script.name()` is the most straightforward way to call a hardcoded script, its also possible to call a script from a string or variable

When you call `#ns.script.name()`, it expands to `ns_call("script.name")()`. `ns_call("script.name")` returns a function object and does not call the function itself, so you may do `var x = ns_call("script.name"); x()`

You may additionally wish to use `ns_call("script.name")` directly in your code, which will work as expected

Example:

```javascript
this script is highsec due to hs_call
function(c, a)
{
	return hs_call("i20k.highsec")(); dont forget the second set of ()s!
}
```

# GENERAL

The console prompt you're given is a raw JS terminal, essentially of the form "return \<input\>". This means that if you enter 1+1, you get 2

Scripts are run through eg `#fs.script.name()`, or `#script.name()` which maps to nullsec

## The Sandbox

Due to the way its implemented, there are no restrictions on the sandbox, you should not be able to edit the global object in a meaningful way

If you are able to mess with any of the global properties in a way that carries over into another script, let me know
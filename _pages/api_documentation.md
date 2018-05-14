---
layout: default
title: api-docs
permalink: /api-docs/
---

# API

The official client uses websockets on 6760, the server will respond back to you in whichever mode you pick (text/binary), however be aware that commands that respond in binary will not work in text mode

As the websocket mode is new, there may be bugs, please let me know if you find any

### Deprecated

There is a deprecated HTTP implementation on 6750 that will be removed in a future update

client_poll and client_scriptargs are both deprecated and now undocumented. Use the JSON versions instead

## Client -> Server

Every client command should be preceded by "client_command ". A correct format for a request is "client_command #fs.scripts.core()"

Calls which directly hook into the realtime chat system should be of the form "client_chat #hs.msg.send({channel:"\<YOURCHAN\>", msg:"\<YOURMSG\>"})"

The "client_chat " prefix simply suppresses script output from the server, so other commands could be piped through here if desired

The #up command follows the format client_command "#up scriptname \<SCRIPT_DATA\>". Additionally, you may use #up_es6 to request es6/typescript compilation, which is slower

Polling is performed by the request "client_poll_json" - this is the main driver that fetches chat so polls should be around ~1s in interval. This will probably be changed in the future because ddos'sing my own server is a poor move

The "client_command register client" command should be sent if no key.key file is present. The response is "command ####register secret <128bytekey>". This 128 byte key should be saved and used to auth, it is not retrievable from the server

The "client_command auth client <128bytekey>" command should be sent to auth the client after a new connection is made to the server, or on reconnect

In the event that your http lib dislikes binary, you can use register client_hex and auth client_hex to process hex instead. The format is little endian. If you ask for hex, the response will be "command ####register secret_hex <128bytekeyashex>"

### Autocompletes

You may request an autocomplete from the server with the format "client_scriptargs_json script.name"

There is currently no way to batch autocompletes together, however with websockets being the default, this isn't so much of an issue

## Server -> client

Responses from the server to "client_command "s are of the form "command \<RESPONSE\>". Be aware that server responses generally may include colour codes EG `Dhello`, that you will be required to parse yourself

The full list of commands that will provoke a valid response are user <username>, #up, #dry, #remove, #public, #private, register client, auth client, auth client_hex and a JS command (which is any text which is not one of the former)

Responses to client_poll_json are in the format "chat_api_json JSON". The JSON format is: {"channels":[channel_list], "data":[{"channel":channel, "text":raw_chat_string}], "tells":[{"user":user, "text":raw_chat_string}]}, where array items may repeat indefinitely

The server sends no response for a "client_chat " command if you use the websocket endpoint. Responses from the server should be stashed in a file somewhere, and reloaded next script run

### Autocompletes

Responses to client_scriptargs_json are in the format "server_scriptargs_json JSON". The JSON format is: {"script":"scriptname", "keys":["key_1", "key_2"], "vals":["val_1, val_2"]}, where array items may repeat indefinitely

In the event a script does not exist, or is a bad scriptname, the response is "server_scriptargs_invalid_json JSON", where the JSON format is {"script":"scriptname"}. In the event that the request is unintelligable, the response is "server_scriptargs_invalid_json"

In the event you are being ratelimited, you will receive "server_scriptargs_ratelimit_json JSON", where the JSON format is {"script":"scriptname"}

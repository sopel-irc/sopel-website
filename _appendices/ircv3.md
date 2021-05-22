---
title: IRCv3 compatibility
migrated: true
source: wiki
order: 2000
---

From version 4.1.0, Sopel supports [IRCv3](https://ircv3.net/). This page details how different pieces of the new standard are supported.

## IRCv3.1
### Mandatory components
#### Capability negotiation
Capabilities can be requested by a plugin in its `setup` procedure, using `bot.cap_req`. This function can also be used to require that a capability *not* be used, and will handle making sure that there is no conflict. If there is, an exception will be raised, and the plugin can decide whether to fail (and leave itself unloaded) or handle the problem gracefully.

#### `multi-prefix`
From version 4.1.0, Sopel supports bitwise channel permissions. `bot.privileges` contains the privileges for each user in each channel. The privilege is a bitwise integer, which can be compared to constants for each known type of privilege. For example, `bot.privileges['#YourPants']['Embolalia'] & sopel.module.OP` will return `True` if Embolalia is an op in #YourPants. Using an inequality operator (e.g. `>=`) is often more useful in practice, as the above would return `True` only if Embolalia is reported as an op, but not if that user is an owner, despite that being a higher level. With `multi-prefix` enabled, the above would be `True` if Embolalia is an op, even if that user is also an owner, as this capability makes it easy for Sopel to recognize when a user has multiple levels.

`multi-prefix` is enabled by default on any server which supports it.

#### SASL Authentication
If the server supports SASL authentication, and the `auth_method` value in the `[core]` section of the config file is set to `sasl`, Sopel will attempt to authenticate with it. By default, the authentication mechanism given is `PLAIN`; this can be changed with the `auth_target` value in the `[core]` section of the config. No other mechanism has been tested. Regardless of the mechanism, the token sent will be a base-64 encoding of `'\0'.join((nick, nick, pass))`, where `nick` is the `auth_username` field of the `[core]` section of the config, or the bot's nickname, and `pass` is the provided SASL password.

Sopel can also be configured for SASL by specifying `server_auth_method = sasl`, `server_auth_username`, `server_auth_password`, `server_auth_sasl_mech` in place of the `[core]` fields of the bot config named in the preceding paragraph. The `auth_*` family of values takes precedence over these if both are set.

### Optional Extensions
#### `account-notify`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
#### `away-notify`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
#### `extended-join`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
#### `tls`
**This is not currently supported.**

## IRCv3.2 (pending)
### Mandatory components
#### Message tags
`trigger.tags` will contain a map of tag names to their values. If no tags are sent, or the server does not support tags, this will be empty.

There is currently no direct way to send a message with tags, though it *may* be possible with `bot.write`.
#### Message intents
If a message has an intent, it can be accessed with `trigger.tags['intent']`. Currently, there is no effort made to ensure that a message with an `ACTION` tag behaves the same way as one sent as `\x01ACTION message\x01`, and as such **it is recommended that plugins *not* request the `intents` capability**, as it may cause unexpected behavior in other plugins. Remedies are being explored for this in future versions.

There is currently no direct way to send a message with intents, though it *may* be possible with `bot.write`.
#### Metadata
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
#### `monitor`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
### Optional extensions
#### `server-time`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
#### `batch`
There is nothing using or requesting this extension by default. A plugin can request this extension with no known problems.
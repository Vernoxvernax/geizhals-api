
## Creating the Bearer-Authorization-Token:

> [!NOTE]
> * with `Base64` I actually mean `Base64URL` without padding (as required by spec)
> * the authorization for logged in users may be completely different and not require these tokens at all

Geizhals uses a `Bearer` authorization token for most of it's incoming requests.

A Bearer token usually consists of two base64 encoded strings and a signature. It's also commonly called a `Json Web Token` (`JWT`). An example of a *semi*-valid Geizhals web token:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjEyMzQ1Njc4OSwiaWF0IjoxMjM0NTY3ODksInJlcXVlc3RfZmluZ2VycHJpbnQiOiJ3aGF0c3RoaXM_IiwidGltZXN0YW1wIjoxMjM0NTY3OTAsInRva2VuX2lkIjoibW9iaWxlYXBwLTIwMjEtYW5kcm9pZCJ9.IxdEXmgDi5LUTTLz4BHNdohZb6D5vB3LvDr5rRmm8R0
```

The three parts are always separated by a full stop `.`:
___
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`:
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
This part is always the same, as it specifies which type of `JWT` it is.
___

`eyJleHAiOjEyMzQ1Njc4OSwiaWF0IjoxMjM0NTY3ODksInJlcXVlc3RfZmluZ2VycHJpbnQiOiJ3aGF0c3RoaXM_IiwidGltZXN0YW1wIjoxMjM0NTY3OTAsInRva2VuX2lkIjoibW9iaWxlYXBwLTIwMjEtYW5kcm9pZCJ9`:
```
{
  "exp": 123456789,
  "iat": 123456789,
  "request_fingerprint": "whatsthis?",
  "timestamp": 123456790,
  "token_id": "mobileapp-2021-android"
}
```
This json body contains the following information about the `JWT`:
* `iat` - issued at, (f.e. today)
* `exp` - expiration, (f.e. tomorrow)
* `timestamp` - the same as `iat`, but due to Geizhals's standard +1 second.
* `token_id` - always `mobileapp-2021-android`, also set by Geizhals

I saved the hard one for last: `request_fingerprint`.
A request fingerprint is a specifically formatted text that contains the following information:
* the request type: [`POST`, `GET`, `PUT`, ...]
* the domain/host, to which the request will be sent to
* if present, the json body (for `POST` requests)

A simplified example:
```
POST|api.geizhals.net||{\"lang\":\"de\"}
```

FPs for requests that **don't** contain a request body usually look like this:
```
GET|api.geizhals.net|device_id={}|
```
`device_id` being a url query.

The resulting string is then hashed with SHA256 and voilà, you got your request fingerprint.
___

`IxdEXmgDi5LUTTLz4BHNdohZb6D5vB3LvDr5rRmm8R0`:

This is the signature which should prove that no one has tampered with the token.
Its created from the combination of the header (1st part), the payload (2nd part) and the signature key.
Usually the entire `JWT` is provided by the server for the client to use.
Obviously this isn't how Geizhals's mobile apps work. Otherwise you wouldn't be able to change any of the payload data, like f.e. the language, or the search query.

You can make of this information what you want, but this hint is already very blatant.

> [!CAUTION]
> As you can tell, I've put a little effort into hiding the secret. If you plan to create your own repository on the internet, I urge you to either mask *it* in your own way (not just base64 encode/one-way hashing), or to just leave it out entirely. I do NOT want shortsighted script-kiddies DDoS-ing Geizhals, nor do I want the team to find *it* and end up locking down their api even more. **Be smart.**

Here is a small proof-of-concept Geizhals api-library: https://github.com/Vernoxvernax/geizhals-api-rs

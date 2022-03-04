% Web Authentication
% https://github.com/STL2600/web-auth-talk
% ![Link to Talk](images/qr-code.png) 

# Bearer Tokens / JWT

## Differences

 - Sent in the `Authorization` header instead of `Cookie`
 - Contains all content needed to authenticate users actions
 - No need to fetch external state

## Technical Specs

## Overview

`<header>.<payload>.<signature>`

## Header

`{ "alg": "RS256", "typ": "JWT" }`

encodes to

`eyAiYWxnIjogIlJTMjU2IiwgInR5cCI6ICJKV1QiIH0`

## Payload

```
{
  "iss": "https://login.my-website.com/",
  "iat": 1646408116,
  "exp": 1646409316,
  "aud": "https://my-website.com/",
  "https://my-website.com/username": "websiteAdminAccount",
  "https://my-website.com/isAdmin": true
}
```

encodes to

```
eyJpc3MiOiJodHRwczovL2xvZ2luLm15LXdlYnNpdGUuY29tLyIsImlhdCI6MTY0NjQwODExNiwiZXhwIjoxNjQ2NDA5MzE2LCJhdWQiOiJodHRwczovL215LXdlYnNpdGUuY29tLyIsInVzZXJuYW1lIjoid2Vic2l0ZUFkbWluQWNjb3VudCIsImlzQWRtaW4iOnRydWV9Cg
```

## Signature

`SIG_ALG(secret, base64(header) + . + base64(payload)`

## Full Token

`eyAiYWxnIjogIlJTMjU2IiwgInR5cCI6ICJKV1QiIH0.eyJpc3MiOiJodHRwczovL2xvZ2luLm15LXdlYnNpdGUuY29tLyIsImlhdCI6MTY0NjQwODExNiwiZXhwIjoxNjQ2NDA5MzE2LCJhdWQiOiJodHRwczovL215LXdlYnNpdGUuY29tLyIsInVzZXJuYW1lIjoid2Vic2l0ZUFkbWluQWNjb3VudCIsImlzQWRtaW4iOnRydWV9Cg.<SIGNATURE>`

## HTTP Header

`Authorization: Bearer eyAiYWxnIjogIlJTMjU2IiwgInR5cCI6ICJKV1QiIH0.eyJpc3MiOiJodHRwczovL2xvZ2luLm15LXdlYnNpdGUuY29tLyIsImlhdCI6MTY0NjQwODExNiwiZXhwIjoxNjQ2NDA5MzE2LCJhdWQiOiJodHRwczovL215LXdlYnNpdGUuY29tLyIsInVzZXJuYW1lIjoid2Vic2l0ZUFkbWluQWNjb3VudCIsImlzQWRtaW4iOnRydWV9Cg.<SIGNATURE>`

## Claims - Standard

 - `iss` Issuer: Identifies principal that issued the JWT.
 - `sub` Subject: Identifies the subject of the JWT.
 - `aud` Audience: Identifies the recipients that the JWT is intended for.
 - `exp` Expiration Time: Identifies the expiration time on and after which the JWT must not be accepted
 - `iat` Issued at: Identifies the time at which the JWT was issued. The value must be a NumericDate.

## Claims - Custom

 - Should be namespaced to avoid collisions
 - Can contain arbritrary data
 - Examples:
   - `https://my-website.com/username`
   - `https://my-website.com/isAdmin`

# Usage

## Authentication Service

 - Dedicated login server or identity provider
 - Accepts some form of authentication
 - Returns an access token in JWT format
 - That token is provided in the `Authorization: Bearer <token>` header

## Consuming Service

 - Parse the JWT into its `header`, `payload`, and `signature`
 - Validate that the header specifies a valid algorithm
 - Check the signature against the header and payload
 - Decode the payload by parsing first as base64url, then as JSON
 - Check that the `iss` and `aud` fields are correct
 - Check that the `iat` and `exp` are current for the token
 - Check application specific claims

# JWKS - JSON Web Key Sets

 - A `.well-known` url to allow a login server to expose valid keys and algorithms for JWT signatures
 - Supports multiple keys at once for seamless rotation
 - Removes the need to configure consuming servers with any secrets
 - Adds a `kid` to the header to identify the key that should be used

# Strengths

## Immune to CSRF

 - Since the browswer will not automatically provide the authentication credential, CSRF attacks will not work

## Stateless

 - All authentication data is provided in the token, no requests to an external server are needed

## Multi-System

 - The only coordination you need between an authorizing server and one consuming the authorization is a public key

# Weaknesses

## Must use JavaScript in the browser

 - Since the browser won't attach the token automatically, a site must make an explicit request with JS

## Difficult to expire

 - If you need to be able to reoke tokens before they expire, you must have some state system
 - Can lessen the need by having short session timeouts

## alg=none

 - Some imlementations of JWT parsers allow you to specify a null signature algorithm
 - Ensure you have a list of allowed signature algorithms

## Avoid shared secrets

 - You can use symmetric encryption to sign the token
 - Avoid this, as it means any service that can validate tokens, can also create them

# General Advice

 - For simple apps, stick to cookies
 - Use an identity provider when possible
 - Don't roll your own crypto

# Questions?

![Link to Talk](images/qr-code.png) 

[https://github.com/STL2600/web-auth-talk](https://github.com/STL2600/web-auth-talk)

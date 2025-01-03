---
title: "Hacking JWTs: A Comprehensive Guide"
date: 2025-01-03 12:49:00 +0000
categories: [Guides]
tags: [Web Application Hacking, Penetration Testing, Ethical Hacking]
---

JSON Web Tokens (JWTs) are becoming increasingly more common in modern web applications due to it's ease of use and the features it provides. Learning how to hack them will prove very useful especially in Web Application Hacking. In this blog post, I will take you through how to hack JWTs. Let's get into it!

## Introduction to JWTs

A JWT is a type of data structure that represents information about a user, such as their identity and authorization. They are a compact and self-contained way to securely transmit information between two parties as a JSON object.

JWTs are used for authentication, authorization and session handling in may modern applications such as APIs and Single Sign-On (SSO).

A JWT typically consists of three components. Each component is Base64-Url encoded and separated by dots:

- Header: This indicates the token type, which is JWT, and the signing algorithm that is used
- Payload: This is the actual body of the token which contains the claims. A claim is a piece of information provided for a specific entity such as token issuer and expiration.
- Signature: This part contains the signature for the JWT. The signature provides a method for verifying the token's authenticity.

The signature for a JWT is produced by combining the encoded header and payload values, a secret and then using the algorithm defined in the JWTs header to encrypt it.

```json
header:
{
  "alg" : "HS256",
  "typ" : "JWT"
}

Payload:
{
  "id" : 54546,
  "name" : "moondie",
  "role" : "admin"
}

Secret: secret_key
```

Representing the above as a JWT results in:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NTQ1NDYsIm5hbWUiOiJtb29uZGllIiwicm9sZSI6ImFkbWluIn0.srtYpLd0RsQET5glbP2XQvRSapwx0oe9uftq0ks0nyo
```

### JWT Signing Algorithms

- None: No algorithm is used for signing. This basically means the JWT cannot be verified.
- Symmetric signing: A symmetric signing algorithm such as HS265 creates the signature by appending a secret value to the header and body of the JWT before generating a hash value.
- Asymmetric signing: Creates the signature by using a private key to sign the header and body of the JWT.

For better understanding of Symmetric and Asymmetric encryption algorithms, check out this [post](https://www.geeksforgeeks.org/difference-between-symmetric-and-asymmetric-key-encryption/).

## Common JWT Vulnerabilities

### Missing or Improper Signature Verification

By design, servers don't usually store any information about the JWTs that they issue. Instead, each token is an entirely self-contained entity. Therefore, if the application doesn't verify the signature properly, an attacker can make changes to the token and use that for malicious purposes.

```json
{
    "id" : 545264,
    "username" : "moondie",
    "isAdmin" : false
}
```

Considering this is a JWT that isn't properly verified or signed, you can just change the value of 'username' to another username to be logged in as that other user. In that same sense, you could easily change the 'isAdmin' value to 'true', providing the you a very easy privilege escalation vector.

### Brute-forcing secret keys

Some signing algorithms such as HS256 use a string as the secret key. This string can be thought of as a password. If you are able to brute-force the secret key, you would be able to create JWTs with any header and payload values you like, then use the key to re-sign the token with a valid signature.

### Insecure Storage of Tokens

JWTs are are stored insecurely, for example in local storage, can easily be stolen using attacks such as XXS or other means. Once you successfully retrieve the token, you could log in, impersonate the user and perform actions on their behalf.

### Expiry and Revocation Issues

If JWTs are not properly issued with short expiration times or lack robust revocation mechanisms such as when a user logs out, once you get hold of a compromised token you could access resources and perform actions on behalf of the legitimate user over extended periods of time.

-------------------------------------------------------------------------------

This is a simple overview of JWTs and some of it's common Vulnerabilities. I specifically addressed some Vulnerabilities that would be relevant in the context of Bug Bounty Hunting. I hope you found this helpful and informative.

Till we meet again! :-)

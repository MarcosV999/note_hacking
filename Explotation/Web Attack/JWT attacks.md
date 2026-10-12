## Exploiting flawed JWT signature verification
```
{
    "username": "carlos",
    "isAdmin": false
}
```
If the server identifies the session based on this `username`, modifying its value might enable an attacker to impersonate other logged-in users. Similarly, if the `isAdmin` value is used for access control, this could provide a simple vector for privilege escalation.
### Accepting arbitrary signatures
JWT libraries typically provide one method for verifying tokens and another that just decodes them. For example, the Node.js library `jsonwebtoken` has `verify()` and `decode()`.
Occasionally, developers confuse these two methods and only pass incoming tokens to the `decode()` method. This effectively means that the application doesn't verify the signature at all.
### Accepting tokens with no signature
JWTs can be signed using a range of different algorithms, but can also be left unsigned. In this case, the `alg` parameter is set to `none`, which indicates a so-called "unsecured JWT". Due to the obvious dangers of this, servers usually reject tokens with no signature. However, as this kind of filtering relies on string parsing, you can sometimes bypass these filters using classic obfuscation techniques, such as mixed capitalization and unexpected encodings.
```
header: {"kid":"6aa6e203-b7b1-4a47-8c6d-e5fe50a7fd64","alg":"none"}
payload: {"iss":"portswigger","exp":1791759918,"sub":"administrator"}
signatura: empty
eyJraWQiOiI2YWE2ZTIwMy1iN2IxLTRhNDctOGM2ZC1lNWZlNTBhN2ZkNjQiLCJhbGciOiJub25lIn0%3d.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc5MTc1OTkxOCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.
```
###### Note: Even if the token is unsigned, the payload part must still be terminated with a trailing dot.
## Brute-forcing secret keys
Some signing algorithms, such as HS256, use an arbitrary, standalone string as the secret key. 
### Brute-forcing secret keys using hashcat
**Review the JWT Header:**  Decode the first part of the token to see the `'alg'` field. If it says 'HS256', it corresponds to mode `16500`.
```
hashcat -a 0 -m 16500 jwt.txt <wordlist>
result - jwt:<secret>
```

![378](Pasted%20image%2020261011182539.png)
## JWT header parameter injections
### Injecting self-signed JWTs via the jwk parameter
JWT headers often contain several other parameters.
- `jwk` (JSON Web Key) - Provides an embedded JSON object representing the key.
- `jku` (JSON Web Key Set URL) - Provides a URL from which servers can fetch a set of keys containing the correct key.
- `kid` (Key ID) - Provides an ID that servers can use to identify the correct key in cases where there are multiple keys to choose from. Depending on the format of the key, this may have a matching `kid` parameter.
### Injecting self-signed JWTs via the jwk parameter
You can see an example of this in the following JWT header:
```
{
    "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
    "typ": "JWT",
    "alg": "RS256",
    "jwk": {
        "kty": "RSA",
        "e": "AQAB",
        "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
        "n": "yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9m"
    }
}
```
You can exploit this behavior by signing a modified JWT using your own RSA private key, then embedding the matching public key in the `jwk` header.
![345](Pasted%20image%2020261011210118.png)
### Injecting self-signed JWTs via the jku parameter
Instead of embedding public keys directly using the `jwk` header parameter, some servers let you use the `jku` (JWK Set URL) header parameter to reference a JWK Set containing the key. When verifying the signature, the server fetches the relevant key from this URL.
```
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "75d0ef47-af89-47a9-9061-7c02a610d5ab",
            "n": "o-yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9mk6GPM9gNN4Y_qTVX67WhsN3JvaFYw-fhvsWQ"
        },
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "d8fDFo-fS9-faS14a9-ASf99sa-7c1Ad5abA",
            "n": "fc3f-yy1wpYmffgXBxhAUJzHql79gNNQ_cb33HocCuJolwDqmk6GPM4Y_qTVX67WhsN3JvaFYw-dfg6DH-asAScw"
        }
    ]
}
```
![379](Pasted%20image%2020261011213614.png)
![539](Pasted%20image%2020261011213316.png)

![](Pasted%20image%2020261011213533.png)
### Injecting self-signed JWTs via the kid parameter
Verification keys are often stored as a JWK Set. In this case, the server may simply look for the JWK with the same `kid` as the token. However, the JWS specification doesn't define a concrete structure for this ID - it's just an arbitrary string of the developer's choosing. If this parameter is also vulnerable to directory traversal, an attacker could potentially force the server to use an arbitrary file from its filesystem as the verification key.
```
{
    "kid": "../../path/to/file",
    "typ": "JWT",
    "alg": "HS256",
    "k": "asGsADas3421-dfh9DGN-AFDFDbasfd8-anfjkvc"
}
```
This is especially dangerous if the server also supports JWTs signed using a [symmetric algorithm](https://portswigger.net/web-security/jwt/algorithm-confusion#symmetric-vs-asymmetric-algorithms). In this case, an attacker could potentially point the `kid` parameter to a predictable, static file, then sign the JWT using a secret that matches the contents of this file.
**You could theoretically do this with any file**, but one of the simplest methods is to use `/dev/null`, which is present on most Linux systems. As this is an empty file, reading it returns an empty string. Therefore, signing the token with a empty string will result in a valid signature.

![295](Pasted%20image%2020261011220048.png)
![355](Pasted%20image%2020261011220147.png)

This success because /dev/null is empty, same than `k` property.
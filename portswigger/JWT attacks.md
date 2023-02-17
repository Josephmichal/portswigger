da## JWT attacks :
-->JWTs are most commonly used in authentication, session management, and access control mechanisms, so compromising these vulnerabilities can potentially compromise the entire website and its users.
-->JSON web tokens (JWTs) are a standardized format for sending cryptographically signed JSON data between systems
-->They can theoretically contain any kind of data, but are most commonly used to send information ("claims") about users as part of authentication, session handling, and access control mechanisms.
-->Unlike with classic session tokens, all of the data that a server needs is stored client-side within the JWT itself. This makes JWTs a popular choice for highly distributed websites where users need to interact seamlessly with multiple back-end servers.

### JWT format :
-->A JWT consists of 3 parts: a header, a payload, and a signature. These are each separated by a dot, as shown in the following example :

 `eyJraWQiOiI5MTM2ZGRiMy1jYjBhLTRhMTktYTA3ZS1lYWRmNWE0NGM4YjUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTY0ODAzNzE2NCwibmFtZSI6IkNhcmxvcyBNb250b3lhIiwic3ViIjoiY2FybG9zIiwicm9sZSI6ImJsb2dfYXV0aG9yIiwiZW1haWwiOiJjYXJsb3NAY2FybG9zLW1vbnRveWEubmV0IiwiaWF0IjoxNTE2MjM5MDIyfQ.SYZBPIBg2CRjXAJ8vCER0LA_ENjII1JakvNQoP-Hw6GG1zfl4JyngsZReIfqRvIAEi5L4HV0q7_9qGhQZvy9ZdxEJbwTxRs_6Lb-fZTDpW6lKYNdMyjw45_alSCZ1fypsMWz_2mTpQzil0lOtps5Ei_z7mM7M8gCwe_AGpI53JxduQOaB5HkT5gVrv9cKu9CsW5MS6ZbqYXpGyOG5ehoxqm8DL5tFYaW3lB50ELxi0KsuTKEbD0t5BCl0aCR2MBJWAbN-xeLwEenaqBiwPVvKixYleeDQiBEIylFdNNIMviKRgXiYuAvMziVPbwSgkZVHeEdF5MQP1Oe2Spac-6IfA`

-->ithile dot sredhiku.first dot verunath vere header.header kazhinjal payload.ath kazhinj main sathanam ie, signature
-->==header means aa jwt il use chytha algorith,type. payload means athil ulla user details.pine ullath signature aan.athayath ee signature idunath userin kitiya aa token user modify chythitila enn urapp varuthanan.athayath ee signature il header um pine payload undakum.ath randum base 64 aayi encode chythitundakum.athupole secret key um undakum.so matteth randum tamper chyan patiyalum ee secret key attack erinte kayil illengil onum chyan patila==.
-->payload il "sub": 12345  enn kanunath ID aan
-->The header and payload parts of a JWT are just base64url-encoded JSON objects. The header contains metadata about the token itself, while the payload contains the actual "claims" about the user. For example, you can decode the payload from the token above to reveal the following claims :

								{
								    "iss": "portswigger",
								    "exp": 1648037164,
								    "name": "Carlos Montoya",
								    "sub": "carlos",
								    "role": "blog_author",
								    "email": "carlos@carlos-montoya.net",
								    "iat": 1516239022
								}

-->In most cases, this data can be easily read or modified by anyone with access to the token. Therefore, the security of any JWT-based mechanism is heavily reliant on the `cryptographic signature.`

### JWT signature :
-->The server that issues the token typically generates the signature by hashing the header and payload. In some cases, they also encrypt the resulting hash. Either way, this process involves a secret signing key. This mechanism provides a way for servers to verify that none of the data within the token has been tampered with since it was issued:

-   As the signature is directly derived from the rest of the token, changing a single byte of the header or payload results in a mismatched signature.
    
-   Without knowing the server's secret signing key, it shouldn't be possible to generate the correct signature for a given header or payload.

-->If you want to gain a better understanding of how JWTs are constructed, you can use the debugger on `jwt.io` to experiment with arbitrary tokens.

### JWT vs JWS vs JWE :
-->The JWT specification is actually very limited. It only defines a format for representing information ("claims") as a JSON object that can be transferred between two parties
-->In practice, JWTs aren't really used as a standalone entity. The JWT spec is extended by both the JSON Web Signature (JWS) and JSON Web Encryption (JWE) specifications, which define concrete ways of actually implementing JWTs.
-->athyath jwt koode jws, jwe undakum ennartham
-->==In other words, a JWT is usually either a JWS or JWE token.When people use the term "JWT", they almost always mean a JWS token. JWEs are very similar, except that the actual contents of the token are encrypted rather than just encoded.==
-->For simplicity, throughout these materials, "JWT" refers primarily to JWS tokens, although some of the vulnerabilities described may also apply to JWE tokens.

## What are JWT attacks?
-->JWT attacks involve a user sending modified JWTs to the server in order to achieve a malicious goal. Typically, this goal is to bypass authentication and access controls by impersonating another user who has already been authenticated.
-->The impact of JWT attacks is usually severe. If an attacker is able to create their own valid tokens with arbitrary values, they may be able to escalate their own privileges or impersonate other users, taking full control of their accounts

## How do vulnerabilities to JWT attacks arise?
-->JWT vulnerabilities typically arise due to flawed JWT handling within the application itself
-->The various specifications related to JWTs are relatively flexible by design, allowing website developers to decide many implementation details for themselves. This can result in them accidentally introducing vulnerabilities even when using battle-hardened libraries.
-->These implementation flaws usually mean that the signature of the JWT is not verified properly
-->This enables an attacker to tamper with the values passed to the application via the token's payload. Even if the signature is robustly verified, whether it can truly be trusted relies heavily on the `server's secret key` remaining a secret
-->If this key is leaked in some way, or can be guessed or brute-forced, an attacker can generate a valid signature for any arbitrary token, compromising the entire mechanism.


-->In most cases, after modifying a JWT, you need to re-sign it using the correct key so that the signature corresponds to the new values in the header and payload. For simplicity, we recommend using the JWT Editor extension for this process.
-->The JWT Editor extension can only sign tokens using keys that you have added to its key store. You can either upload keys that you have obtained already, or use the built-in features for generating a brand new key.

-->the server doesn't actually know anything about the original contents of the token, or even what the original signature was. Therefore, if the server doesn't verify the signature properly, there's nothing to stop an attacker from making arbitrary changes to the rest of the token.

==============================================================

        Lab-1(JWT authentication bypass via unverified signature)
-->This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.
-->aadyam lab open chyuka enit login chyuka
-->login chythathin shesham namuk session cookie kitum.login chythathin shesham vere enthengilium request chyuka apol namude request header il cookie: session=eyJraWQiOiI2ODZiNWQ1YS0zODQ5LTQ2OTUtYTk1My03NTZjOTQ5NDgyYTIiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsIn...   enn kanam.so ithinte thudakathile ey nok.ey enn thudangunathoke jwt aan.
-->ini first dot kazhinjitulla payload il double click chyumbol namuk inspector tab il kanam ee base64 decode aayi kidakunath
-->so ee request repeater il iduka.enit request inte path maati /admin enn kodukuka enit send adikuka
-->response il namuk kanam administrator aayi login chythale ee pathilek pokan patumen
-->so nerthe pole repeater il vech jwt token ile first dot kazhinjitulla payload il double click chyka apol inspector il decode chythath kanam.athil "sub": "wiener"  ennakum undavuka.so ath mati administrator enn koduth save chyth send adikumbol namuk 200 ok kitunu.so aa response copy chyth browser il send chyth nokumbol kanam namuk admin panel kitiyirikunu
-->ini carlos inte account delete chyuka
-->JWT libraries typically provide one method for verifying tokens and another that just decodes them. For example, the Node.js library `jsonwebtoken` has `verify()` and `decode()`.
-->Occasionally, developers confuse these two methods and only pass incoming tokens to the `decode()` method. This effectively means that the application doesn't verify the signature at all.

==============================================================

### Accepting tokens with no signature :
-->Among other things, the JWT header contains an `alg` parameter. This tells the server which algorithm was used to sign the token and, therefore, which algorithm it needs to use when verifying the signature

          `{ "alg": "HS256", "typ": "JWT" }`
          
-->JWTs can be signed using a range of different algorithms, but can also be left unsigned. In this case, the `alg` parameter is set to `none`(lab), which indicates a so-called "unsecured JWT".
-->Due to the obvious dangers of this, servers usually reject tokens with no signature

       Lab-2(JWT authentication bypass via flawed signature verification)
-->This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.
-->adyam lab open chyth login chyuka.http history il poyal kanum namude session ennath jwt aan
-->so GET /my-account  request eduth repeater il iduka.enit path mati /admin ennaki send chyuka apol kanam administrator aayi login chythale ee path access chyan patu
-->so jwt token il poi payload part double click chyuka enit athile wiener mati administrator enn kodukuka
-->jwt ile header part il double click chyth athile "alg":"RS256"  ennath mati value "None" enn kodukuka
-->ini signature aayitulla part angane thane remove chyuka.so ipol token il header um payload mathrame ullu.NOTE: paylaod part inte avasanam dot undayirikanam
-->ini path /admin enn koduth send chyuka apol kanam namuk admin panel kitiyrikunu
-->so browser il carlos delete chyan kanum.intercept on chyth delete button click chyuka.enit session token namude malicious token paste chyuka apol account delete akum

==============================================================

## Brute-forcing secret keys :
-->Some signing algorithms, such as HS256 (HMAC + SHA-256), use an arbitrary, standalone string as the secret key. Just like a password, it's crucial that this secret can't be easily guessed or brute-forced by an attacker. Otherwise, they may be able to create JWTs with any header and payload values they like, then use the key to re-sign the token with a valid signature.
-->namuk hash cat use chyth weak aayitulla secret key bruteforce chyth kandupidikan patiyekum
-->Karanam chela apps weak secret keys use chyum

       Lab-3(JWT authentication bypass via weak signing key)
-->This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist
-->adyam lab open aakuka.enit login chyuka. GET /my-account enn request il jwt login undakum
-->aa request repeater il iduka.enit token copy chyth jwt.io enn site il itt test chyuka
-->avidd kanam namude username oke.ini enth venam ennal namuk ee tokente secret key brute force chyth kandupidikan patumon nokanam
-->WORDLIST->https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list
-->so kali yil poi hashcat vech test chyam :
-> `hashcat -a 0 -m 16500 <YOUR-JWT> /path/to/jwt.secrets.list`  enn koduth test chyuka.apol namuk kanam secretkey : secret1 ennan.ith urapp varuthan jwt.io il poi secret key nte avide secret1 enn kodukuka apol kanam sign same thane
-->so ini username wiener ennath mati administrator enn kodukuka.enit namuk athilninum kituna token eduth namude site il vann cookie extension il paste chyth refresh adikumbol namal admin aayi.ini carlos account delete aakuka.lab solve aayi
-->==If the server uses an extremely weak secret, it may even be possible to brute-force this character-by-character rather than using a wordlist.==
-->namuk secret key kitiyal ath thane aano secret key ennariyan jwt.io il pokuka enit avide signature il kitiya secret key kodukuka.so real secret key aanengil namude jwt namuk kitiya athe same jwt aayirikum ok.
-->NOTE : ==namuk kituna eth jwt yum ie, HS256 algorithm use chyuna eth jwt token um namal ithupole hashcat vech test chyanam.athin rockyou.txt file use chyth bruteforce chyth nokam ok==

==============================================================

## JWT header parameter injections :
-->According to the JWS specification, only the `alg` header parameter is mandatory
-->In practice, however, JWT headers (also known as JOSE headers) often contain several other parameters. The following ones are of particular interest to attackers.
-   `jwk` (JSON Web Key) - Provides an embedded JSON object representing the key.
    
-   `jku` (JSON Web Key Set URL) - Provides a URL from which servers can fetch a set of keys containing the correct key.
    
-   `kid` (Key ID) - Provides an ID that servers can use to identify the correct key in cases where there are multiple keys to choose from. Depending on the format of the key, this may have a matching `kid` parameter.

### Injecting self-signed JWTs via the jwk parameter :
-->The JSON Web Signature (JWS) specification describes an optional `jwk` header parameter, which servers can use to embed their public key directly within the token itself in JWK format.
-->A JWK (JSON Web Key) is a standardized format for representing keys as a JSON object.
-->You can see an example of this in the following JWT header:

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

-->Ideally, servers should only use a limited whitelist of public keys to verify JWT signatures. However, misconfigured servers sometimes use any key that's embedded in the `jwk` parameter.
-->You can exploit this behavior by signing a modified JWT using your own RSA private key, then embedding the matching public key in the `jwk` header.
-->The `kid` (key ID) claim is an optional header claim, used to specify the key for validating the signature.

       Lab-4(JWT authentication bypass via jwk header injection)
-->The server supports the `jwk` parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.
-->athayath jwk il verification chyanula key undakum.but ivide lab il server check chyunila aa key trusted resourse il ninum thane annen
-->adyam lab open chyth login chyth GET /my-account eduth repeater il ittu
-->enit /my-account mati /admin en koduth apol response il administrator aayi login chythale access ullu enu kanikum
-->so namal jwt editor il poi puthiya key generate chyanam.so namal rsa eduthu.enti ethengilum length kodukua.enit generate chyuka.lenth ethayalum kozhapam ila automatic aayi adjust aayikolum
-->ini thirich /admin aayitula mate request ile avide poi json webtoken tab il poi "sub":"administrator"  enu kodukuka enit sign click chyth namude key kodukuka.enit attack click chyth embeded jwk kodukuka ini repeater il ulla send button click chyuka
-->apol namuk kanam namal admin aayirikunu.ini carlos ine delete chyuka
-->ipo kathi.athayath mukilil theory part nok.njan paranju servers public keys oke white list chythitundakum.so misconfigured aaya servers token il JWK parameter il ulla eth keyum accept chyum.so namuk kitiya token il namal thane undakiya RSA private key kodukanam.athupole matching aaya public key jwk parameter il value aayikodukanam.athayath namal ivide jwk parameter inject chyukayan chyunath.paavam server maryathak implement chyathond jwk enn kandapol athile key aan nokendath enn vech ath process chythu

-->ithan namul inject chytha jwk(header mathrame ullu payload,sign illa ok) :

 {
  "kid": "57a72875-a238-4b87-8467-381405ce9812",
  "typ": "JWT",
  "alg": "RS256",
  "jwk": {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "57a72875-a238-4b87-8467-381405ce9812",
    "n": "xO_Y20LwBab5N8ZHcL-USEJRUfMdXbL9TcmZxDvJ14aCQja3OPiRU3yLH3TLZZrZ4h0MvhC_JvG3l6ElVqGmNaTfeSvUyCaZqQP_i-hZOq-DfIIf3PHl9KqtgMqKR9nGOGmwtExcalCkAoyPs9IiLberCKntuvba3DWQi-pbaJDFZHIy2DKn20MZoRYp-pdHr4LxnYFl-FI7HbGgrWaNa4ckHSe0zAAsAPfzhABXtz2YRGaMhJK6Xl5FyVHemS-3Y_EPjJplJN-HgGg4OG7Q7A_h9vcZbbHkNVvxdCSmTK8SlzYtyg8xt_vptRmAeGYZfKK4pG0Sh7XFw9hP0-KY0Q"
  }
}

-->ith jwt.io il ninum decode chythathan.token njan kanikunila.kanditum karyamonum ila

==============================================================

## Injecting self-signed JWTs via the jku parameter :
-->Instead of embedding public keys directly using the `jwk` header parameter, some servers let you use the `jku` (JWK Set URL) header parameter to reference a JWK Set containing the key. When verifying the signature, the server fetches the relevant key from this URL.
-->athayath mumbathe lab il namal kandu publick key jwk enna header il aan ullath.but ivide "jku" : "url"  enna header il public key undakum.athayath token verify chyumbol server ee jku enna header il koduthitulla site il poi avide ninum public key fetch chyum.so athan namal ivide exploit chyunath.
-->A JWK Set is a JSON object containing an array of JWKs representing different keys. You can see an example of this below.
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

-->JWK Sets like this are sometimes exposed publicly via a standard endpoint, such as `/.well-known/jwks.json`.

       Lab-5(JWT authentication bypass via jku header injection)
-->The server supports the `jku` parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.
-->aadyam lab open chyth login chyuka.enit history il /my-account request nokiyal kanam namude token kidakunath.so aa request eduth repeater il iduka.enit /my-account mati /admin enn kodukuka.apol kanam administrator aayi login chythale ee path access chyan patu enn
-->so **JWT Editor Keys** tab il pokuka.enit namuk puthiya rsa key undakam.size no problem enthengilum koduk ath pine automatic aayi adjust aayikolum
-->ini enth venamenal namude exploit server il poi (jku inte url aayi pretend chyunath) oru empty jwk aaknam.ivide exploit server il aan chyunath.real life il namal control chyuna site il chyanam

								{
								    "keys": [
								
								    ]
								}
								
-->ith body il iduka enit thirich namal undakiya rsa key ude tab il pokuka.enit aa key il right click chyth "copy public key as jwk"  copy chyuka enit ee empty jwk il idanam

{
    "keys": [
             {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "28383101-c154-4365-8342-5bb839ed270d",
    "n": "sFXUhXcX2HWcFUCHr-3TskZYQxm7EbpI7hFuxbdXReVaHRjXX_XFz3WECfwxSE0-ZGJNEVBfOkebrGln7hN_nhQKCoSbGCLQpuah3WkyZqu7obYT1s3Xnm_446fIEuZVD9puPY7eFY-C3MNkE9M29-57h-6JqO-wQvf3RWyOv0YbrGZuTXuYT_4rnl3o1hD_RMZVEqAdqWOnEdbZrsCUF4EMmHoEnzEC-6HKb6_edVu6RhE-KbY1a4HU3NN0VS8z_e1VeJLOswfZvCAKuothGWRR12QwV75_aR8rYCaevs610G-vahj71oB1TmIJSPAiFRXkLYim5x6UvMLR_k6bWw"
}
    ]
}

-->ingane undakum copy chyth store chyumbol exploit il 

-->ini namal second part ilek kadakukayan.so nere namal undakiya /admin request ilek pokuka(ie, nerthethe /my-account)
-->enit json web token tabil pokuka enit namal undakiya kid inte value ithil ulla kid il kodukuka. athayath kid meas priate key
-->ini aa jwt il puthiya oru "Jku"  parameter kodukam enit namude exploit server inte url value aayi kodukam.NOTE: jwt puthiya parameter aan.so next parameter idunathin mumb mumbathe parameter kazhinj coma idanam
-->enit payload section il poi "sub": "wiener"  ennath mati administrator enn kodukuka.enit thazhe ulla sign button click chyanam.enit ok click chyuka.NOTE: sign button mathram click chythal mati attak button click chyenda
-->so ee sign button click chyth ok adichathodkoodi namal modify chytha token correct signature il signed aayitund.ini ee request send chyuka.apol server ithil ulla jku parameter nokum apol namude exploit server il aan public key kidakunath enn manasilakum.enit aa key vech ee token il ulla signature verify chyum.so namal payload il name administrator enn ittathkond admin aayi maran patum.so response il kanam namuk admin panel kiti.so ini carlos ine delete chyuka
-->lab inte mukalil ulla theory part nok.namuk onnil kooduthal keys ithupole jku il ulla server il aaki idam.like whitelist chyukayan chyunath.so ee jku il ulla key mathrame accept chyu ok.

==============================================================

## Injecting self-signed JWTs via the kid parameter :
-->Servers may use several cryptographic keys for signing different kinds of data, not just JWTs. For this reason, the header of a JWT may contain a `kid` (Key ID) parameter, which helps the server identify which key to use when verifying the signature.
--> kid parameter il directory traversal chyan patum.so ath test chyth nokuka -> 
        `"kid": "../../path/to/file"`
-->This is especially dangerous if the server also supports JWTs signed using a [symmetric algorithm](https://portswigger.net/web-security/jwt/algorithm-confusion#symmetric-vs-asymmetric-algorithms). In this case, an attacker could potentially point the `kid` parameter to a predictable, static file, then sign the JWT using a secret that matches the contents of this file
-->You could theoretically do this with any file, but one of the simplest methods is to use `/dev/null`, which is present on most Linux systems. As this is an empty file, fetching it returns null. Therefore, signing the token with a Base64-encoded null byte will result in a valid signature.


           Lab-6(JWT authentication bypass via kid header path traversal)
-->This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the `kid` parameter in JWT header to fetch the relevant key from its filesystem.
-->aadyam lab open aaki login chyuka enit history il poi /my-account request eduth repeater il iduka
-->ee /my-account mati /admin enn kodukuka apol kanam administrator aayi login chythale ee pathilek access ullu enn
-->**JWT Editor Keys** tab  ilek pokuka enit click new symmetric key  chyuka.enit generate button click chyuka.NOTE: size vishayamalla enthengilum kodukuka
-->enit athile "K" parameter il Base64-encoded null byte (`AA==`)  kodukuka enit ok click chyuka
-->apol avide ath save aayi kidakunath kanam key.NOTE: symmetric key aayath kond thane publick key undakila.athi avide kanam.ie, publick key inte avide untick aayi kidakunath kanam

-->eni part 2 ilek kadakam.nerthethe /admin request ilek pokuka.enit json web token tab edukuka
-->In the header of the JWT, change the value of the `kid` parameter to a path traversal sequence pointing to the `/dev/null` file  ->  "kid": "../../../../../../../dev/null"
-->enit "sub":"wiener"  mati administrator enn kodukuka enit thazhe ull sing button click chyth ok adikuka.enit send adichal namuk admin panel kitum.enit carlos account delete akuka apol lab solve aayi.
-->enik ivide thazhe ulla sign button click chythapol sign chyan pateela athukond lab solve aayila.but karyangal manasilayi
-->==In this solution, we'll point the `kid` parameter to the standard file `/dev/null` and sign the token with a null byte. In practice, you can point the `kid` parameter to any file with predictable contents.==
-->If the server stores its verification keys in a database, the `kid` header parameter is also a potential vector for SQL injection attacks.

==============================================================

## Other interesting JWT header parameters :
-->The following header parameters may also be interesting for attackers:
- `cty` (Content Type) - Sometimes used to declare a media type for the content in the JWT payload. This is usually omitted from the header, but the underlying parsing library may support it anyway. If you have found a way to bypass signature verification, you can try injecting a `cty` header to change the content type to `text/xml` or `application/x-java-serialized-object`, which can potentially enable new vectors for XXE and deserialization attacks.
- `x5c` (X.509 Certificate Chain) - Sometimes used to pass the X.509 public key certificate or certificate chain of the key used to digitally sign the JWT. This header parameter can be used to inject self-signed certificates, similar to the [`jwk` header injection](https://portswigger.net/web-security/jwt#injecting-self-signed-jwts-via-the-jwk-parameter) attacks discussed above. Due to the complexity of the X.509 format and its extensions, parsing these certificates can also introduce vulnerabilities. Details of these attacks are beyond the scope of these materials, but for more details, check out [CVE-2017-2800](https://talosintelligence.com/vulnerability_reports/TALOS-2017-0293) and [CVE-2018-2633](https://mbechler.github.io/2018/01/20/Java-CVE-2018-2633).

==============================================================

## JWT algorithm confusion :
-->Even if a server uses robust secrets that you are unable to brute-force, you may still be able to forge valid JWTs by signing the token using an algorithm that the developers haven't anticipated. This is known as an algorithm confusion attack
-->Algorithm confusion attacks (also known as key confusion attacks) occur when an attacker is able to force the server to verify the signature of a JSON web token (JWT) using a different algorithm than is intended by the website's developers
-->If this case isn't handled properly, this may enable attackers to forge valid JWTs containing arbitrary values without needing to know the server's secret signing key.
-->confusion algorithm attack il namal oru server inekond force chyipich matoru algorithm vech verify chyan sramikunu

### Symmetric vs asymmetric algorithms :
-->JWTs can be signed using a range of different algorithms. Some of these, such as HS256 (HMAC + SHA-256) use a "symmetric" key. This means that the server uses a single key to both sign and verify the token. Clearly, this needs to be kept secret, just like a password.
-->athyath server thane ee key vech aadyam token sign cheyum.pineed varuna request ukal ellam ithe same key vech thane verify chyum
-->Other algorithms, such as RS256 (RSA + SHA-256) use an "asymmetric" key pair. This consists of a private key, which the server uses to sign the token, and a mathematically related public key that can be used to verify the signature.
-->athayath ivide server oru private key vech ee token sign chyum.enit pineed varuna request ukal ellam ithinte koode ulla public key vechan verify chyuka
-->server symmetric key aan use chyunathengil oru otta key mathrame undaku.so ath server oru password pole safe aayi keep chyanam
-->assymetric key use chyumbol oru private key undakum.so ath safe aayi keep chyanam. athupole publick key um undakum.ath safe ayi keep chyarila servers

### How do algorithm confusion vulnerabilities arise?
-->Algorithm confusion vulnerabilities typically arise due to flawed implementation of JWT libraries
-->Although the actual verification process differs depending on the algorithm used, many libraries provide a single, algorithm-agnostic method for verifying signatures. These methods rely on the `alg` parameter in the token's header to determine the type of verification they should perform.
-->The following pseudo-code shows a simplified example of what the declaration for this generic `verify()` method might look like in a JWT library :

 function verify(token, secretOrPublicKey){
    algorithm = token.getAlgHeader();
    if(algorithm == "RS256"){
        // Use the provided key as an RSA public key
    } else if (algorithm == "HS256"){
        // Use the provided key as an HMAC secret key
    }
}

-->Problems arise when website developers who subsequently use this method assume that it will exclusively handle JWTs signed using an asymmetric algorithm like RS256. Due to this flawed assumption, they may always pass a fixed public key to the method as follows:

			publicKey = <public-key-of-server>;
			token = request.getCookie("session");
			verify(token, publicKey);

-->In this case, if the server receives a token signed using a symmetric algorithm like HS256, the library's generic `verify()` method will treat the public key as an HMAC secret. This means that an attacker could sign the token using HS256 and the public key, and the server will use the same public key to verify the signature.
-->==NOTE : The public key you use to sign the token must be absolutely identical to the public key stored on the server. This includes using the same format (such as X.509 PEM) and preserving any non-printing characters like newlines. In practice, you may need to experiment with different formatting in order for this attack to work.==

## Performing an algorithm confusion attack :
-->An algorithm confusion attack generally involves the following high-level steps :

1) Obtain the server's public key

2) Convert the public key to a suitable format

3) Create a malicious JWT with a modified payload and the alg header set to HS256.

4) Sign the token with HS256, using the public key as the secret.

-->In this section, we'll walk through this process in more detail, demonstrating how you can perform this kind of attack using Burp Suite.

### Step 1 - Obtain the server's public key :
-->Servers sometimes expose their public keys as JSON Web Key (JWK) objects via a standard endpoint mapped to `/jwks.json` or `/.well-known/jwks.json`, for example. These may be stored in an array of JWKs called `keys`. This is known as a JWK Se :

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

-->Even if the key isn't exposed publicly, you may be able to extract it from a pair of existing JWTs. 

### Step 2 - Convert the public key to a suitable format :
-->Although the server may expose their public key in JWK format, when verifying the signature of a token, it will use its own copy of the key from its local filesystem or database. This may be stored in a different format.
-->In order for the attack to work, the version of the key that you use to sign the JWT must be identical to the server's local copy. In addition to being in the same format, every single byte must match, including any non-printing characters.
-->For the purpose of this example, let's assume that we need the key in X.509 PEM format. You can convert a JWK to a PEM using the JWT Editor extension in Burp as follows :

1.  With the extension loaded, in Burp's main tab bar, go to the **JWT Editor Keys** tab.
2.  Click **New RSA** Key. In the dialog, paste the JWK that you obtained earlier.
3.  Select the **PEM** radio button and copy the resulting PEM key.
4.  Go to the **Decoder** tab and Base64-encode the PEM.
5.  Go back to the **JWT Editor Keys** tab and click **New Symmetric Key**.
6.  In the dialog, click **Generate** to generate a new key in JWK format.
7.  Replace the generated value for the `k` parameter with a Base64-encoded PEM key that you just copied.
8.  Save the key.

### Step 3 - Modify your JWT :
--> Once you have the public key in a suitable format, you can modify the JWT however you like. Just make sure that the alg header is set to HS256. 

### Step 4 - Sign the JWT using the public key :
--> Sign the token using the HS256 algorithm with the RSA public key as the secret. 

     Lab-7(JWT authentication bypass via algorithm confusion)
-->This lab uses a JWT-based mechanism for handling sessions. It uses a robust RSA key pair to sign and verify tokens. However, due to implementation flaws, this mechanism is vulnerable to algorithm confusion attacks.
-->To solve the lab, first obtain the server's public key.public edukuka enathan adyathe task.ath theory part il paranjitund molil nokiyamathi
-->ivide hint tharunund ie, You can assume that the server stores its public key as an X.509 PEM file.
-->X.509 ennath public key certificate inte format aan.**X.509** is a standard format for **public key certificates**, digital documents that securely associate cryptographic key pairs with identities such as websites, individuals, or organizations.Common applications of X.509 certificates include SSL/TLS and HTTPS for authenticated and encrypted web browsing, signed and encrypted email via the S/MIME protocol, code signing, document signing, client authentication, and government-issued electronic ID
-->[PEM](https://www.ssl.com/faqs/how-can-i-get-my-certificates-in-pem-format/) (originally “**P**rivacy **E**nhanced **M**ail”) is the most common format for [X.509](https://www.ssl.com/faqs/what-is-an-x-509-certificate/) certificates, CSRs, and cryptographic keys. A PEM file is a text file containing one or more items in Base64 ASCII encoding, each with plain-text headers and footers (e.g. `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----`). A single PEM file could contain an end-entity certificate, a private key, or multiple certificates forming a complete chain of trust. Most certificate files downloaded from SSL.com will be in PEM format.
-->namal kanuna -----BEGIN CERTIFICATE----- sldjflksd... -----END CERTIFICATE----- enna certificate aan pem.ithan pem format.thazhe ulla reference must read
-->==so ivide indakuna flow enthenal public key namal eduthit ath private key aakukayan chyunath.mukalile theroy part il jwt library code und athil nokiyal manasilakum.athil randamathe comment vaichal manasilakum.athan namal exploit chyunath.athayath namal algoritm mati hs256 aakum apol server kitiya publick key direct secret key aayi edukum.karanam hs256 ennath symmetric algoritm aanalo so kitiya key secret key aakum apol ath vech namuk exploit chyam.simple aayi paranjal kitiya public key namal private key aakunu.athupole algorthm symmtric aakunu(hs256)==
-->so aadyam lab open chyuka enit login chyuka.enit histroy il poi /my-account request il poi nokiyal namuk jwt token kanam.so aa request eduth repeater il iduka
-->enit GET /my-account mati /admin ennaki send chyuka apol kanam administrator aayi login chythale aa path access aku enn
-->so browser il poi /jwks.json ennadikumbol namuk private key kitunund.so aa key namal copy chyuka ie, {"kty":"RSA","e":"AQAB","use":"sig","kid":"f57472da-4815-409f-90cf-9ea6a4507e1a","alg":"RS256","n":"vi495ddVCdB5vUygeXHnFOS9z26elni42tOgKcDqFrrTTBIJv8th13V1DElJSW6Xm6dpa3EJR1rRy0LuJgfUJvpce05aAAH2PiFkjWguUAhjp1ZYfgT6V_aoZR-4rOOzkkQGg0eESVglBfam9rsENEeOIVjI6gfR8h1TooobEd7AL77zREowoB0lkRuQvpvXrQ5zMTm45UdbZSN25paR1DpJVPcrJcKnJVJgxrh9cSCT_1D_gfK36saxjvvgUr9DayEf58RLaZhs4DS8kJKPswhT9QDNPkcL7g75iPzZ8DUnLBm3TJj3zDl4UAAlUxn-BmYVN_5S9aeDX2c4F49lqw"}

-->see molil ullathan copy chyenda reethi baaki ullathelam copy chyenda.ie, "keys":agnane ullathonum.
-->enit namal jwt editor il vann new rsa key edukuka enit jwk option ullath select chyuka.enit ee kitiya key angane thane aa box il paste chyth save chyuka.generate onum click chyenda
-->ini enth venamenal aa save chytha key right click chyuka enit "copy public key as PEM" select chyuka enit decoder il poi ith paste chyuka enit ith base64 il encode chyuka
-->enit thirich jwt editor il vann new symmetric key select chyuka.enit generate click chyuka enit athile k parameter il (k means key ennan ente nigamanam) aa base64 il encode chythath paste chyuka enit save click chyuka
-->ini thirich repeater il varuka enit json web token tab il po "alg": "HS256"  akuka athupole "sub": "administrator" ennakuka.enit sign button click chyth ee namal undakiya symmetric key vech sign chyuka.enint aa request send chythal namuk admin panel kitum.ini carlos ine delete chyuka lab solve aayi
-->REFERENCE MUST READ -> https://www.ssl.com/guide/pem-der-crt-and-cer-x-509-encodings-and-conversions/

==============================================================

## Deriving public keys from existing tokens :
-->In cases where the public key isn't readily available, you may still be able to test for algorithm confusion by deriving the key from a pair of existing JWTs
-->This process is relatively simple using tools such as `jwt_forgery.py`. You can find this, along with several other useful scripts, on the [`rsa_sign2n` GitHub repository](https://github.com/silentsignal/rsa_sign2n).
-->We(portswigger) have also created a simplified version of this tool, which you can run as a single command:  `docker run --rm -it portswigger/sig2n <token1> <token2>`
-->This uses the JWTs that you provide to calculate one or more potential values of `n`. Don't worry too much about what this means - all you need to know is that only one of these matches the value of `n` used by the server's key. For each potential value, our script outputs:

-   A Base64-encoded PEM key in both X.509 and PKCS1 format.
    
-   A forged JWT signed using each of these keys.

-->To identify the correct key, use Burp Repeater to send a request containing each of the forged JWTs. Only one of these will be accepted by the server. You can then use the matching key to construct an algorithm confusion attack.
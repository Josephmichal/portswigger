## OAuth 2.0 authentication :
-->While browsing the web, you've almost certainly come across sites that let you log in using your social media account. The chances are that this feature is built using the popular OAuth 2.0 framework
-->OAuth is a commonly used authorization framework that enables websites and web applications to request `limited access` to a user's account on another application
-->Crucially, OAuth allows the user to grant this access without exposing their login credentials to the requesting application
-->This means users can fine-tune which data they want to share rather than having to hand over full control of their account to a third party
-->The basic OAuth process is widely used to integrate third-party functionality that requires access to certain data from a user's account.For example, an application might use OAuth to request access to your email contacts list so that it can suggest people to connect with
-->==NOTE:Although OAuth 2.0 is the current standard, some websites still use the legacy version 1a. OAuth 2.0 was written from scratch rather than being developed directly from OAuth 1.0. As a result, the two are very different. Please be aware that the term "OAuth" refers exclusively to OAuth 2.0 throughout these materials.==
-->OAuth 2.0 was originally developed as a way of sharing access to specific data between applications. It works by defining a series of interactions between three distinct parties, namely a client application, a resource owner, and the OAuth service provider.
-   **Client application** - The website or web application that wants to access the user's data.
-   **Resource owner** - The user whose data the client application wants to access.
-   **OAuth service provider** - The website or application that controls the user's data and access to it. They support OAuth by providing an API for interacting with both an authorization server and a resource server.

-->There are numerous different ways that the actual OAuth process can be implemented. These are known as OAuth "flows" or "grant types". In this topic, we'll focus on the "authorization code" and "implicit" grant types as these are by far the most common. Broadly speaking, both of these grant types involve the following stages:
1.  The client application requests access to a subset of the user's data, specifying which grant type they want to use and what kind of access they want.
2.  The user is prompted to log in to the OAuth service and explicitly give their consent for the requested access.
3.  The client application receives a unique access token that proves they have permission from the user to access the requested data. Exactly how this happens varies significantly depending on the grant type.
4.  The client application uses this access token to make API calls fetching the relevant data from the resource server.

### OAuth grant types :
-->The OAuth grant type determines the exact sequence of steps that are involved in the OAuth process
-->The grant type also affects how the client application communicates with the OAuth service at each stage, including how the access token itself is sent. For this reason, grant types are often referred to as "OAuth flows".
-->The **client application** specifies which grant type it wants to use in the initial authorization request it sends to the OAuth service
-->There are several different grant types, each with varying levels of complexity and security considerations. We'll focus on the `authorization code` and `implicit` grant types as these are by far the most common.

### OAuth scopes :
-->For any OAuth grant type, the client application has to specify which data it wants to access and what kind of operations it wants to perform. It does this using the `scope` parameter of the authorization request it sends to the OAuth service.

==========================================================

## Authorization code grant type :
-->the client application and OAuth service first use redirects to exchange a series of browser-based HTTP requests that initiate the flow
-->The user is asked whether they consent to the requested access. If they accept, the client application is granted an "authorization code"
-->The client application then exchanges this code with the OAuth service to receive an "access token", which they can use to make API calls to fetch the relevant user data.
-->All communication that takes place from the code/token exchange onward is sent server-to-server over a secure, preconfigured back-channel and is, therefore, invisible to the end user. This secure channel is established when the client application first registers with the OAuth service. At this time, a `client_secret` is also generated, which the client application must use to authenticate itself when sending these server-to-server requests.
-->As the most sensitive data (the access token and user data) is not sent via the browser, this grant type is arguably the most secure. Server-side applications should ideally always use this grant type if possible.

#### 1. Authorization request
-->aadyathe stage il client application oAuth service in reqeust ayakunu asking for permission to access specific user data

`GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=code&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1`

ingnae aayirikum normally request undavuka.This request contains the following noteworthy parameters, usually provided in the query string:

-   `client_id`
    
    Mandatory parameter containing the unique identifier of the client application. This value is generated when the client application registers with the OAuth service.
    
-   `redirect_uri`
    
    The URI to which the user's browser should be redirected when sending the authorization code to the client application. This is also known as the "callback URI" or "callback endpoint". Many OAuth attacks are based on exploiting flaws in the validation of this parameter.
    
-   `response_type`
    
    Determines which kind of response the client application is expecting and, therefore, which flow it wants to initiate. For the authorization code grant type, the value should be `code`.
    
-   `scope`
    
    Used to specify which subset of the user's data the client application wants to access. Note that these may be custom scopes set by the OAuth provider or standardized scopes defined by the OpenID Connect specification. We'll cover OpenID Connect in more detail later.
    
-   `state`
    
    Stores a unique, unguessable value that is tied to the current session on the client application. The OAuth service should return this exact value in the response, along with the authorization code. This parameter serves as a form of CSRF token for the client application by making sure that the request to its `/callback` endpoint is from the same person who initiated the OAuth flow.

#### 2. User login and consent :
-->When the authorization server receives the initial request, it will redirect the user to a login page, where they will be prompted to log in to their account with the OAuth provider. For example, this is often their social media account.
-->They will then be presented with a list of data that the client application wants to access. This is based on the scopes defined in the authorization request. The user can choose whether or not to consent to this access.
-->It is important to note that once the user has approved a given scope for a client application, this step will be completed automatically as long as the user still has a valid session with the OAuth service. In other words, the first time the user selects "Log in with social media", they will need to manually log in and give their consent, but if they revisit the client application later, they will often be able to log back in with a single click.

#### 3. Authorization code grant :
-->If the user consents to the requested access, their browser will be redirected to the `/callback` endpoint that was specified in the `redirect_uri` parameter of the authorization request. The resulting `GET` request will contain the authorization code as a query parameter. Depending on the configuration, it may also send the `state` parameter with the same value as in the authorization request.

                   GET /callback?code=a1b2c3d4e5f6g7h8&state=ae13d489bd00e3c24 HTTP/1.1 
                   Host: client-app.com`

#### 4. Access token request :
-->Once the client application receives the authorization code, it needs to exchange it for an access token. To do this, it sends a server-to-server `POST` request to the OAuth service's `/token` endpoint. All communication from this point on takes place in a secure back-channel and, therefore, cannot usually be observed or controlled by an attacker.

             POST /token HTTP/1.1 
             Host: oauth-authorization-server.com 
             …
            client_id=12345&client_secret=SECRET&redirect_uri=https://client- 
            app.com/callback&grant_type=authorization_code&code=a1b2c3d4e5f6g7h8`

In addition to the `client_id` and authorization `code`, you will notice the following new parameters:

-   `client_secret`
    
    The client application must authenticate itself by including the secret key that it was assigned when registering with the OAuth service.
    
-   `grant_type`
    
    Used to make sure the new endpoint knows which grant type the client application wants to use. In this case, this should be set to `authorization_code`.

#### 5. Access token grant :
-->The OAuth service will validate the access token request. If everything is as expected, the server responds by granting the client application an access token with the requested scope.

                             { 
                             "access_token": "z0y9x8w7v6u5", 
                             "token_type": "Bearer",
                              "expires_in": 3600, 
                              "scope": "openid profile", 
                              … 
                              }

#### 6. API call :
-->Now the client application has the access code, it can finally fetch the user's data from the resource server. To do this, it makes an API call to the OAuth service's `/userinfo` endpoint. The access token is submitted in the `Authorization: Bearer` header to prove that the client application has permission to access this data

                               GET /userinfo HTTP/1.1 
                               Host: oauth-resource-server.com
                                Authorization: Bearer z0y9x8w7v6u5

#### 7. Resource grant :

                               { 
                                       "username":"carlos", 
                                       "email":"carlos@carlos-montoya.net",
                                        … 
                                }

-->The client application can finally use this data for its intended purpose. In the case of OAuth authentication, it will typically be used as an ID to grant the user an authenticated session, effectively logging them in.

==============================================================

## Implicit grant type :
-->The implicit grant type is much simpler. Rather than first obtaining an authorization code and then exchanging it for an access token, the client application receives the access token immediately after the user gives their consent.
-->implicit is far less secure
-->When using the implicit grant type, all communication happens via browser redirects - there is no secure back-channel like in the authorization code flow. This means that the sensitive access token and the user's data are more exposed to potential attacks.

#### 1. Authorization request -> 2. User login and consent -> 3. Access token grant  
-->If the user gives their consent to the requested access, this is where things start to differ. The OAuth service will redirect the user's browser to the `redirect_uri` specified in the authorization request. However, instead of sending a query parameter containing an authorization code, it will send the access token and other token-specific data as a URL fragment.

           `GET /callback#access_token=z0y9x8w7v6u5&token_type=Bearer&expires_in=5000&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1`
-->As the access token is sent in a URL fragment, it is never sent directly to the client application. Instead, the client application must use a suitable script to extract the fragment and store it.

#### 4. API call :
-->Once the client application has successfully extracted the access token from the URL fragment, it can use it to make API calls to the OAuth service's `/userinfo` endpoint. Unlike in the authorization code flow, this also happens via the browser.

                              GET /userinfo HTTP/1.1 
                              Host: oauth-resource-server.com 
                              Authorization: Bearer z0y9x8w7v6u5

#### 5. Resource grant :
-->The resource server should verify that the token is valid and that it belongs to the current client application. If so, it will respond by sending the requested resource i.e. the user's data based on the scope associated with the access token

                          {
	                           "username":"carlos", 
	                           "email":"carlos@carlos-montoya.net"
	                      }
-->The client application can finally use this data for its intended purpose. In the case of OAuth authentication, it will typically be used as an ID to grant the user an authenticated session, effectively logging them in.

==============================================================

## OAuth authentication :
-->Although not originally intended for this purpose, OAuth has evolved into a means of authenticating users as well
-->For example, you're probably familiar with the option many websites provide to log in using your existing social media account rather than having to register with the website in question. Whenever you see this option, there's a good chance it is built on OAuth 2.0.
-->OAuth authentication is generally implemented as follows:
1.  The user chooses the option to log in with their social media account. The client application then uses the social media site's OAuth service to request access to some data that it can use to identify the user. This could be the email address that is registered with their account, for example.
2.  After receiving an access token, the client application requests this data from the resource server, typically from a dedicated `/userinfo` endpoint.
3.  Once it has received the data, the client application uses it in place of a username to log the user in. The access token that it received from the authorization server is often used instead of a traditional password.


            Lab-1(Authentication bypass via OAuth implicit flow)
-->ivide namul login chyanamengil namude social media account koduthitan login chyendath
-->namal oAuth login process complete chyth kazhinjal nere blog website ilek redirect chyapedum
-->so namude social mediayil ninum namude basic infromation client application kitum.so ee kitiya info vech namude application log in chyum (In a post request.thazhe nok) :

                {
	                "email":"wiener@hotdog.com",
	                "username":"wiener",
	                "token":"hi1xUqlRvgk-sZ9GnwcyTWMd-xhJhCk6KaabA4Bspti"
	            }
-->so ivide namude email,name,pine oru valid token um kanam.so ee post request eduth repeater il viduka
-->enit ee email:"wien..."  ennath mati namuuk venda user inte email kodukuka.ie ivide carlos inte email kodukuka.enit send chyuka.apol FOUND 302  enn kanam.so ee response copy chyth browser il nokiyal kanam namal carlos aayi mariyikunu.so lab solve aayi

==============================================================

### Recon :
-->Recognizing when an application is using OAuth authentication is relatively straightforward. If you see an option to log in using your account from a different website, this is a strong indication that OAuth is being used.
-->The most reliable way to identify OAuth authentication is to proxy your traffic through Burp and check the corresponding HTTP messages when you use this login option. Regardless of which OAuth grant type is being used, the first request of the flow will always be a request to the `/authorization` endpoint containing a number of query parameters that are used specifically for OAuth
-->In particular, keep an eye out for the `client_id`, `redirect_uri`, and `response_type` parameters. For example, an authorization request will usually look something like this:

        GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=token&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1

-->If an external OAuth service is used, you should be able to identify the specific provider from the hostname to which the authorization request is sent
-->As these services provide a public API, there is often detailed documentation available that should tell you all kinds of useful information, such as the exact names of the endpoints and which configuration options are being used.
-->Once you know the hostname of the authorization server, you should always try sending a `GET` request to the following standard endpoints:

-   `/.well-known/oauth-authorization-server`
-   `/.well-known/openid-configuration`

-->These will often return a JSON configuration file containing key information, such as details of additional features that may be supported. This will sometimes tip you off about a wider attack surface and supported features that may not be mentioned in the documentation.

==============================================================

## Exploiting OAuth authentication vulnerabilities :
-->Vulnerabilities can arise in the client application's implementation of OAuth as well as in the configuration of the OAuth service itself.We can look at both of these :
1) vulnerabilities in the client applicatoin :
              a.improper implementation of the implicit grand type
              b.flowed CSRF protection
2) vulnerabilities in the oAuth service :
              c.leaking authorization codes and access token
              d.flowed scope validation 
              e.unverified user registrarion

### Vulnerabilities in the OAuth client application :
-->Client applications will often use a reputable, battle-hardened OAuth service that is well protected against widely known exploits. However, their own side of the implementation may be less secure.

####  a. Improper implementation of the implicit grant type :
-->Due to the dangers introduced by sending access tokens via the browser, the implicit grant type is mainly recommended for single-page applications. However, it is also often used in classic client-server web applications because of its relative simplicity.
-->In this flow, the access token is sent from the OAuth service to the client application via the user's browser as a URL fragment. The client application then accesses the token using JavaScript. The trouble is, if the application wants to maintain the session after the user closes the page, it needs to store the current user data (normally a user ID and the access token) somewhere
-->In the implicit flow, this `POST` request is exposed to attackers via their browser
-->==ithinte Lab namal already chyth (lab-1). molil und nokiko==

==============================================================

### b. Flawed CSRF protection :
-->Although many components of the OAuth flows are optional, some of them are strongly recommended unless there's an important reason not to use them. One such example is the `state` parameter.
-->The `state` parameter should ideally contain an unguessable value, such as the hash of something tied to the user's session when it first initiates the OAuth flow
-->This value is then passed back and forth between the client application and the OAuth service as a form of CSRF token for the client application
-->Therefore, if you notice that the authorization request does not send a `state` parameter, this is extremely interesting from an attacker's perspective. It potentially means that they can initiate an OAuth flow themselves before tricking a user's browser into completing it, similar to a traditional CSRF attack
-->Consider a website that allows users to log in using either a classic, password-based mechanism or by linking their account to a social media profile using OAuth. In this case, if the application fails to use the `state` parameter, an attacker could potentially hijack a victim user's account on the client application by binding it to their own social media account.

            Lab-2(Forced OAuth profile linking)
-->ivide namuk website namude social media profile account umayi attach chyan ulla option tharunund.so namude normal usernme,password oke type chyth login chyunathin pakaram ee oAuth service vazhi login chyam
-->ivide client application oAuth nalla rethiyil implement chythitila.so oru attacker engane exploit chyunu enn nokam
-->so aadyam lab open chyuka.enit my account click chyuka avide thane kanam normal username,password koduth keram allengil namude social media account vech keram
-->aadyam namal normal reethiyil keram.apol my account page il nokiyal kanam `attach a social profile`  enn.so avide click chyth namude social media account kodukuka.ellam kazhinj namude website ilek thane namal pokum
-->ini burp history il poi ee oauth service study chyuka.apol namuk kanam :

         GET /auth?client_id=l35fh7bg4n033gqcoagfd&redirect_uri=https://0a24008b033b7e07c0491ba000380015.web-security-academy.net/oauth-linking&response_type=code&scope=openid%20profile%20email HTTP/1.1

-->so ivide nokiyal namuk kanam state parameter use chythitila.so athinartham namuk csrf attakc in scope und.mukalile Recon section il nokiyal kanam enthoke parameter aan satharan undavuka enn
-->ini history il thane ini study chyuka apol kanam :

          GET /oauth-linking?code=n7L8WSYsQ1bSw3996RrXdFJ7WdbYfeb7tpWsiAFS0Re HTTP/1.1
-->so ivide namude account umayi socila media account link chyuna samayath um state parameter illa
-->so ini enth venamenal onukoodi `attach a social media`  option select chyuka.enit mumbathe pole thane username,password oke koduth send chyuka.but ivide intercept on chythit venam chyan.enit avasanam ee GET /oauth-linking?code=... enna request ethumbol copy url select chyuka enit request drop chyuka.apol ithil namuk thanitula code parameter ile value used aakunila.so csrf attack chyumbol aa code used aayikolum
-->ini namuk oru iframe aaki ee url src il kodukam(exploit server il) :

               `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>`

-->ini matoru user in kodukam.ivide admin aan kodukunath.so ini admin ee malicious iframe il poikumbol ee url trigger aakum.so adminte puthiya social media profile aayi namude social media profile attach aakum.so ini namal application il poi social media account vazhi login chyuna samayath admin te account il aakum keruka karanam.admin avante social media account aayi namude account koduthirikunu
-->ivide enthan sambavikunath ennal.ivide kanam rand reethiyil login chyam.athupole namude account il keritum social media profile attach chyam.so ivide oauth chyumbol kanam url il state parameter illa.so prone to csrf attack.so namal namude account il keri social media account attach chyuna button click chyuka.enit ee social media account link chyuna url vere ethit aa request inte url copy chyunu.enit aa request drop chyuka.apol athil ulla code parameter used aakunila.so valid aayi nikum.ini ee copy chytha url oru iframe il aaki victim in athayath ivide admin kodukunu.admin click chyumbol ee iframe il ulla src yil ee oauth process inte url aanalo ullath.athayath social media link chyuna request apol namude admin annan ee iframe il kerumbol ee link work aakum.apol admin annante account ile social media account namude social media account aayi marum.athayath namude social media username,password oke koduth last step ayapol alle namal url copy chyth request drop chythath.so ini vere oru user ee malicious iframe il kerumbol ayalude account il namude social media account aan attach aakuka.so ini namal namude social media acount vech login chythal aa victim nte account ilekan namal keruka ok

==============================================================

### c. Leaking authorization codes and access tokens :
-->Perhaps the most infamous OAuth-based vulnerability is when the configuration of the OAuth service itself enables attackers to steal authorization codes or access tokens associated with other users' accounts
-->By stealing a valid code or token, the attacker may be able to access the victim's data
-->this can completely compromise their account - the attacker could potentially log in as the victim user on any client application that is registered with this OAuth service.
-->Depending on the grant type, either a code or token is sent via the victim's browser to the `/callback` endpoint specified in the `redirect_uri` parameter of the authorization request.If the OAuth service fails to validate this URI properly, an attacker may be able to construct a CSRF-like attack, tricking the victim's browser into initiating an OAuth flow that will send the code or token to an attacker-controlled `redirect_uri`.

           Lab-3(OAuth account hijacking via redirect_uri)
-->mattu user inte authorisation code steal chyunathan ivide nokunath.ivide namuk admin te authorisation code aan steal chyendath.enit admin aayi keri carlos ine delete chyanam
-->so aadyam lab open chyuka enit my account click chyumbol namal nere social media account login chyunathilekan pokunath.so wiener:peter enn koduth social media vazhi login chyuka
-->ini burp history il pokuka.enit most recent aaya authorisation request nokuka.This should start with `GET /auth?client_id=[...]` :

                GET /auth?client_id=pikuwtb69h477dpaq3isl&redirect_uri=https://0aff007d036f3207c03008d1005200ce.web-security-academy.net/oauth-callback&response_type=code&scope=openid%20profile%20email HTTP/1.1
                
-->Notice that when this request is sent, you are immediately redirected to the `redirect_uri` along with the authorization code in the query string. Send this authorization request to Burp Repeater.
-->repeater il poi nokumbol kanam ee redirect_uri il namuk enth value um idan patum.ee redirect_uri il namal namude exploit server inte link kodukuka enit follow redirect adichit exploit server ile access log click chyumbol kanam code= value  avide varunund.so athinartham eth redirection pathilekum code imediate aayi oauth kodukum
-->so ini namuk oru iframe undaki ee request (redirect kitiya request alla namal uri maati vekuna first request) inte url src aayi kodukuka enit namal matoru user in kodukumbol namude exploit server ilek aa user inte code=...  kitum
-->so ivide social media login complete aakuna samayath GET /oauth-callback?code=DC-xOQ94ljUf-YXcKPysHmY5cw9LaxZv2MgnztWcbdg enn request il namude code kanam.so ee reqeust eduth repeater il iduka enit ee code mati namuk kitiya adminte code itt send adikumbol namal admin aayi marum.so ini aa response copy chyth browser il poi iduka enit carlos inte account delete chyuka ok
-->`redirect_uri`  il namal pala reethiyil test chyth nokanam.application already chelapo avark venda redirection url white list chyth vechitundakum.so vere arbitrary url koduthal error varum.apol namal pala reethiyil test chyth nokanam.like extra karyam append chyth nokanam eg: `https://default-host.com &@foo.evil-user.net#@bar.evil-user.net/`. ssrf, cors iloke ee reethi namal padichitund.url mati localhost enn itt test chyth nokuka.It is important to note that you shouldn't limit your testing to just probing the `redirect_uri` parameter in isolation
-->However, you may be able to use directory traversal tricks to supply any arbitrary path on the domain. Something like this: `https://client-app.com/oauth/callback/../../example/path`   May be interpreted on the back-end as:    `https://client-app.com/example/path`

==============================================================

             Lab-4(Stealing OAuth access tokens via an open redirect)
-->ee lab korach tough aan enalum korach imp points nokam
-->lab il keri oauth login chyuka enit athile auth start chyuna request edukuka ivide :

            GET /auth?client_id=p18aamdh7zzc31b8xzfb3&redirect_uri=https://0a5b00df049eadf9c06f27d000d0003e.web-security-academy.net/oauth-callback&response_type=token&nonce=-236298252&scope=openid%20profile%20email HTTP/1.1

-->ivide ulla redirect_uri il nerthe pole enthengilum ittal aavila.like athile value remove chyth https://example.com  enn koduthal error varum.but ithile value il enthengilum extra cherkumbol kozhapam ila so  -> redirect_uri=https://0a5b00df049eadf9c06f27d000d0003e.web-security-academy.net/oauth-callbac/../post?postid=1   enn kodukumbol aavunund
-->note: ivide post ennath already ee website il ulla oru path aan.athil ulla real parameter thane aan ivide itath.ivide /../ enn ittath in karanam ee post enna path one page up il aan ullath athukondan 
-->ingane chymbol namal first blog post ilek redirect chyapedukayan
-->baki manasilayita pine epengilum nokam

==============================================================

# OpenID Connect :
-->OpenID Connect extends the OAuth protocol to provide a dedicated identity and authentication layer that sits on top of the basic OAuth implementation
-->It adds some simple functionality that enables better support for the authentication use case of OAuth.
-->OAuth was not initially designed with authentication in mind; it was intended to be a means of delegating authorizations for specific resources between applications.However, many websites began customizing OAuth for use as an authentication mechanism. To achieve this, they typically requested read access to some basic user data and, if they were granted this access, assumed that the user authenticated themselves on the side of the OAuth provider.
-->If OpenID connect is actively being used by the client application, this should be obvious from the authorization request. The most foolproof way to check is to look for the mandatory `openid` scope.
-->Even if the login process does not initially appear to be using OpenID Connect, it is still worth checking whether the OAuth service supports it
-->You can simply try adding the `openid` scope or changing the response type to `id_token` and observing whether this results in an error.
-->As with basic OAuth, it's also a good idea to take a look at the OAuth provider's documentation to see if there's any useful information about their OpenID Connect support. You may also be able to access the configuration file from the standard endpoint `/.well-known/openid-configuration`.
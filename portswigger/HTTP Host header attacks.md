### HTTP Host header attacks :
-->The HTTP Host header is a mandatory request header as of HTTP/1.1. It specifies the domain name that the client wants to access
-->For example, when a user visits `https://portswigger.net/web-security`, their browser will compose a request containing a Host header as follows:

							GET /web-security HTTP/1.1
							Host: portswigger.net
-->oru webserver il thane kore websites um applications um run chyunundakum apol eth back end component umayan client communicate chyendath enn decide chyan host header use chyunu
-->Attacks that involve injecting a payload directly into the Host header are often known as "Host header injection" attacks.

-   Web cache poisoning
-   Business logic flaws in specific functionality
-   Routing-based SSRF
-   Classic server-side vulnerabilities, such as SQL injection

ithupolula vulnerabilites host header attack vazhi chyan patum

-->On the other hand, as the Host header is such a fundamental part of how the websites work, tampering with it often means you will be unable to reach the target application at all. The front-end server or load balancer that received your request may simply not know where to forward it, resulting in an "`Invalid Host header`" error of some kind. This is especially likely if your target is accessed via a CDN. In this case, you should move on to trying some of the techniques outlined below.
## #1 Check for flawed validation :
--> arbitrary sathanam host il itt send chyumbol "`Invalid Host header`"  ennayirikum response varuka.ella response um ingane aayirikanamenila.so varuna response in anusarich forward chyanam
-->websiste engane aan host header parse chyunath enn study chyanam.host header il port check chyunundon nokanam.check chyunilengil avide namuk payload chyan patumon nokanam

                        GET /example HTTP/1.1 
                        Host: vulnerable-website.com:bad-stuff-here

## #2 Send ambiguous requests :
-->One possible approach is to try adding duplicate Host headers(apol rand host header varum).athikavum namude request block chyapedum.but ella devopers um ee scenario munkooti kananamenila so ingane ulla situation handle chyan ulla code chyanamenila.so ee technique use chyumbol chelapo intresting aaya response kanan patiyekum
-->rand host header kodukumbol aadyam original pine duplicate itt test chyuka athupole vise vesa ayum chyth nokanam ok

                      GET /example HTTP/1.1 
                      Host: vulnerable-website.com 
                      Host: bad-stuff-here

-->Let's say the front-end gives precedence to the first instance of the header, but the back-end prefers the final instance. Given this scenario, you could use the first header to ensure that your request is routed to the intended target and use the second header to pass your payload into the server-side code.
-->duplicate host header(duplicate allathathilum chyth nokiko) use chyumbol space itt test chyuka.Some servers will interpret the indented header as a wrapped line and, therefore, treat it as part of the preceding header's value

                    GET /example HTTP/1.1 
                         Host: bad-stuff-here      (ingane space itt test chyuka)
                    Host: vulnerable-website.com
-->backendum front endum ee technique il engane behave chyunu enn anusarich mumbot pokuka

## #3 Inject host override headers :
--> websites are often accessed via some kind of intermediary system, such as a **load balancer or a reverse proxy**. In this kind of architecture, the Host header that the back-end server receives may contain the **domain name** for one of these intermediary systems. This is usually not relevant for the requested functionality.
-->To solve this problem, the front-end may inject the `X-Forwarded-Host` header, containing the original value of the Host header from the client's initial request. For this reason, when an `X-Forwarded-Host` header is present, many frameworks will refer to this instead. You may observe this behavior even when there is no front-end that uses this header.
-->You can sometimes use `X-Forwarded-Host` to inject your malicious input while circumventing any validation on the Host header itself.

                   GET /example HTTP/1.1 
                   Host: vulnerable-website.com 
                   X-Forwarded-Host: bad-stuff-here
-->Although `X-Forwarded-Host` is the de facto standard for this behavior, you may come across other headers that serve a similar purpose, including:

-   `X-Host`
-   `X-Forwarded-Server`
-   `X-HTTP-Host-Override`
-   `Forwarded`

-->In Burp Suite, you can use the Param Miner extension's "Guess headers" function to automatically probe for supported headers using its extensive built-in wordlist.
-->From a security perspective, it is important to note that some websites, potentially even your own, support this kind of behavior unintentionally. This is usually because one or more of these headers is enabled by default in some ==third-party technology== that they use.

==============================================================

### Password reset poisoning :
-->It is a tachnique where an attacker manipulates a vulnerable website into generating a password reset link pointing to a domain under their control.This behavior can be leveraged to steal the secret tokens required to reset arbitrary users' passwords and, ultimately, compromise their accounts.
-->athayath attacker website il poi password reset chyunu.apol reset you password il poi click chyunu.aa request intercept chyth athile host header maati attacker control chyuna host kodukunu.ini aa request client in koduth avante token edukunatho angan entho aan
-->pala reethiyil password reset work aakum.oru reethi nokiyal .. : adyam user reset option select chyunu enit email , passwordum adikunu. athin shesham website aa info vech user exist chyunundon nokunu and then generates a temporary, unique, high-entropy token, which it associates with the user's account on the back-end.The website sends an email to the user that contains a link for resetting their password. The user's unique reset token is included as a query parameter in the corresponding URL: `https://normal-website.com/reset?token=0a1b2c3d4e5f6g7h8i9j`.When the user visits this URL, the website checks whether the provided token is valid and uses it to determine which account is being reset. If everything is as expected, the user is given the option to enter a new password. Finally, the token is destroyed.
-->Password reset poisoning is a method of stealing this token in order to change another user's password

         Lab-1(Basic password reset poisoning)
-->namal lab open chyuka.enit login chyenda.pakaram login page il poi forgot password adikuka
-->enit namude username kodukuka.ini exploit server ilek pokuka avide email-client enna button click chythal namuk kanan patum putiya password undakan ulla link website ayachirikunath
-->so athil click chythal kanam putiya password adikan ulla page ilek namal pokum
-->so namal nertthe forgot password? enna button click chyth aa pageilek onude pokuka enit aa request repeater ilek viduka.karanam ee page il ninanalo ellam thudangunath
-->ith oru POST /forgot-password   request aan.  ee request inte thazhe aayi csrf=,pine namal kodutha username= um und
-->ivide ini namal enth chyunu ennal host: josu.com enn kodukua enit send adikuka.apol namuk kanam website load aakunund(ie,200OK kanikunund)
-->ini exploit server ile email-client ilek poyal kanam puthiya password reset link vanitund.athinte link nokiyal kanam josu.com/forgot-passwod......token=dfjdslf...   ennan kidakunath
-->so athinartham namal control chyuna oru webserver ilek ee link pokum
-->so namuk namude exploit server inte link eduth host: header il idam (/exploit  ennath venda)
-->enit username=wiener ennath mati carlos aakuka.apol carlosinte email lek password reset link send aakum
-->ini exploit server il poi access-log click chyuka athil kanam namude link carlos click chythitund
-->athil carlos inte token und.so namal aa token edukuka enit real aayi password matuna site ilek pokuka.enit athil ulla token maati carlos inte token url il kodukuka.enit puthiya password adich koduthal carlos inte password maarum
-->==ivide website password change chyenda link namude email ilek ayakukayan.aa link il token und. ee token vechan namale validate chyunath.so namuk kitiya token maati vere oru user inte token aa url ile token parameter il koduthal ayal aayi marum nammal==

==============================================================

         Lab-2(Password reset poisoning via middleware)
-->lab open chyuka.enit nerthe pole forgot password adikuka.enit wiener enn kodukuka.enit exploit server->email-client edukuka
-->athil kanam namuk password change chyan ulla link vanitund.so namuk athil poi password matam
-->ini ee POST /forgot-password   enna request repeater il viduka enit right click chyth param miner use chyth guess header click chyuka.apol namuk kanam x-forwarded-host header use chyan patumen
-->so aa request il x-forwarded-host: josu   enn koduth send chyuka enit exploit serveril email-client il poi nokiyal namuk kanam josu/forgot-passw....   enna link pasword reset chyanayi website ayachitund
-->so namuk namude exploit server inte path ee header il kodukam athupole username= parameter il carlos kodukam apol ayalde email ilek pasword reset link varum
-->ini namuk exploit-server->access-log il poi nokiyal kanam carlos inte token.so ath copy chyuka
-->enit veendum email-client ilek pokuka enit real aayitulla password change chyuka link copy chyth puthiyal tabil paste chyuka.enit athile token maati(ath namude token aan) namuk access log il ninum kitiya carlos inte token paste chyth send adikuka apol password matuna page varum. athil puthiya password(josu) koduth save chyuka
-->ini carlos:josu koduth keruka apol lab solve aayi
-->==nerthe lab il namal direct host header il thane chythu but ivide host header il chyumbol 403 Forbdden aan varunath.so namal param miner use chyth headers check chyunu.so x-forwarded-host kandthunu(enik kandethan patiyitila).ee header vazhi namuk host: header il chyan patuna attack chyan patum.so ivide ee header use chyumbol namal kodukuna url ilek link pokunund==

==============================================================

            Lab-3(Web cache poisoning via ambiguous requests)
-->lab open chyuka.enit home page inte GET / request repeater il iduka
-->aadyam host header edit chyth send chyuka apol namuk 504 error varum
-->so athinartham host header il onum chyan patila
-->so namuk aadyam oru cache buster idam ie,/cb=123
-->ini namuk oru duplicate host: josu idam
-->enit send adikuka aadyam miss kanikum pinem send chythal hit kaanikum.so athinartham ee response cache il ninan varunath.
-->ini ithinte response il nokiyal matoru karyam koodi kanam.josu enna string reflect chyunund. ath oru url ilan reflect chyunath.aa url oru js file edukukayan: `//josu/resources/js/tracking.js`
-->ini ee duplicate host header remove chyuka enit send adikumbol kanam hit il ninan response varunath.athpole josu ipozhum response il reflect chyunund
-->so ini namal exploit server il poi file path `/resources/js/tracking.js`   enn kodukuka.enit body il alert(document.cookie)  enn kodukuka.enit store chyuka.
-->exploit serverinte url copy chyth repeater il vann duplicate host header il paste chyuka enit cachebuster remove chyuka enit send adikuka hit kitiyathin sesham browser il vann refresh chythal alert pop aakum labum solve aakum

==============================================================

           Lab-4(Host header authentication bypass)
-->ivide namal lab open chythu enit athinte GET / request edukuka
-->athile host: header il vere enthengilum koduth send chyumbozhum page kitunund.so host header vulnerable aan
-->ini namal home page il poi /robots.txt adikumbol kanam /admin disallow chythathayi kanam
-->so homepage il /admin itt search chyuka apol `Admin interface only available to local users`  enn kanam
-->namak ariyam host header vulnerable aan.ivide local users in admin account access chyan patum
-->so host: localhost enn koduth GET /admin ennum koduth repeater il send chyumbol 200OK kanikunund
-->ini aa response copy chyth browser il itt search chyuka apol namuk kanam admin panel namuk kitiyirikunu
-->so carlos inte account delete click chyth intercept chyth host: locahost koduth forward adikuka apol lab solve aakum

==============================================================

### Routing-based SSRF
-->Classic SSRF vulnerabilities are usually based on XXE or exploitable business logic that sends HTTP requests to a URL derived from user-controllable input. Routing-based SSRF, on the other hand, relies on exploiting the intermediary components that are prevalent in many cloud-based architectures. This includes in-house load balancers and reverse proxies.

-->Although these components are deployed for different purposes, fundamentally, they receive requests and forward them to the appropriate back-end. If they are insecurely configured to forward requests based on an unvalidated Host header, they can be manipulated into misrouting requests to an arbitrary system of the attacker's choice.
-->ingane ulla load balancers in full allengilum korachengilum internal network il access/controll undakum.so websiteintem backendinum idak ith nilkum enit website il ninum request edukum.aa request il ulla host header il ssrf attack chyathnokam.these technique potentially transform a simple load balancer into a gateway to the entire internal network.
-->You can use Burp Collaborator to help identify these vulnerabilities.If you supply the domain of your Collaborator server in the Host header, and subsequently receive a DNS lookup from the target server or another in-path system, this indicates that you may be able to route requests to arbitrary domains.
-->ithoke chyan patiyal ini adutha step ennath namuk internal networks ine control chyan patumon enn nokanam.athin internalil use chyuna private ip address ariyanam.standard private ip aaya `192.168.0.0/16`  bruteforce chyth range oke kandupidich internal ip undon check chyam

                      Lab-5(Routing-based SSRF)
-->ivide namal lab open aaki enit home page inte GET / request repeater il idunu
-->host header il burpcollaborator client url kodukunu enit send adikunu
-->ini collaborator il vann poll now adikumbol namuk kanam avide request vanirikunu.so athinartham we are able to make the website's middleware(loadbalancer) issue requests to an arbitrary server.athyath website intem backendindem edak nikuna loadbalancerin chela thakrar und.athukondan thoniya serverilek request ayachath
-->so collaboratorinte aavishyam kazhinju.ini ee request intruder ilek viduka
-->enit ellam clear chythit `Host: 192.168.0.§0§`   enn kodukuka.enit payload numbers select chyuka. 0:255:1  ennkoduth start attack kodukuka.apol namuk kanam baaki elam 504 varum onn mathram 302 enna kanikum.
-->==NOTE: intruder il host il matam namal varuthunathkond thane puthiya version il ath namal explicitly tick chyanam.ie,update host header to match target ennath untick chythit venam start attack chyan ok.illengil aavilla==
-->aa request inte response nokumbol kanam namuk /adminte kanikunath.so ini ee request repeater ilek viduka
-->GET /amin enn kodukuka.host aa ip thane kodukuka.send adikuka.response il nokiyal kanam namuk admin panel access kity
-->ithinte response browser il nokumbol kanam namuk oru user ine delete chyenamengil oru form und athil input koduthale delete chyan patu
-->repeater il ulla response il nokiyal kanam delete chyenda path ie,/admin/delete ennan so namal request il angane iduka
-->ini aa form study chythal kanam athil csrf token und athupole username parameter um und. namal undkuna exploitil ath illengil deletion nadakila
--->ith mathramalla, nammal admin aayitan action chyunath so adminte session cookie koodi venam.so response il session und.so ath copy chyth namude cookil iduka.so final payload ingane undakum : `GET /admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos`   
-->njan chythapol set-cookie header response il kanichila.so njan ente request il ulla session cookie remove chythit onude send adichapol set-cookie kity.so ath copy chyth request il session cooke ittu.enit send chythpol ellam aayi.lab solve aayi

==============================================================

		        Lab-6(SSRF via flawed request parsing)
-->ivide lab open chyuka enit home page inte GET / request eduth repeater il viduka
-->host header il extra enthengilum add chyuka apol kanam 403 FORBIDDEN enn kanam
-->ini namal GET /   eena request  ile  /   maati  browser il poi aa home page inte url angane thane copy chyth ivide paste chyuka ie, `GET https://0aae00fa047fc721c1fd054b0069005c.web-security-academy.net/ HTTP/1.1`
-->ingane koduthit ini host header il enthengilum extra add chyumbol namuk block kitunila pakaram  504 Gateway Timeout  ennan kanunath
-->athesamayam host header il request il undaya aa same value(original) thane itt send adikumbol response ok thane aan kitunath.so ivide namal GET il home pageinte url koduth send chyumbol application ee absolute url aan validate chyunath.host header ile value nokunila
-->so ini burpcollaborator use chyam.host: burp url kodukuka enit send chyuka enit poll now click chyumbol kanam namude burp server ilek ee request pokunund.so athinartham middleware(loadbalancer) arbitrary serverilek request ayakunund
-->so ee absolute url adangiya request intruder ilek viduka.host: `192.168.0.$0$`  kodukuka enit untick chyuka update host header. enit payload numbers and 0:255:1 koduth start attack
-->ithl namuk oru ip adress inte status 302 enn kanam so aa request inte response nokiyal akanam location /admin enn.so aa request repeater il viduka
-->ini repeater il poi `GET https://0aae00fa047fc721c1fd054b0069005c.web-security-academy.net/admin`   admin add chyuka enit send adkumbol namal admin panel ilek keriyath kanam
-->ivide delete chyuna option und.mumbathe lab pole thane.oru form und athil username koduth delete chyanam
-->aadyam absolute url il ..../admin/delete enn kodukuka enit athil response il ninum kitiya csrftoken add chyuka.pine username um kodukuka.final payload :
    `GET https://your-lab-id.web-security-academy.net/admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos`
-->ini respones il ninum kitiya set-cookie yil ulla value eduth namjude cookie yil sesssion=aa value  kodukuka enit send chyumbol carlos enna account delete aakum.so lab solve aayi

==============================================================

### Proxy vs Reverse proxy(ytube: it k funde) :
### proxy: 
1) proxy works for the security of the client ie,

                                     CLIENT -> PROXY  --------> INTERNET 
2) ivide clent um proxy ennath oru closed environment ilan (ie, namla namude veetile networkil aan).so namuk ee closed networkil ninum enthengilum karyam purathulla network(internet) ilninum namuk edukanamengil.aadyam aa request ee proxy vazhi aan travel chyuka enit ath internet ilek poi namuk venda respones esuth thirich ee proxy vazhi namuk aa respones tharunu
3) proxy act as a sheild between us and the internet.oru firewall pole act chyum proxy.so intenet il ninum varuna bad aaya karyangal filter chyum
4) oru internal networkile ella clients um oru otta proxy vazhi aan request ayakuka
5) caching chyan proxy help chyum. vendapetta pages oke cache chyapedunath proxy il aanen thonunu
6) namal oru request proxy vazhi ayakumbol namude request il ullathoke encrypt chyan proxy sahyikum

### Revese proxy :
1) proxy client side il ninum work chyapedunu, reverse proxy server side il ninum work chyapedunu
2) instead of protecting the client , it rather protects the server

                                      INTERNET--------->  RE.PROXY--->server

3) athayath oru client ayakuna request server ilekanalo pokunat.so aa varuna requet serverumayi communicate chyan use chyunu ee reverse proxy
4) so oru website ilekan request varunathengil re.proxy angotek request ayakum. enit respone ayakum
5) reverse proxy ennath satharan proxy pole thanne aan.pakshe re.proxy il additional benefits und. ie, loadbalancing.loadbalancing ennath re.proxy ide oru main feature aan
6)  kore clients amazon.com request chyumbol kore request serverilek varum.apol aa load balance chyunathan load balancer aan
7) ==caching: satharana proxy pole re.proxy ilum caching nadakunund. ee caching aan nammal webcache poisoning attackil padichath==.so oru particular pagein aayi multiple request nadakumbol server aa page ee rev.proxy il cache chyth vekum.apol clients in easy aayi page kitum
8) adutha featurum proxy ullath thanne ie, seucrity. so request ayakuna client in ariyan patila ethe server , ip  il ninan respones varunath.so ellam hidden aaki vekum
9) rev.proxy ide matoru feature aan compression.athayath varuna requst ile packets compress chyth oru .zip file aak namuk response oke tharum
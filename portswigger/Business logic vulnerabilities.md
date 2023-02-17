-->Business logic vulnerabilities are flaws in the design and implementation of an application that allow an attacker to elicit unintended behavior
-->these can be also called "application logic vulnerabilities" or simply "logic flaws". 
-->Logic flaws are often invisible to people who aren't explicitly looking for them as they typically won't be exposed by normal use of the application. However, an attacker may be able to exploit behavioral quirks by interacting with the application in ways that developers never intended. 
-->automated vulnerability scanners vech ee vulnerabilitied kandupidikan patila.so manual test chyth thaen kandupidikanam

           Lab-1(Excessive trust in client-side controls)
-->ivide nammude kayil 100$ ullo pakshe namuk 1000 thinte jacker vangikanam
-->so nammal 1 quantity select chyth add to cart adikuka.ee request repeater il iduka
-->enit cart ilek browser vazhi pokuka.avide kanam product price 1000$ enn
-->nammal burp repeater il nokiyal kanam productid= parameter inte koode price=1000 parameter kanam so namal aa 1000 enath 10 aakuka 
-->ini browser il ulla cart page refresh adikuka apol price 10$ ena kanam.ini place you order click chyuka

           Lab-2(2Fa broken logic)
-->ith namal authentication il padichitund
-->lab-8 nokiyal mathi

            Lab-3(High-level logic vulnerability)
-->ivide negative value koduthit quantity de size kuraykunu athu vazhi price um kurayum.so rand product undenkil onile quantity size negative aaki kurachal mate productinte prize um athinanusarich kurayum
-->aadyam oru cheriya product cart ilek add akuka.enit athinte request il nokiyal kanam quantity enna parameter ulath
-->aa parametril nammal kodukuna integer value anusarich cart ile quantity marunund
-->so nammal - negative symbol itt number kodukumbozhum quantity um kurayunund athupole total prize um athinanusarich kuranj negative aakunund 
-->==REMEMBER- total prize aanu kurayunath allathe aa quantity de prize mathram alla== apol ini nammal vere product cartilek ittal ipol ula total prize negative alle.so aa number kurachite total prize kanikua
-->so nammal valiya number aan negative aaki itathengil mate productinte paisa nanne kurayum athre ullu
-->website ilum minus chyanula option und but negative akan patila.athukondan proxy vazhi chyunath
-->so nammal ini leather jacket product cart il aad chyuka.enit cart ilek pokuka.ipol rand product und
-->ini nerthe burp repeater il chythapole matte productinte (leather alla) price ini negative akikondirikuka apol leather jacket inte price um kurayum

               Lab-4(Low-level logic flaw)
-->ividem quantity= parameter il aan chyunath 
-->1 digit,2digit itt test chyumbol ok kanikukunund but 3 digit(111) number kodukumbol error kanikunund.so 99 quantity mathrame oru product namuk order chyan patu
-->==ivide nammal integer overflow ne patti arinjirikanam==
-->the maximum possible value a binary interger can hold **(2^n)-1** n=number of bits.ivide lab il 32 bits number aan use chyumath so 2^32-1 ie,4,294,967,296.ithil thane signed(contains + and - numbers) unsigned(conains + only numbers)numbers und.unsigned=2,147,483,647 ithu vere aan.so ithil kooduthal number vannal programming language in handle chyan patila.ithinte koode +1 ital ath negative number aayi marum ie,    -2,147,483,6478   athan namuk vendath
-->baki integer overflow theory il poi nokiko
-->ivide namal ee overflow undo enn adyam check chyunu
-->athin mumb ethra digit number kolum en nokanam.ivide 2 digits aan maximum.athayath 99 maximum order chyavuna quantity
-->so first namak medikanulla leather jacker add to cart adikunu.enit aa request repeater,intruder il kond poi idunu enit ellam clear chyunu.ivide $add$ kodukunila.pakaram quantity=99 parameteril 99 enn kodukuka karanam athan maximum order chyan patuna quantity 
-->ini payload type null kodukuka and continue indefinetly kodukuka apol limit ilathe attack nadakum.ini start attack kodukuka
-->==ivdede ee null payload koduthit attack kodkukumbol entha sambavikunathenal ee same request 99 units vech unlimited aayi requst nadathum ath vazhi integer overflow undon check chyum undengi -negative number aakum angade namuk positive number vech total prize cheriya amount aakam==
-->attack nadakuna samayath cart page refresh chythondirikuka.apol total prize marunath kanam.athil -negative value varunundo en nokanam.ivide varunund so interger overflow nadakunund.so aa cartil ulla item remove chyuka.enit ini mattoru product cartil add chyuka.ini nerthepole onnudi same attack nadathuka.apozhum ithupole valiyoru negative total prize kitum
-->ini namuk ee valiya negative value kurach total prize 100 in thazhem athupole negative allathem akiyamathi.ath namuk bidhi vech chyavunathe ullo
-->karanam ee intruderil vita same request namal repeater ilum vitirunalo.so avide vech quantity oru 50 kodukuka apol total prize inte negative value kuranj varum
-->karanam negative inte koode positive value koodumbol negative kurayumallo
-->karanam repeater il 50 kodukuka enn paranjal leather inte quantity 50 increase chyuka enanalo apol athrem quantity de paisa koodum angane namuk total prize 100 thaze aaki lab solve chyam
-->The person who coded the website should have used an unsigned integer for the basket amount which meant that it could never be negative.

               Lab-5(Inconsistent handling of exceptional input)
-->ivide nammal aadyam site edukuka.enit ath burp ile target il kanam.right click chyth engagement tools->content discover edukuka apol aa site ile karyangal kanikum
-->athil /admin enna path kanam.so athilek pokuka.athl kanam -  Admin interface only available if logged in as a DontWannaCry user.athayath namal @dontwanacry email il account undegile admin aayi keran patu
-->ivide registeri il notice chythal kanam ==If you work for DontWannaCry, please use your @dontwannacry.com email address== .athayath dontwannacry employees avaruke company email address vechan kerendath.apol namuk manasilakam somthing@dontwannacry.com ennakam avarude email id
-->aadyam namal oru normal account aaki register chyuka
-->email il valiya oru string koduthit namude email id kodukanam ie,  
              email : long-strind@exploit-acb01fcb1eacf7c8c0ed54c50144001c.web-security-academy.net                 (minimum oru 200 stirngs long)
-->ini password koduth register chythal email ilek authenticate chyanula link vanitundakum ath clink chyuka
-->ini my account select chyth namal undakiya accountil keri nokuka.avide kanam namude email.email truncated aayitan undavum.aa email namude editor appil copy chyth paste chythal kanam 255 characters mathrame email aayit angeekariku baki truncate chyum
-->so namal ee 255 number ormich vekuka.enit namuk puthiya account akanam with namude normail email alla pakaram @dontwanacry email itt koduthit enale namud admin privilege kitu
             email : long-string@dontwannacry.com.exploit-acb01fcb1eacf7c8c0ed54c50144001c.web-security-academy.net   
-->NOTE: ivide dontwannacry.com enn verunath vere mathram namuk website il kitiyamathi baki truncate chyapedanam.namal nerethe kandu 255 vere valid ullu baki truncate chyapedumen.so ivide last .com vere vanamathi athin shehsam namal kodukukna email id truncate ayikolum 
-->ingane kodukumbol namuk register um chyam aa link namude email id lek varunakayum chyum
-->ini myaccount eduth login chyuka apol namuk admin panel enn kanum page il apo manasilakiko namak admin privilege kity en so ini carlos inte account delete chyuka

           Lab-6(Inconsistent security controls)
-->ivide enthan chyunathenal nerthe pole thane athayath namuk admin privilege aakanam
-->/admin pathilek poyal kanam @dontwannacry users in mathrame admin privilege ulu
-->so namal adyam oru normal account undakuka with normal email id
-->enit account inte ullul kerit email update/change chyth @dontwannacry.com enn akiyal namuk admin privilege kitum
-->ivide registeri il notice chythal kanam ==If you work for DontWannaCry, please use your @dontwannacry.com email address== .athayath dontwannacry employees avaruke company email address vechan kerendath.apol namuk manasilakam somthing@dontwannacry.com ennakam avarude email id

         Lab-7(Weak isolation on dual-use endpoint)
-->ivide password reset il aan chyunath
-->username,current passwd,new passwd,confirm passwd parameter und
-->ith nammal intercept chyuka enit current passwrod angane thane remove chyuka value um athupole parameterum
-->ini forward adichal kanam current password app validate chyunila.username mathrame nokunulu
-->so eni onukoodi ee request intercept on aakit intercept chyuka
-->enit current password remove chyuka and username administrator en kodukuka
-->passwd enthengilum koduthit forward adichal ok aayi
-->ini namak administrator ayit login chyam

             Lab-8(Insufficient workflow validation)
-->ivide namude kayil 100$ ullu.so adyam cheriya paisede enthengilum order chyuka
-->ini burp history il poi nokuka.avide namal order place chythathinushesham namuk /cart/order-confirmation?order-confirmed=true enna oru confirmation request history il kanan sadhikum.so ath repeater il iduka
-->ini valiya paisede leather jacket cart ilek add chyuka enit repeater il poi aa same request edit onum chyuathe send kodukuka
-->apol browser il nokiyal kanam order place aayi en

           Lab-9(Authentication bypass via flawed state machine)
-->ivide namal namude details adich login chytha kerumbol namude role select chyan chodikunund.so user select chyuka.ini logout adikuka
-->burp il intercept on akuka
-->ini onukoodi correct details adich login chyuka.aa requst intercept chyanam
-->ini aa request forward adikuka athinushesham varuna request sradhikuka athiil kanam get /role-selector enn aa request drop chyuka so aa requst backendilek pokila 
-->ipol browser il error varum karanam aa request pokathathkond
-->but nammal browser il web app inte home page lek manually poi nokiyal kanam namal admin aayitan ulath
-->so ini admin panel il poi carlos ine delete chyuka.ayi

           Lab-10(Flawed enforcement of business rules)
-->ivide namuk offers tharunund app.athayath new customers in coupen code um athpole avarude newsletter sighup chythal athinum coupen tharunund.athuvazhi namuk product cheriya paisak vangikam
-->so leather jacket cart ilek add kodukuka enit coupen code randum kodukuka
-->ivide note chythal manasilakum same coupen adupich koduthal error varum but rand coupen mari mari koduthal error varila
-->so angane alternative aayi koduth total prize full ayit korakan nokuka
-->angane 0 total prize aakum apol order place chythal lab finish aayi

         Lab-11(infinite money logic flow)
-->ivide namal login chyth keruka enit thazhe newsletteri signin chythal namuk oru coupen code kitum
-->ini athil gift card enna oru product und ath cartilek add kodukuka
-->enit cart il vech coupen koduth apply kodukubol 30% discount kitum ini aa product place chyumbol namuk gift card um kitum aa gift card copy chyth namude my account in vanit gift card paste chyth apply koduthal namuk 3$ extra credit aakum
-->so ee process nammal automate chyukayan ivide chyunath
-->apol namuk unlimited aayi money kitikondirikum
-->so ithrem chyuka ie,news letter sign in chyth coupen edukuka,gift card product cart til add akuka,copen adich 30 discount edukuka,place order adikuka,enit athil ninum kituna gift card redeem chyth 3$ extra accountil aakuka.ini ith automate chyan athe process ilude thanne chyanam
-->so burp histrory il poi ee process nokuka - ==adyam nammal cart ilek pokuk,second coupen apply chyunu,third product place chyunu,fourth avidunu kitiya coupen gift card redeem chyuna sthalth poit redeem chyunu== apol aan namuk paisa add akunath
-->so nammal project options->sessions->session handlings->add click chyth->scope->include all urls pine details tabil add click chyth ee requests select chyuka
                                -->POST /cart
								-->POST /cart/coupon
								-->POST /cart/checkout
								-->GET /cart/order-confirmation?order-confirmed=true
								-->POST /gift-card
-->ini direct test macros adichal kanam post /gift-card 400 error varum.namuk ivide vendath ennthenal puthiya gift card undaknam
-->so nalamathe request select chyuka enit enit configure item select enit **derived from previous response** kodukanam enit custom parameter location nte avide add click chyth parameter name : gift-card enn kodukanam (karanam anjamathe respons il nokiyal kanam gift card couper kitunath gift-card enna parameter ilude aan).enit thazhe response il aa gift coupen select chythit ok kodukanam
-->ini anjamathe  /gift-card request select chyuka enit again counfigure item select chyuka enit athil kanam use from privios response enn so ath mati nerthe pole derived from previous response enn kodukuka apol correct 4 mathe request kazhinj ith run chyum karanam ee naalum anjum namal select chythathale athinte idayil historyil nokiyala kanam vere kore request ulath ath ozhivakanna ok
-->ini test macros adikuka apol kanam puthiya gift coupen generate aayi vanath
-->ini nere prxy->history il poi GET /my-account edukuka enit ath intruder il send chyuka enit ellam clear chyuka
-->payload type null kodukuka enit payload generate 412 kodukuka enit resoruce pool il poi 1 concurrent kodukuka enit start attack koduthal aakum

### Providing an encryption oracle:
-->Dangerous scenarios can occur when user-controllable input is encrypted and the resulting ciphertext is then made available to the user in some way
-->==Cipher is an algorithm which is applied to plain text to get ciphertext. It is the unreadable output of an encryption algorithm==
-->This kind of input is sometimes known as an "encryption oracle"
-->An attacker can use this input to encrypt arbitrary data using the correct algorithm and asymmetric key. 
-->This becomes dangerous when there are other user-controllable inputs in the application that expect data encrypted with the same algorithm. In this case, an attacker could potentially use the encryption oracle to generate valid, encrypted input and then pass it into other sensitive functions. 
-->athayath user kodukuna input encrypt chyuna reethi und angane encrypt chyuna ciphertext user in ethengilum reethiyil available aayal danger aan.apol attacker in ee ciphertext ine pati padikam athnte algorithm oke padichal pine aa app in vere pala inputs indavum athilum same encrypted aaya reethiyil thane aakum input expect chyunath apol namuk aa input ilum data encrypt chyth vitit exploit chyan nokam
-->eg: notification enna cookie il ninum reflect chyuna message plaintext aayit site il kanichal athinartham notification cookie il enth undo athoke decrypt chyum apol namuk mattu cookie il ulla content ee notification enna cookiel itt decrypt chyth test chyam
        
		Lab-12(Authentication bypass via encryption oracle)
-->ivide namuk admin privilege aaki carlos delete chyanam
-->aadyam nammal namude details adich keranam.stay logged in ennath check chyanam.apol requst il nokiyal kanam stay-logged-in enna cookie activate aayitund, ath encrypted form il aan ullath (ciphertext enum parayumayirikum)
-->sie il comment chyanul option und.so nammal comment chyumbol email: il invalid input kodukuka ie,josu.josu.com apol error kanikum.site il thane mukalilayi -  **Invalid email address: josu.josu.com** ena kanikum.oru sradhikuka athayath ee error kanikunathin mumb response il nokiyal kanam oru puthiya cookie-ie,notification cookie set chythath kanam.ee cookie um encrypted form il aan ullath.ee cookie set aayathin sheshamanu namuk site il error response tharunath
-->ini notice chyuka athayath ee cookie il nunum vanna message aan site il invalid email enna error ayi vannath.so athinartham aa cookie il ulla content decrypt chythitan namude site il vanath.apol ee same notification cookie mattu cookie inte values itt test chyth nokam apol athum decrypt aakunundo en ariyam
-->so ee rand request - athayath namal site il comment chyan use chytha aa POST /post/comment request um pine namuk error response aayi vanna GET /post request (ithil aan notification cookie ullath) um burp repeater il add chyuka
-->ini set-logged-in enn cookie il ulla content notification cookiel itt test chyuk apol kanam   - wiener:1655297085089   (ith timestamp ann).so nammale login aaki vekunath namude password um athinte koode timestamp vechitan
-->ini aa timestamp copy chyuka.enit email=administrator:1655297085089 enn koduthit send adikuka apol kanam aparth responsil puthiya notification cookie set aayath so aa cookie value copy chyth namude mate repeater request il poi paste chyth decrypt chyuka apol kanam ella response ilum - Invalid email address:  enna 23 character message kanikunund so 
-->so ee notification cookie burp decoder ilek viduka enit 1)decode->url 2)decode->base64 ->enit hex select chythit 23 hex characters delete chyuka enit reverse encode it 1)encode->base64 2)encode->url 
-->ini aa url code select chythit notification il value aayi kodukuka enit send adikuka apol result il kanam 500 internal server error -->**Input length must be multiple of 16 when decrypting with padded cipher** .so athinartham namal 32 charecters ittale decrypt aaku so nammal email parameter il 9 charecters koodi idanam (23+9=32)
        ie, email=xxxxxxxxxadministrator:1655297085089   enu kodukanam
-->ini ith nerthe pole send chyth notification value kiti decoder ilek viduka enit hex 32 charecters delete chyuka enit encode chythit again notification value ee puthiya url code itt send chythal ok en kanikum  -ithil kanikum - administrator:1655298465528 enn
-->so namal ipol hex 32 delete chytha aa notification cookie value copy chyth vekuka.karanam ini ee value aan namal stay-logged-in cookie il kodukukanath.karanam nammal login chyumbol
                 stay-logged-in= wiener: 1655297085089    ennan undavuk.apol admin login
chymbol   stay-logged-in=administrator:1655297085089    enu varum
-->so burp il poi intercept on aakuka enit browser il mukalil rand option undakum - home , my account enn . so home click chyuka apol aa request burp il kitum.
-->so first session enna cookie angane thane remove chyuka eni athine koode ulla
stay-logged-in cookie il namal copy chytha cookie(ciphertext) paste chythit forward adichal kanam admin panal ena puthiya oru tab browser il.so athil keri carlos ine delete chyuka
-->ini athava admin panel click chythit aakunilengil aa admin panel click chytnathum intercetp chyuka enit again session full remove chyuka enit set-logged-in= paster chyth forward adikuka aayi
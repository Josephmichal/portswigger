### Server-side template injection :
-->templates are file/codes that contains a bunch of static contents and within that content we have spots where dynamic content is supposed to go
-->so nammal use chyuna template enginte syntax il namuk dynamic content ee spots il add chyam eg: ruby il erb syntax , python il jinja syntax
-->Server-side template injection attacks can occur when user input is concatenated directly into a template, rather than passed in as data
-->try fuzzing the template by injecting a sequence of special characters commonly used in template expressions, such as `${{<%[%'"}}%\`
-->nammal type chyumbol `7*7` enn kodukuka apol 49 enn kandal server side il mathematical operation nadanu ennan
-->Simply submitting invalid syntax is often enough because the resulting error message will tell you exactly what the template engine is, and sometimes even which version
-->PAYLOAD ->https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

==============================================================

       lab-1(Basic server-side template injection)
-->ivide namal lab il keruka enit athil ulla oru product click chyuka apol namuk aa same page il thane unfortunatly the product is not avalilabe enn kanikum
-->so namal aa reqeust eduth repeater il itt test chyuka.athayath -> GET /?message=unfo.... enna request 
-->ini namuk rand reethiyil test chyam onn manual aayi repeater il message parameter il oro payload test chyam allengil ee request intruder il iduka enit message=`$$` koduth googlil ninum SSTI payload search chyth kodukam
-->options tabe il poi grep-match eduth ee unforunately... ennna message varuna aa message mathram select chyuka.apol ini start attack chyumbol ee div il ulla karyangal prathyekam result il kanikum status oke kanikunath pole apol namuk peten matangal manasilkan patum
-->resutl il <%= 3 * 3 %> enn payload 9 enn kanikunu athinartham SSTI vulnerability und
-->so ini ee payload il <%=+system("ls")+%> adikuka apol athinte result kitum
-->lab il paryaunath carlos inte home directiory il poi morale.txt delete chyan aan so message= il  <%=+system("rm+/home/carlos/morale.txt")+%> enn koduth send chyumbol delete aakum athupole lab solve um aakum

==============================================================

       Lab-2(Basic server-side template injection (code context))
-->ivide namal lab il keri login chyuka.enit athil namude preferred name select chyan ulla option und ath select chyuka enit athinte request nokuka ->`POST /my-account/change-blog-post-author-display`   ee request il `blog-post-author-display=user.first_name` ennan parameter value ullath.
-->ivide user. enn object ile attribute value aan first_name ennath. namal first name aan namuk vendath enn koduthath kondan ee attribute vannath
-->ini ee same site il blog post il comment iduna option und.athil namal comment mathram ittal mati.so namuk SSTI payload athil itt test chyam pakshe oru payload ilum 49 enna result kitunila
-->payloads : 
		           
		                                {{7*7}}
										${7*7}
										<%= 7*7 %>
										${{7*7}}
										#{7*7} 

-->but nerethe namal namude preferred name koduthirunalo so namude comment mukalil aa name aan kanikunath
-->so namal enth chyanamennal nerethe preferred name select chytha aa request il poi user.first_name ennath mati user.firstsjdlfj enthengilum kodukuka enit send adikuka
-->enit namal comment chytha aa blog ilek pokumbol kanam error sambavichirikunu
-->karanam template in correct aayi username inte value render chyan patunil so error kanikunu
-->`... in <module> File "/usr/local/lib/python2.7/dist-packages/tornado/template.py", line 348, in g.....`     ithil ninum manasilakam tornado enna template engine aan use chyunathen
-->so ini tornado engine pati padikuka.athil engane aan RCE chyan patuka enn nokuka
-->we discover that template expressions are surrounded with double curly braces, such as `{{someExpression}`
-->burp repeater il aadyam namal adyathe expression il ninum purath veranam enitan namuk venda payload kodukendath so `blog-post-author-display=user.name}}{{7*7}}`  enn payload kodukuk.
-->ivide aadyam rand }} koduth aa expression il ninum purath vannu enit nammude payload idunu
-->ipol namude preffered name ithan .so ini namal puthiya oru blog post il poi enthengilum comment chyuka.apol namude comment in mukalil aayi namude perum kanikum athupole athinte koode save aaya payload um kanikum ie,`Peter Wiener49}}` so 
-->ini namuk ithil RCE chyan patumon nokam.so athinayi ee template il system command engane chyam enn study chyuka
-->`{% import os %} {{os.system('rm /home/carlos/morale.txt')`  ee payload nmmal itt chyumbol ee command aakum(aadyam ls polula command chyanam)
-->so ith ingane ittal aakila . aadyam user.first_name enn kodukanam enit }} double braces itt aa expression close chyanam enitan ee payload idendath
-->`blog-post-author-display=user.first_name}}{%+import+os+%}{{os.system('pwd')&csrf=....`      so ivide namal pwd enna command ittu.so ini namal comment aa page refresh chyumbol namude commentin mukalil namude perinte koode `/home/carlos` enn kanikum(space ull sthalath + symbol iduka)
-->so athinartham namude RCE work aayi ini namuk lab il parayuna pole carlose ine morale.txt delete chyuka athinte payload :
-->`blog-post-author-display=user.name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')`
-->ee payload namude preferred name request il itt send chyuka ini comment page reload chythal delete aayikolum

==============================================================

       Lab-3(Server-side template injection using documentation)
-->lab open chyth login chyuka.ivide namal content manager aayan kerandath conten-manager : C0nt3ntM4n4g3r
-->enit ethengilum product click chyuka.athil edit template enn option undakum ath click chyuka
-->enit athil static aayullathelam remove chyuka apol last  `${product.stock} left of ${product.name} at ${product.price}`  enn mathram undakum
-->ini ath preview/save click chyuka apol namuk producinte stock ,name,price oke dynamic aayi render chyunath kanam
-->ini athil ethengilum oru product.templateil enthengilum kodukuka like product.namedsfs enit preview adichal error varum karanam engine ath render chyan patil karanam angane oru template illa
-->apol browser il enginte per kanikum `...ateengines.FreeMarker.processInput(FreeMarker.java:56) at lab.actio....`
-->ithil ninum manasilakam freemarker aan engine
-->ini intro yil kodutah site il poi (hactricks) freemarker enn search chythal namuk athumayi bhandapetta payload oke kanum.so athil first ullath select chyuka.enit ee template ull sthalath paste chyuka
-->`<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id")}` ithan payload.ini preview click chythal ee command run chyum ie , uid=12002(carlos) gid=12002(ca....
-->so lab solve aakan morale.txt enna file delete chyanam so namude ee payload il "id" ennath maati `rm morale.txt` enn kodukuka apol ath delete aakum so lab solve aayi

==============================================================

         Lab-4(Server-side template injection in an unknown language with 
                             a documented exploit)
-->lab il keruka athil login option illa.so athinartham authentication check chyuna sambavam ila
-->ithil first product inte view-details enn button click chyuka
-->apol `Unfortunately this product is out of stock` enn page il kanikum.ee same msg url ilum kanikum
-->so aa url il vere enthengilum adich nokuka apol athum page il kanikum ie, message=hello enn koduthal ath page il reflect chyunund
-->so url il message=`${{<%[%'"}}%\` enn kodukuka enit search chyumbol namuk error kanan patum
-->aa error il nokumbol kanam handlebars enna engine aan use chyunath
-->so athinte exploit code online il ninum edukuka.intro yil ulla website il und avidunum edukam
-->enit athile "whoami"  mati "rm+/home/carlos/morale.txt"  ennakiyal ath delete aakum apo lab solve aayi
-->real life il chyumbol namuk ee code execute chyuna sthalath namade collaborator client url koduth send chyth nokam apol namuk manasilakaum ee ayakuna sathanam ping aakunundon.angane entho ivan kanikunund

==============================================================

           Lab-5(Server-side template injection with information disclosure via 
                      user-supplied objects)
-->ivide nammal content-manager aayi kerunu.enit ehthengilum oru item click chyuka enit edit template options select chyuka
-->athile static aaya description oke remove chyuka athinu sehsham athil dynamic aayi veruna template mathram vekuka ie,`{{product.stock}} left of {{product.name}} at {{product.price}}` 
-->ini ithle ethengilum onil ->`${{<%[%'"}}%\`  ee sring iduka ie, `product.pri${{<%[%'"}}%\ce` 
-->apol error varum.ee error il ninum manasilakam django template engine aan use chyunath
-->so namuk django template inte documentation study chyanam and notice that the built-in template tag `debug` can be called to display debugging information.
-->so template full ayi remove chyth ->`{% debug %}` ee code itt preview click chyuka apol athinte sambavam aakum
-->ithupole namuk setting enna template tag use chyan patum so `{{settings}}` enn koduth preview click chyuka
-->ini document il nokiyal kanam ee settings tag in oru SECRET_KEY property undakumpolu.so nammal athukoodi cherth code itt preview click chythal namuk secret key kitum
-->`{{settings.SECRET_KEY}}`   ith itt preview click chythal namuk key kitum ith submit chythal lab solve aayi

==============================================================

          Lab-6(Server-side template injection in a sandboxed environment)
-->ivide namal conten-manager aayi login chyuka enit product il poi edit template click chyth static aaya karyanal remove chyuka enit 
-->athile dynamic aaya code il enthengilum add chyth error varon nokuka ` ${product.stock} left of ${product.name} at ${product.prsdfsdice}`   
-->apol error varum.athil kaanam freemarker template engine aan use chyunath 
-->ivide intro yil ulla site ilek pokuka enit athil freemrker exploit nokuka athil sandbox payload kanam ath copy chyth namude site il paste chyth preview adikuka
-->payload   :
                     <#assign classloader=article.class.protectionDomain.classLoader>
					<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
					<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
					<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
					${dwf.newInstance(ec,null)("id")}
-->apol error varum karanam ithil article.class ennan ullath.namude template il produc. ennnan ullath so ithile first line il ulla article.class maati product.class ennnakuka enit preview adichal id command execute aakum
-->so namuk vendath oru password file open chyth athile content eduth submit chythalan lab solve aakuka
-->so id maati ls adikuka apol my_passwrd.txt enna file kanam.so cat my_password adikuka apol athile password kitum . ath eduth submit chyuka . apol lab solve aayi

==============================================================

                Lab-7()
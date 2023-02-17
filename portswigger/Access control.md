-->Access control (or **authorization**) is the application of constraints on who (or what) can perform attempted actions or access resources that they have requested.
-->In the context of web applications, access control is dependent on authentication and session management: 
     1)Authentication identifies the user and confirms that they are who they say they are.
     2)Session management identifies which subsequent HTTP requests are being made by that same user.
     3)Access control determines whether the user is allowed to carry out the action that they are attempting to perform
-->==**Broken access control vulnerabilities** exist when a user can in fact access some resource or perform some action that they are not supposed to be able to access. ==

             Lab-1(Unprotected admin functionality)
-->ivide lab il keruka enit url il /robots.txt enadikuka apol disallow: /administrator-panel enna kanam
-->so /robots.txt mati /administrator-panel  enn kodukuka apol namal admin aayi marum enit user carlosine delete chyuka aayi

        Lab-2(Unprotected admin functionality with unpredictable URL)
-->sensitive files oke petenn guess chyuna reethiyil ayirikila url il undavuka ennalum applicartion athinte url leak chythitundavam.eg:javascript code nokuka athil chelepo undakum if user admin= ivide oke sensitive file undakam
-->ivide lab il home page edukuka enit view page source edukuka
-->athil javascript code und athil nokiyal kanam oru function il - adminPanelTag.setAttribute('href', '/admin-xl7xm3'); so ithile /admin-x17xm3 eduth url il kodukumbol namal admin aayi marum ini carlos ine delete chyuka

          Lab-3(User role controlled by request parameter)
-->Some applications determine the user's access rights or role at login, and then store this information in a user-controllable location, such as a hidden field, cookie, or preset query string parameter
-->ivide namal intercept on aakuka enit wiener:peter password adich login chyuka.enit aa req intercept chyumbol kanam admi=false enn so ath true aakuka forward adikuka angane or request ilum admin=true enn kodukuka angane avasanam admin panelil poi carlos ine delete chyuka

        Lab-4(User role can be modified in user profile)
-->ivide nammal namude credentials adich keruka enit update email enn optionil puthiya email kodutha update chyuka
-->ithinte requestil nokiyal kanam email update aakunath oru json format il aan--
               {"email":"jef@dontwannacry.com"}  so ith repeater il viduka
-->enit send adich response nokuka apol kanam--
               {
                "username": "wiener",
                "email": "jef@dontwannacry.com",
                "apikey": "ttJCc086PvL3nNsfeJGLT3KKNOE2JAAH",
                 "roleid": 1
                 }
-->so nammal enth chyanamenal namude requestil "roleid" enna puthiya oru variable koduth athinte value matanam apol namuk vere oru account aayi keran patum ie --
               {"email":"jef@dontwannacry.com","roleid":2}
-->ini send adich ithinte response nokiyal kanam 302 found enn so ini browser refresh chyth nokiyal kanam namal admin ayi en.so ini carlos delete chyuka

       Lab-5(URL-based access control can be circumvented)
-->ivide nammal lab edukuk enit url inte avasanam /admin enn kodukuka apol kanam access denied enn so aa same karyam onnukudi repeat adich burp il nokuka
-->enit request il GET / enn kanam ath angane thane vekuk enit puthiya oru header create chyanam ie,x-original-url.so x-original-url:/invalid enn kodukuka apol response il nokiyal kanam "not found" enn.athinartham backend namude ee x-original-url enna header process chyunun enanu.karanam ivide GET / enna sathanam aan namuk response tharendath.ath avide induthanum.pakshe namal kodutha x-origina-url enna parameter nokiyan response thanath
-->so nammal (request il) x-original-url:/admin enn koduth send chythal 200 ok enn kanam so namuk angane admin aayi keram.karanam backend ee header aan nokunath
-->ini carlos enn account delete chyan GET /?username=carlos enn kodukuka enit x-original-url:/admin/delete enn koduthal aa account delete aakum

      Lab-6( Method-based access control can be circumvented)
-->ivide namuk already admin access namuk und.namuk enthan chyendath enal wiener in admin power kodukanam
-->so aadyam admin aayi keruka enit carlos in admin power kodukuka
-->aa request burp il edukuka.enit repeater il viduka
-->ini lab il ninum logout chyuka.enit wiener:peter adichu keruka.login chythathinu shesham kituna GET request repeateril viduka
-->ivide ee kitiya GET request il weiner inte session id copy chyuka enit nerethe carlos ine admin aakan use chytha repeater il ulla request il poi usernmae=wiener kodukuka enit session=wienerintepastechyuka enit send chyuka apol response il kanam "unauthorised" enn 
-->so POST ennath POSTx enn itt nokuka apol response il - "Missing parameter 'username'" enn kanma
-->so aa requestil right click chyth "change request method" enn kodukuka apol aa post request get request aayi marum ini username=wiener,session=nerethe copy chythath paster chyuka enit send adkumbol namuk kanam wiener in admin privilege aayi maruth.aayi

       Lab-7(User ID controlled by request parameter)
-->ivide namal wiener:peter adich keruka 
-->enit myaccount button click chyuka 
-->url il nokiyal kanam ?id=wiener enn so ath maati id=carlos enn kodukuka enit send adikuka apol namuk carlos inte apikey kitum ath submit chythal lab solved aayi

      Lab-8(User ID controlled by request parameter, with unpredictable user IDs)
-->ivide namal aadyam lab open chyuka enit athil kore blog post undakum athil onn carlos itta post aan
-->so ath open chyuka apol aa post inte thazhe aayi kanam aa post itta user inte name
-->so aa name click chyuka apol carlos userne patti kanikum
-->aa page ile url il nokiyal kanam ...t/my-account?id=a211ae3f-2144-45bd-906f-55d84c888bfd.so ithan carlos inte id
-->ini nammal wiener:peter adich login chyuka enit myaccount button click chyumbol namude id kanikum so aa id remove chyth carlos inte id koduthal nammal carlos aayi

      Lab-9(User ID controlled by request parameter with data leakage in redirect )
-->ivide namal wiener:peter vech keruka enit my account click chyuka enit ?id=wiener ennath carlos aakuka enit send kodukuk repeater il
-->apol responseil kanam namal carlos aayi marunila pakaram login page ilek redirect aakukayan chyunath
-->pakshe burp ile response il nokiyal kanam carlos inte api key disclose chyth kidakunath.ath copy chyth submit chythal mathi aayi
-->ivide ee same process njan browser il chythu,login page ilek redirect aayi but api key kandila.burp il chythapozhan kandath

    Lab-10(User ID controlled by request parameter with password disclosure)
-->ivide namude credential vech keruka.avide myaccount page il poi nokiyal kanm namude password avide set aayi kidakunath kanikum
-->so ?id=wiener ennath administrator aakuka apol administratorinte passwd kanikum
-->ath copy chyuka ini administrator aayi keruka enit carlos ine delete chyuka.aayi


#### Insecure direct object references:
-->IDOR are a subcategory of access control vulnerabilities
-->IDOR vulnerabilities are most commonly associated with horizontal privilege escalation, but they can also arise in relation to vertical privilege escalation. 
   
    Lab-11(Insecure direct object references)
-->ivide oru chat option und athil nammal chat chythit view transcript ennadichal nammal chat chythathoke oru text file aaki namuk download chyth tharum
-->so namal enthengilum chat chyuka enit view transcript adikuka.adikunathin mumb intercept on chyuka
-->ini ath forward adikuka.enit namuuk varuna textfile ulla request nokuka.athil kanam:
        GET /download-transcript/2.txt HTTP/1.1   so ivide namal 2.txt enath 1.txt enn aakuka apol 1.txt inte karyanngal namuk kitum
-->so 1.txt il carlos userinte password und.so angane namuk passwd kity
-->ini aa credential vech login chyuka aayi

     Lab-12(Multi-step process with no access control on one step)
-->ivide nammal admin aayi keranam enit carlos user ine admin power aaki upgrade chyanam.ath burp il edukanam
-->athil upgrade chyuna samayath chodikunund are you sure? enn so yes kodukanam nammal.athan main aayit namuk venda request
-->so ini logout chythit wiener aayi keruka enit wiener inte session cookie copy chyth nerthe are you sure? request il poi session paste chyth usernmae=wiener ennu parameter value aayi kodukuka enit send adikumbol lab solve aayi.wiener admin power kiti
-->==pakshe enik ariyathath namuk already admin power kitiyalo so pine enthin ee reethiyil privilege matanam? direct carlos inte chythapole wiener chythapore?==

               Lab-13(Referer-based access control)
-->ividem nerthe pole thane
-->adyam admin aayi keruka enit carlos ine upgrade chyuka enit aa request repeater il iduka 
-->aa request il nokiyal kanam referer:labid/admin ennan.so athinartham referer il ulla /admin ullath kondan upgrade chyan patunath
-->ini wiener aayi keruka enit nerthe pole session cookie copy paste chyuka username=wiener ennakuka.enit send adichal lab solve aayi
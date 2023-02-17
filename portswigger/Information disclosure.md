-->Information disclosure, also known as information leakage, is when a website unintentionally reveals sensitive information to its users like:
        
    1)Data about other users, such as usernames or financial information
    2)Sensitive commercial or business data
    3)Technical details about the website and its infrastructure

--> Some basic examples of information disclosure are as follows:

    1)Revealing the names of hidden directories, their structure, and their contents via a robots.txt file or directory listing
    2)Providing access to source code files via temporary backups
    3)Explicitly mentioning database table or column names in error messages
    4)Unnecessarily exposing highly sensitive information, such as credit card details
    5)Hard-coding API keys, IP addresses, database credentials, and so on in the source code
    6)Hinting at the existence or absence of resources, usernames, and so on via subtle differences in application behavior

==============================================================

        Lab-1(Information disclosure in error messages)
-->ivide nammal ethengilum product edukuka enit burp histroy nokiyal kanam aa request il productid=2 enna parameter 
-->so 2 enna aa value mateet productid="something"  enn kodukuka apol 500 internel server enn kanikum
-->aa error inte thazhe aayi website inte server um athinte version um kanikunund ie, apche 2 2.3.13 enn ok

        Lab-2(Information disclosure on debug page)
-->website develop chyuna samayath developers website debugg chyum enit athile informations oru file il aakum.ella website ilum ella debelopers um debugg chyum. pakshe athile contents production environmentil leak aayal oru attacker in athile contenst nokam:
        Values for key session variables that can be manipulated via user input
        Hostnames and credentials for back-end components
        File and directory names on the server
        Keys used to encrypt data transmitted via the client
ithoke nokeet athinanusarich pravarthikam
-->ivide namal aadyam lab site edukuka
-->enit aa site inte request history il nokuka enit target il poi aa site right click chyth engagement tools->find comments edukuka
-->apol aa website il ulla ella comments um crawl chyum ithil oru comment ennath - /cgi-bin/phpinfo.php aan so namal aa tab close chythit target tab il aa site inte > symbol click chyth athile directories nokuka athil cgi-bin ena directory kanam athil phpinfo ena file kanam ath send to repeater koduth repeater il poi edit onum chyathe send kodukuka.response kitan 2-3 minute edukunund
-->ith enthanena phpinfo site aan epozhum kanunath thane
-->athil secret key enna oru bhagam und athil secret key und ath submit chythal aayi
 
              Lab-3(Source code disclosure via backup files)
-->ivide nammal /robots.txt enna path ilek pokuka
-->avide /backup enn path disallowed enn kanan patum
-->athilek poyal kanam "ProductTemplate.java.bak." enna backup source code file
-->so athilek pokuka apol manasilakum ath oru java source code file aanen 
-->athil connectio_builder enna oru variable kanan patum athil postgresql inte password kanan patum ath copy chyth submit chythal lab complete aayi

         Lab-4(Authentication bypass via information disclosure)
-->ivide TRACE ena http header use chythan exploit chyunath
-->ini lab il keruka enit /admin enna pathilek pokuka.apole kanam -  **Admin interface only available to local users** so local user aayirikanam admin aahi kerendath athayath aa nerworkil ulla user aayirikanamenn.
-->ath engane aanu kooduthal vyakthamayi manasilakunathenal ee /admin rquest repeater il iduka enit GET ennath matti TRACE enn kodukuka.apol responsil oru http header undakum ->X-Custom-IP-Authorization: 103.147.208.100 so ithile ip ennath namude ip aan.ee custom headeril nokum request ayakuna aalude ip localhost aano enn.enal mathrame login chyan patoo
-->so namal enthuchyanam ennal ee ip mati localhost ip(127.0.0.1) kodukanam
-->athin ee header angane thane copy chyuka enit proxy->options->match and replace edukuka enit match codition blank kodukuka enit replace condition il - **X-Custom-IP-Authorization: 127.0.0.1** enn kodukanam.apol ini burp ayakuna oro request ilum ee header automatic aayi add akum apol automatic aayi admin aayi marum
-->ini browseril poi home button click chyuka apol kanam admin panel enna button athinartham namal admin ayi enan.karanam namal burp proxy chyunond thane namal ayakuna oro request um burp ilude aan kadan pokunath so automatic aayi ee header add ayikolum

           Lab-5( Information disclosure in version control history)
-->ivide lab il /.git enn adich search chyuka apol git inte karyangal kanum
-->ini aa git inte directory copy chyanam
                 -->wget -r https://your-lab-id.web-security-academy.net/.git/
-->ini aa directory il poikuka ->ls -la adichal kanum .git ena directory athil keruka 
-->git log enn adikuka apol rand commit kanan patum
-->git differ first-commint second-commit    
-->rand commitum paste chythathinu shesham enter adikuka apol namuk admin password kitum
-->ingane chyunilengi git-cola enna app upayogichum chyam.apol aa appil ee directoru open chythit undo last commit enn adikuka apol oru admin.config enn file kanam athil keriyal ithe admin password kitum 
-->ini administrator:passwd koduth keri carlos ine delete chyuka kazhinju
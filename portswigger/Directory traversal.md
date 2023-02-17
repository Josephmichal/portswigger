-->Directory traversal (also known as file path traversal) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application
-->This might include application code and data, credentials for back-end systems, and sensitive operating system files
-->udhaharanathin oru web app il ninum photo edukunu enn karuthuka
               <img src="/loadImage?filename=218.png">
-->ee images okke store chythirikuna locationa - /var/www/images/218.png
-->so ee 218.png enna file kitan - /var/www/images/218.png enn varum
-->The application implements no defenses against directory traversal attacks, so an attacker can request the following URL to retrieve an arbitrary file from the server's filesystem: 
         https://insecure-website.com/loadImage?filename=../../../etc/passwd
--> This causes the application to read from the following file path: 
         /var/www/images/../../../etc/passwd
--> ../ ennal one level up ennathanen namuk ariyamalo.The three consecutive ../ sequences step up from /var/www/images/ to the filesystem root, and so the file that is actually read is: 
           /etc/passwd
-->On Windows, both ../ and ..\ are valid directory traversal sequences, and an equivalent attack to retrieve a standard operating system file would be: 
          https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
		  
		  
		        Lab-1(File path traversal, simple case)
-->ivide namuk /etc/passwd aanu kandupidikendath
-->so aathyam ethengilum oru product click chyuka enit intercept or http history il pokuka
-->note - http history il image request okke unchecked aayitan ullathengil scope il poi check iduka
-->apo kanam filename=...jpg eno mato so ath nammal =../../../../../../../../../etc/passwd  enn kodukuka apol root il ninum namuk passwd kitum athre ullu

       Lab-2(traversal sequences blocked with absolute path bypass)
-->ividem nerthe pole filename= parameter il aan chyunath
-->but ivide ..?filename=../../../../../../../../../etc/passwd enn kodukumbol no such file ennan kanikunath.so athinartham entho sanitisation ithinte edayil nadakunu enan
-->so angane idunathin pakaram filenam=/etc/passwd enn idumbol ath kitunund
         
		Lab-3(traversal sequences stripped non-recursively)
-->ivedem aa filename il aan chyunath
-->ivide namal ../ chyumbol website ath strip chyunu athayath ath remove chyunu
-->so nammal ....// enn rand pravisham aayi idanam apol nadakum
-->ie, ....//....//....//etc/passwd ittal namuk sambavan kitum

     Lab-4(traversal sequences stripped with superfluous URL-decode)
-->ividem filename il aan chyunath
-->ivide nerthe pole strip alla nadakunath so ath akila
-->ivide nammal / ne double url encode chyunu
-->ie,   ..%252f..%252f..%252fetc%252fpasswd
-->apol namuk passwd kitunu

                   Lab-5(validation of start of path)
-->ividem filename il aan chyunath
-->ivide filename=/var/www/images/62.jpg  ennan kanikunath
-->so filename=../../../etc/passwd enn kodukumbol missing parameter filename ennan kanikunath
-->so filename=/var/www/images/../../../etc/passwd enn koduthal passwd kitum

          Lab-6(validation of file extension with null byte bypass)
-->ividem filename il aa chyunath
-->ivide app validates chyunund athayath filename end chyunath ethengilum extension vechitano enn.so nammal null byte use chyanam.apol aa extension varukayum chyum ennal ath read chyukayum ila
-->so filename=../../../etc/passwd%00.jpg ingane aayirikum payload undavuka
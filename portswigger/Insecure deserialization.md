## Insecure deserialization :
-->the process of converting an object to some stream of bytes/binary is serializaiton and the reverse process of converting the stream of bytes to an object is deserialization
--> serialize ennal complex aaya data structure like objects oke oru prathyeka sthalathek like database ilek oru pratheyeka format il save chyunathan
--> deserialzation ennal serialize chytha objects oru sthalath save chythite so aa sathanam retrieve chyunu athum athinte original formil enganeyano undayrunath athe form il thanne ok
-->Some languages serialize objects into binary formats, whereas others use different string formats
-->**ysoserial** ,**PHPGCC** ee rand tools githubil und.ith randum use chyth namuk deserialization vuln kandupidikam
-->It is even possible to replace a serialized object with an object of an entirely different class
-->Alarmingly, objects of any class that is available to the website will be deserialized and instantiated, regardless of which class was expected. For this reason, insecure deserialization is sometimes known as an `object injection` vulnerability.
-->An object of an unexpected class might cause an exception.Many deserialization-based attacks are completed **before** deserialization is finished
-->This means that the deserialization process itself can initiate an attack
-->deserialization process kazhinjan webapp check chyunathengil athil karyamila karanam nerthe paranjapole deserialization kazhiyunathin munne attack nadakum.so webapp deserialization process aarambikumbol thane chech chyanam enthengilum kozhapam undon


### #1 How to identify insecure deserialization :
-->nammal blackbox aano atho whitebox aano ennathinanusarich irikum namal ee vuln kandupidikuka
-->burp scanner automatic aayi sereialized objeccts kandupidikum
-->consider a `User` object with the attributes :
              
              $user->name = "carlos"; 
              $user->isLoggedIn = true;
-->When serialized, this object may look something like this:

        O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}

-->The native methods for PHP serialization are `serialize()` and `unserialize()`. If you have source code access, you should start by looking for `unserialize()` anywhere in the code and investigating further.
-->languages, such as Java, use binary serialization formats and this is more difficult to read
-->For example, serialized Java objects always begin with the same bytes, which are encoded as `ac ed` in hexadecimal and `rO0` in Base64.
-->Any class that implements the interface `java.io.Serializable` can be serialized and deserialized. If you have source code access, take note of any code that uses the `readObject()` method, which is used to read and deserialize data from an `InputStream`.

### #2 Manipulating serialized objects :
-->Exploiting some deserialization vulnerabilities can be as easy as changing an attribute in a serialized object
-->Broadly speaking, there are two approaches you can take when manipulating serialized objects. You can either edit the object directly in its byte stream form, or you can write a short script in the corresponding language to create and serialize the new object yourself.The latter approach is often easier when working with binary serialization formats
--> `O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}` ingane oru serilaized object kiti enn vecho.so namuk attributes modify chyth deserialization attack chyam. ivide isAdmin enn atrribute inte value ennath b:0 aan athyath boolean and zero means false so namuk ath maati b:1 enn kodukam one means true.so ingane attribute value maatit pine sadharana pole re-encode chyuka aa object enit send chyuak


              Lab-1(Modifying serialized objects)
-->ivide namal lab il keruka enit login chyuka wiener:peter
-->enit burp proxy history il nokiyal kanam /POST login request in shesham namal /GET my-account request vazhi account info retreive chyunu.
-->aa request il namuk oru session cookie kitiyath kanam.athupole inspector tab il nokiyal kanam request cookie enna oru tab athil nokiyal kanam aa value automatic aayi namuk decode chythirikunu url->then base64. so base64 decode chyumbol namal ingane kanam : `O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}` so ivide ninum namuk kanam deserialization nadakunund enn
-->so ini aa string eduthit athile admin enn attribute modify chyuka
-->so adyam ith repeater il viduka enit send adikuka enit avide ulla inspector vech athile admin attribute ile 0 enna value 1 aakuka enit apply changes enn button click chyuka apol automatic aayi aa cookie marikolum enit aa request send chyuka
-->NOTE: namal manual aayan chyunathengil aadyam url -> base64 decode chyuka enit attribute modify chyuka enit base64->url encode chyuka ok
-->apol namuk kanam admin panel namuk kitiyirikunu response il 
-->so /my-accounts enna path mati /admin enn kodukuka enit send chyumbol admin panalilek namal kerunu ini avide carlos delete chyenda href=/admin/delete?username=carlos kanikunu so ini ee same path request il aaki send chythal carlos enna account delete aakum.apol lab solve aayi
-->==ee labil ninum manasilakendath enthenal serialization-based session mechanism aan use chyunath== athan namal expoit chyunath.karanam deserialization namuk edit chyan patum ithuvazhi.server derserialization process kazhinjitayirikum check chyuka.athukond thane aa process kazhiyunathin munne exploitation nadannu kazhinju

==============================================================

           Lab-2(Modifying serialized data types)
-->ivide namal nerthe pole login chyuka enit /GET my-accounts enn reqeust repeater il viduka enit athil inspector il session cookie decode chythath kanam : `O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"kznhtfism591l476mdp7n4vm56ulk6on";}`
-->so ithile attributes oke namuk modify chyanam :
-->`O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}`
-->ivide s ennal string aan i ennal integer
-->s:13 aaki adminstrator kodukumbol. access_token ennath 12 ennam und athukond 12 kodukunu.athinushesham token value gibbridh undayath namal 0(zero) enn kodukunu
-->string , interger oke datatypes aan.zero enn koduthathkond thane ath string alla integer aan so "" double qoutes kodukanda
-->enit namal repeater il send adichal admin panel kanum.enit sathrana pol /admin enn reqeuts path aakuka enit carlos account delete chyuna path kanum aa path request il aaki send adichal lab solve aakum

==============================================================

-->ipo chytha rand lab ilum format string aayirunu so namuk direct kandapol thane manasilayi but binary format aanengil direct modify chyan paranemenil so Hackvertor enna extension burp il add chyuka.
-->With Hackvertor, you can modify the serialized data as a string, and it will automatically update the binary data, adjusting the offsets accordingly

==============================================================

### #3 Using application functionality :

    Lab-3(Using application functionality to exploit insecure deserialization)
-->ivide namal lab il keri log in chyuka.enit athil delete enna button undakum so intercept on aaki delete click chyuka enit aa requet repeater il viduka intercept ilulla request drop chyuka
-->repeater il vannal kanam session cookie inspector tab il decode chythirikunu.ath nokumbol kanam deserialization nadakunund
-->.....s:11:"avatar_link";s:18:"users/wiener/avatar";}   ennan undavuka.so nammal ith modify chyth test chyanam
-->namuk mattoralude username oke koduth aa account delete aakan patumon nokanam
-->O:4:"User":3:{s:8:"username";s:5:"gregg";s:12:"access_token";s:32:"vjnz5etkhjs3f29rsimg2ikqnjgccah8";s:11:"avatar_link";s:23:"/home/carlos/morale.txt";}
-->ivide nammal namude name  maati carlos enn kodukunu.enit apply changes adich veendum intercept on aakuka
-->enit onude delete account click chyuka enit athil ulla session maati nammude ee malicious session kodukuka apol lab solve aayi means carlos enna account delete aayi

==============================================================

### #4 Injecting arbitrary objects :
-->attacker in source code access chyan patumengil ella classes ine pati study chyth exploit undakn easy aayirikum
-->Magic methods are a special subset of methods that you do not have to explicitly invoke. Instead, they are invoked automatically whenever a particular event or scenario occurs
-->Magic methods are a common feature of object-oriented programming in various languages. They are sometimes indicated by prefixing or surrounding the method name with double-underscores.
-->One of the most common examples in PHP is `__construct()`, which is invoked whenever an object of the class is instantiated, similar to Python's `__init__`.
-->ypically, constructor magic methods like this contain code to initialize the attributes of the instance. However, magic methods can be customized by developers to execute any code they want.
-->Magic methods are widely used and do not represent a vulnerability on their own. But they can become dangerous when the code that they execute handles attacker-controllable data, for example, from a deserialized object


         Lab-4(Arbitrary object injection in PHP)
-->ivide namal lab il keri log in chyunu.enit burp ile site map il nokumbol athile lib directory il customtemplate.php enna file kanunu.so ath repeater ilek viduka
-->ini athile path il ~ sign iduka ie, /libs/cutomtemplate.php~ enit send chyuka.apol namuk aa php code kitum
-->ithil customtemplate enna oru class aan kanikunath.this class contains the `__destruct()` magic methodj.This will invoke the `unlink()` method on the `lock_file_path` attribute, which will delete the file on this path
-->athupole thane namal login chythathin shesham /my-accounts enna request il session cookie yil deserialized object aan ullat.inspector tab il ath kanikunund
-->so namuk matte sthalath ninn php code kanan patti.atha parayunath source code access undengil insecure deserialization peten kandupidikam.ivide namuk php code kiti.athupole deserialized object ulla sthalavum kandupidichu.so ee deserialzation nadakuna object eduth athil namuk ee php code il kanda karyangal modify chyth cherth send adichal server deserialization nadathunath namal chyth vecha karyangal aayirikum
-->ivide namuk carlos inte account aan delete chyendath so namuk ee deseralized object athinulla code chyam with the help of that customtemplate.php source code
--->PAYLOAD : `O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}` .ith koduth apply changes adich onengil repeater il thane send chyuka.allengil aa page reload chyuka.karanm login chythapol aanalo /my-accounts enna request ayakunath so aa same reload chyuka
-->enit athile session cookiyil ee payload encode chythath paste chyth intercept il forward adichal lab solve aayikolum

==============================================================

### #5 Gadget chains :
-->A "gadget" is a snippet of code that exists in the application that can help an attacker to achieve a particular goal
-->

        Lab-5(Exploiting Java deserialization with Apache Commons)
-->Ithentho java yil insecure deserialization chyunathan.ath correct manasilkunila
-->ysoseral enna tool install chyth kaliyil : `java -jar path/to/ysoserial.jar CommonsCollections4 'rm /home/carlos/morale.txt' | base64`  enn type chyth enter adikuka
-->This will generate a Base64-encoded serialized object containing your payload.
-->ith url ecode chyth deserilazation object ulla sthalath replace chyuka.athayath ivide session cookiyil analo deseriazed object ullath avide ee payload paser chyth send chyuka.ingane chyumbol namade carlos inte account delete aakumena parayunath


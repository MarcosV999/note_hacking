
Los permisos se dividen en tres categorías de usuarios:
- U (user)
- G (group)
- O (others)
Cada categoria se divide en 3 tipos de permisos:
- r (read)
- w (write)
- x (execute)

Comando para ver los permisos:
``` bash
ls -l
```
-rw-rw-r--  1 marcos marcos   971 Feb 23 11:06  marcos.sh

First symbol:
- - : regular file (.txt, .sh, so on)
- d: directory
- l: link symbol
First trio (rwx): owner permissions (user)
Second trio (rwx): group permissions (group)
Third trio(rwx): others permissions (others)

Grant permissions:
``` bash
chmod u+x <file_name>
chmod 754 <file_name>
```

1. El Método Numérico (Octal):
- **4** = Lectura (`r`)
- **2** = Escritura (`w`)
- **1** = Ejecución (`x`)
- **0** = Ninguno (`-`)

| Suma          | Permisos Resultantes | Significado                      |
| ------------- | -------------------- | -------------------------------- |
| **7** (4+2+1) | rwx                  | Todo: Leer, escribir y ejecutar. |
| **6** (4+2)   | rw-                  | Leer y escribir.                 |
| **5** (4+1)   | r-x                  | Leer y ejecutar.                 |
| 4             | r--                  | Solo lectura.                    |

2. El Método Simbólico (Letras)
Usa esta estructura: {A quién} {Operación} {Permiso}
- **A quién:** `u` (user), `g` (group), `o` (others), `a` (all). 
- **Operación:** `+` (añadir), `-` (quitar), `=` (asignar exacto).
- **Permiso:** `r`, `w`, `x`.

3. Permisos Especiales
	1. **SUID (Set User ID)**
		Cuando un archivo tiene activo el bit SUID, cualquier usuario que lo ejecute lo hará con los **privilegios del dueño del archivo**, no con los suyos propios.
		**Representación:** Una `s` minúscula en el lugar de la `x` del dueño (`-rws------`).
		Valor numérico: **4000**.
		**Ejemplo real:** El comando `passwd`. Para cambiar tu contraseña, necesitas escribir en `/etc/shadow` (que solo root puede tocar). `passwd` tiene el bit SUID de root para que puedas modificar tu propia clave legalmente.
	2. **SGID (Set Group ID)**
		Tiene dos funciones dependiendo de si se aplica a un archivo o a una carpeta:
		**En Archivos:** El proceso se ejecuta con los privilegios del **grupo** dueño del archivo.
			**Valor numérico:** **2000**.
			**Representación:** Una `s` en el lugar de la `x` del grupo (`-rwxr-s---`).
        **En Directorios (Muy útil):** Cualquier archivo nuevo que se cree dentro de esa carpeta heredará automáticamente el **grupo** de la carpeta madre, sin importar quién lo cree.
        Es ideal para carpetas compartidas entre equipos de desarrollo o seguridad.
        
    3. **Sticky Bit (Bit de Persistencia)**
	    Este permiso se aplica casi exclusivamente a **directorios** para proteger los archivos de los usuarios entre sí.
	    **Función:** Evita que un usuario pueda borrar o renombrar archivos de otros usuarios dentro de esa carpeta, aunque tenga permisos de escritura en la carpeta. **Solo el dueño del archivo o root pueden borrarlo.**
		    **Representación:** Una `t` al final de los permisos (`drwxrwxrwt`).
		    **Valor numérico:** **1000**.
		    **Ejemplo real:** La carpeta `/tmp`. Todos pueden escribir ahí, pero no sería seguro que tú pudieras borrar mis archivos temporales.
		
| Permiso | Bit    | Valor | Ejemplo           | Resultado en `ls -l` |
| ------- | ------ | ----- | ----------------- | -------------------- |
| SUID    | User   | 4     | chmod 4755 script | -rwsr-xr-x           |
| SGID    | Group  | 2     | chmod 2755 folder | drwxr-sr-x           |
| Sticky  | Others | 1     | chmod 1777 /data  | drwxrwxrwt           |
### ¿Cómo detectar la "S" mayúscula vs minúscula?

A veces verás una `S` (mayúscula) o una `T` (mayúscula). Esto es un aviso del sistema:
- **Minúscula (`s`, `t`):** El bit especial está activo **y además** el archivo tiene permiso de ejecución (`x`). (Todo bien).
- **Mayúscula (`S`, `T`):** El bit especial está activo **pero el archivo NO tiene** permiso de ejecución. Esto suele ser un error de configuración, ya que el permiso especial no servirá de nada si nadie puede ejecutar el archivo.


Comando de Pentesting (Enumeración):
``` bash
find / -perm -4000 -type f 2>/dev/null
```
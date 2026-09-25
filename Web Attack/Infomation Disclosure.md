## How to test for information disclosure vulnerabilities
- [Fuzzing](https://portswigger.net/web-security/information-disclosure/exploiting#fuzzing)
- [Using Burp Scanner](https://portswigger.net/web-security/information-disclosure/exploiting#using-burp-scanner)
- [Using Burp's engagement tools](https://portswigger.net/web-security/information-disclosure/exploiting#using-burp-s-engagement-tools)
- [Engineering informative responses](https://portswigger.net/web-security/information-disclosure/exploiting#engineering-informative-responses)

Many websites provide files at `/robots.txt` and `/sitemap.xml` to help crawlers navigate their site.
### Error messages
One of the most common causes of information disclosure is verbose error messages. As a general rule, you should pay close attention to all error messages you encounter during auditing.
The content of error messages can reveal information about what input or data type is expected from a given parameter. This can help you to narrow down your attack by identifying exploitable parameters. It may even just prevent you from wasting time trying to inject payloads that simply won't work.
![](Pasted%20image%2020260925125841.png)
### Information disclosure due to insecure configuration
developers might forget to disable various debugging options in the production environment. For example, the HTTP `TRACE` method is designed for diagnostic purposes. If enabled, the web server will respond to requests that use the `TRACE` method by echoing in the response the exact request that was received. This behavior is often harmless, but occasionally leads to information disclosure, such as the name of internal authentication headers that may be appended to requests by reverse proxies.
![](Pasted%20image%2020260925124257.png)
![](Pasted%20image%2020260925125613.png)

### Version control history
Virtually all websites are developed using some form of version control system, such as Git. By default, a Git project stores all of its version control data in a folder called `.git`. Occasionally, websites expose this directory in the production environment. In this case, you might be able to access it by simply browsing to `/.git`.
While it is often impractical to manually browse the raw file structure and contents, there are various methods for downloading the entire `.git` directory. You can then open it using your local installation of Git to gain access to the website's version control history. This may include logs containing committed changes and other interesting information.
```
wget -r https://YOUR-LAB-ID.web-security-academy.net/.git/
```

```
git checkout c0677335bac2909bc3adb51f4d475e4636c71fca
git reset --hard c0677335bac2909bc3adb51f4d475e4636c71fca
```
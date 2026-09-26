### Discovering API documentation
Even if API documentation isn't openly available, you may still be able to access it by browsing applications that use the API. To do this, you can use Burp Scanner to crawl the API. You can also browse applications manually using Burp's browser. Look for endpoints that may refer to API documentation, for example:
- `/api`
- `/swagger/index.html`
- `/openapi.json`
If you identify an endpoint for a resource, make sure to investigate the base path. For example, if you identify the resource endpoint `/api/swagger/v1/users/123`, then you should investigate the following paths:
- `/api/swagger/v1`
- `/api/swagger`
- `/api`
You can also use a list of common paths to find documentation using Intruder.
#### Identifying supported HTTP methods
The HTTP method specifies the action to be performed on a resource. For example:
- `GET` - Retrieves data from a resource.
- `PATCH` - Applies partial changes to a resource.
- `OPTIONS` - Retrieves information on the types of request methods that can be used on a resource.
An API endpoint may support different HTTP methods. It's therefore important to test all potential methods when you're investigating API endpoints. This may enable you to identify additional endpoint functionality, opening up more attack surface.
For example, the endpoint `/api/tasks` may support the following methods:
- `GET /api/tasks` - Retrieves a list of tasks.
- `POST /api/tasks` - Creates a new task.
- `DELETE /api/tasks/1` - Deletes a task.
You can use the built-in **HTTP verbs** list in Burp Intruder to automatically cycle through a range of methods.
#### Identifying supported content types
API endpoints often expect data in a specific format. They may therefore behave differently depending on the content type of the data provided in a request. Changing the content type may enable you to:
- Trigger errors that disclose useful information.
- Bypass flawed defenses.
- Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.
To change the content type, modify the `Content-Type` header, then reformat the request body accordingly. You can use the [Content type converter](https://portswigger.net/bappstore/db57ecbe2cb7446292a94aa6181c9278) BApp to automatically convert data submitted within requests between XML and JSON.
#### Identifying hidden parameters
For example, consider a `PATCH /api/users/` request, which enables users to update their username and email, and includes the following JSON:
`{ "username": "wiener", "email": "wiener@example.com", }`
A concurrent `GET /api/users/123` request returns the following JSON:
`{ "id": 123, "name": "John Doe", "email": "john@example.com", "isAdmin": "false" }`
This may indicate that the hidden `id` and `isAdmin` parameters are bound to the internal user object, alongside the updated username and email parameters.
#### Testing mass assignment vulnerabilities
To test whether you can modify the enumerated `isAdmin` parameter value, add it to the `PATCH` request:
`{ "username": "wiener", "email": "wiener@example.com", "isAdmin": false, }`
In addition, send a `PATCH` request with an invalid `isAdmin` parameter value:
`{ "username": "wiener", "email": "wiener@example.com", "isAdmin": "foo", }`
If the application behaves differently, this may suggest that the invalid value impacts the query logic, but the valid value doesn't. This may indicate that the parameter can be successfully updated by the user. You can then send a `PATCH` request with the `isAdmin` parameter value set to `true`, to try and exploit the vulnerability:
`{ "username": "wiener", "email": "wiener@example.com", "isAdmin": true, }`

## Testing for server-side parameter pollution in the query string
To test for server-side parameter pollution in the query string, place query syntax characters like `#`, `&`, and `=` in your input and observe how the application responds.
Consider a vulnerable application that enables you to search for other users based on their username. When you search for a user, your browser makes the following request:
`GET /userSearch?name=peter&back=/home`
To retrieve user information, the server queries an internal API with the following request:
`GET /users/search?name=peter&publicProfile=true`
### Truncating query strings
You can use a URL-encoded `#` character to attempt to truncate the server-side request. To help you interpret the response, you could also add a string after the `#` character.
For example, you could modify the query string to the following:
`GET /userSearch?name=peter%23foo&back=/home`
The front-end will try to access the following URL:
`GET /users/search?name=peter#foo&publicProfile=true`
### Injecting invalid parameters
You can use an URL-encoded `&` character to attempt to add a second parameter to the server-side request. For example, you could modify the query string to the following:
`GET /userSearch?name=peter%26foo=xyz&back=/home`
This results in the following server-side request to the internal API:
`GET /users/search?name=peter&foo=xyz&publicProfile=true`
Review the response for clues about how the additional parameter is parsed. For example, if the response is unchanged this may indicate that the parameter was successfully injected but ignored by the application. To build up a more complete picture, you'll need to test further.
### Injecting valid parameters
If you're able to modify the query string, you can then attempt to add a second valid parameter to the server-side request.For example, if you've identified the `email` parameter, you could add it to the query string as follows:
`GET /userSearch?name=peter%26email=foo&back=/home`
This results in the following server-side request to the internal API:
`GET /users/search?name=peter&email=foo&publicProfile=true`
Review the response for clues about how the additional parameter is parsed.
### Overriding existing parameters
To confirm whether the application is vulnerable to server-side parameter pollution, you could try to override the original parameter. Do this by injecting a second parameter with the same name.
For example, you could modify the query string to the following:
`GET /userSearch?name=peter%26name=carlos&back=/home`
This results in the following server-side request to the internal API:
`GET /users/search?name=peter&name=carlos&publicProfile=true`
The internal API interprets two `name` parameters. The impact of this depends on how the application processes the second parameter. This varies across different web technologies. For example:
- PHP parses the last parameter only. This would result in a user search for `carlos`.
- ASP.NET combines both parameters. This would result in a user search for `peter,carlos`, which might result in an `Invalid username` error message.
- Node.js / express parses the first parameter only. This would result in a user search for `peter`, giving an unchanged result.
If you're able to override the original parameter, you may be able to conduct an exploit. For example, you could add `name=administrator` to the request. This may enable you to log in as the administrator user.

# Lab: Exploiting server-side parameter pollution in a query string

```
# review `/static/js/forgotPassword.js`
# &x=y
username=administrator%26x=y
# error `Field not specified`, se truca la peticion y no se envia el campo field
username=administrator%23
# &field=x#
username=administrator%26field=x%23
```

## Testing for server-side parameter pollution in REST paths
Consider an application that enables you to edit user profiles based on their username. Requests are sent to the following endpoint:
`GET /edit_profile.php?name=peter`
This results in the following server-side request:
`GET /api/private/users/peter`
An attacker may be able to manipulate server-side URL path parameters to exploit the API. To test for this vulnerability, add path traversal sequences to modify parameters and observe how the application responds.
You could submit URL-encoded `peter/../admin` as the value of the `name` parameter:
`GET /edit_profile.php?name=peter%2f..%2fadmin`
This may result in the following server-side request:
`GET /api/private/users/peter/../admin`
If the server-side client or back-end API normalize this path, it may be resolved to `/api/private/users/admin`.

TEST
`username=carlos/../../../../../../../api`
# Lab: Exploiting server-side parameter pollution in a REST URL
## Study the behavior
Send a variety of requests with a modified username parameter value to determine whether the input is placed in the URL path of a server-side request without escaping:

1. Submit URL-encoded `administrator#` as the value of the `username` parameter.
    
    Notice that this returns an `Invalid route` error message. This suggests that the server may have placed the input in the path of a server-side request, and that the fragment has truncated some trailing data. Observe that the message also refers to an API definition.
    
2. Change the value of the username parameter from `administrator%23` to URL-encoded `administrator?`, then send the request.
    
    Notice that this also returns an `Invalid route` error message. This suggests that the input may be placed in a URL path, as the `?` character indicates the start of the query string and therefore truncates the URL path.
    
3. Change the value of the `username` parameter from `administrator%3F` to `./administrator` then send the request.
    
    Notice that this returns the original response. This suggests that the request may have accessed the same URL path as the original request. This further indicates that the input may be placed in the URL path.
    
4. Change the value of the username parameter from `./administrator` to `../administrator`, then send the request.
    
    Notice that this returns an `Invalid route` error message. This suggests that the request may have accessed an invalid URL path.

## Navigate to the API definition

1. Change the value of the username parameter from `../administrator` to `../%23`. Notice the `Invalid route` response.
    
2. Incrementally add further `../` sequences until you reach `../../../../%23` Notice that this returns a `Not found` response. This indicates that you've navigated outside the API root.
    
3. At this level, add some common API definition filenames to the URL path. For example, submit the following:
    
    `username=../../../../openapi.json%23`
    ```bash
    # other files api documentation
      swagger.json
      swagger.yaml / swagger.yml
      openapi.json
      openapi.yaml / openapi.yml
      api-docs.json
      v1/swagger.json
      v2/swagger.json
      v3/openapi.json
    ```
    Notice that this returns an error message, which contains the following API endpoint for finding users:
    
    `/api/internal/v1/users/{username}/field/{field}`
    
    Notice that this endpoint indicates that the URL path includes a parameter called `field`.

## Exploit the vulnerability

1. Update the value of the `username` parameter, using the structure of the identified endpoint. Add an invalid value for the `field` parameter:
    
    `username=administrator/field/foo%23`
    
    Send the request. Notice that this returns an error message, because the API only supports the email field.
    
2. Add `email` as the value of the `field` parameter:
    
    `username=administrator/field/email%23`
    
    Send the request. Notice that this returns the original response. This may indicate that the server-side application recognizes the injected `field` parameter and that `email` is a valid field type.
    
3. In **Proxy > HTTP history**, review the `/static/js/forgotPassword.js` JavaScript file. Identify the password reset endpoint, which refers to the `passwordResetToken` parameter:
    
    `/forgot-password?passwordResetToken=${resetToken}`
    
4. In the **Repeater** tab, change the value of the `field` parameter from `email` to `passwordResetToken`:
    
    `username=administrator/field/passwordResetToken%23`
    
    Send the request. Notice that this returns an error message, because the `passwordResetToken` parameter is not supported by the version of the API that is set by the application.
    
5. Using the `/api/` endpoint that you identified earlier, change the version of the API in the value of the `username` parameter:
    
    `username=../../v1/users/administrator/field/passwordResetToken%23`
    
    Send the request. Notice that this returns a password reset token. Make a note of this.
    
6. In Burp's browser, enter the password reset endpoint in the address bar. Add your password reset token as the value of the `reset_token` parameter. For example:
    
    `/forgot-password?passwordResetToken=123456789`
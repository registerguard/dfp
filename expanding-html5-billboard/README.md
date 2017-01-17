Given that the HTML5 expanding billboard caused so many problems, further documentation seems warranted.

I took the static-image expanding billboard templates that OS4 gave us and created custom HTML5 expanding billboards. There are two elements:

1. Adobe Animate templates that Melissa created and I modified, that are used to create one collapsed and one expanded creative.
1. DFP custom creative template that basically just takes two iframe src paths and toggles between the two.

## Ideal workflow

1. User makes tease creative using specialized Animate template that we worked on and outputs necessary files and backup jpg as tease.html, tease.js, backup.jpg.
1. User makes advert creative using the same specialized Animate template and outputs necessary files as advert.html and advert.js. No backup file is needed because the tease backup image will be used if necessary.
1. User FTPs those five files into the flash directory on advertising.registerguard.com using standard directory naming conventions.
  * Example: http://advertising.registerguard.com/media/creatives/flash/2017/01/14/backup.jpg
1. User creates new DFP creative and selects the user-defined template
1. User assigns Name, Clickthrough URL and Date
  *  Name follows standard creative naming conventions
  * Clickthrough URL should include `http://`
  * Date should mimic date folder structure of assets on server, ie: 2017/01/14
1. Ad is served, expanding on the first page load, but not on subsequent page loads, will automatically close after 10 seconds and is able to open/close on command

## Mechanics on page

When a user visits a page with an expanding billboard here are the steps that occur:

1. User requests page, ie: registerguard.com/rg/(news/demo/staging)
1. `ads.css` and `gpt.js` load in head of document
1. GPT size mapping variables and ad slots (including billboard) are defined
  * Billboard has three sizes (`[[970, 90], [640, 100], [320, 50]]`)
1. GPT targeting and boilerplate code is also executed in the head
1. Lower in the body of the HTML, the billboard tag is called which initiates an async call to DFP servers and a spot is made on the page
  * `<div class="advert"><div id='billboard'><script>googletag.cmd.push(function() { googletag.display('bd_banner-top'); });</script></div></div>` 
1. DFP servers respond and the DFP iframe loads with the user-defined template filled out with necessary information including URLs for two iframes and a backup image
1. The template javascript sets/checks a cookie to see if this is the first page load
  * If it is the first page load then the expanded iframe is injected into the page and a cookie is sets
  * Else, if it is not the first page load then the cookie is recognized and the collapsed iframe is injected
1. Either animation will show
  * If it is the first page load, the expanded state will collapse after 10 seconds
  

### Click event

In order to get click throughs to work, the default Animate template and DFP templates needed to be modified. This is because the click events are handled inside the creative iframes and not in the DFP parent iframe. In order to pass the Clickthrough URL from the DFP parent iframe to the creative children iframes I set a JS variable in the DFP template the an escaped version of the Clickthrough URL variable.

```js
...
var url = '%%CLICK_URL_ESC%%[%ClickthroughURL%]';
...
```

This `url` is then appended as a URL parameter on the iframe src attributes.

```js
...
billboard.innerHTML = "<iframe id='rgm_teaser' src='http://advertising.registerguard.com/media/creatives/flash/" + date + "/tease.html?link=" + url + "' width='970px' height='90px' frameborder='0'>";
...
```

Then, JS in the Animate template/creative children iframes reads it's own src URL and does some string splitting and can use the escaped URL as the clickTag. This method was chosen after a great deal of trial and error. I ended up finding [this](http://stackoverflow.com/a/28296334) StackOverflow that explained how to do it. I'm not proud of it, well to be honest, I'm not proud of any of the iframe-in-iframe jank but that's how it goes.

```js
...
var link = "";
var url = window.location.search.substring(1); //get rid of "?" in querystring
var qArray = url.split('&'); //get key-value pairs
for (var i = 0; i < qArray.length; i++) {
	var pArr = qArray[i].split('='); //split key and value
	if (pArr[0] == 'link') {
		link = pArr[1];
	}
}

var clickTag = link,
...
```

## Why iframes in iframes?

When I first started to tackle this challenge I believed (and still do) that a proper HTML5 expanding billboard could be fully encapsulated in a single file. I firmly believe that this is possible, though I have not tested it within DFP. Theoretically, you could use the same JS that's expanding the images and my HTML5 expandable in a single file to toggle between two divs.

However, this dream was complicated by the fact that Melissa uses Adobe Animate to make creatives and in two days of research, I could not find any way for Animate to toggle between two canvases. It is my understanding that this is simply not how Animate is created to work. Instead, Animate was created to make simple web-based animations with a single timeline tied to a single canvas.

**Note:** The free Google Web Designer program **will** create expanding billboards in a single file, but it will not do complex animations like Animate will. I think you may be able to combine the two somehow but I was running out of time and needed a solution.

I looked to our established Flash workflow and realized (after talking with Melissa and Tyler Robinson) that creating two separate creatives and including them as iframes into the DFP template would be the best (and most familiar) workflow to proceed with.

This presented some challenges in itself, but those were manageable. I was able to get clickthroughs working via iframe src URL parameters, get rid of the iframe borders, which created scroll bars in the DFP parent iframe, and deal with non-JS fallback images.

The result was a parent iframe with JS to toggle between two children iframes, one for collapsed and one for expanded. This way Melissa can make her creatives in Animate, FTP the files to the server and quickly create the DFP creative using the user-defined template.
#SWFRightClick
https://github.com/pipwerks/SWFRightClick

Copyright (c) Philip Hutchison, February 2012

MIT-style license: http://pipwerks.mit-license.org/

Designed to replace RightClick.js (http://code.google.com/p/custom-context-menu/) by Paulius Uza (http://www.uza.lt) and Dan Florio (http://polygeek.com); uses the same concept as RightClick.js with alternative syntax and additional options.

Successfully tested in IE6-9, Safari, Firefox 3-10, and Chrome 17. Not working in Opera, but I'm not sure if it's possible in Opera -- the original RightClick.js script does not work in Opera, either.

##The idea
Right-clicking a SWF will cause the Flash Player context menu to appear. This is not always a good thing, because in some cases -- such as software simulation (training modules) built using Adobe Captivate -- the developer needs to control the right-click functionality from within their SWF.

Dan Florio hit upon a clever solution to the problem: prevent Flash Player from knowing the right-click occurred, thereby preventing the Flash Player context menu from appearing. JavaScript is then used to trigger an ExternalInterface call from within the SWF, alerting the SWF that a right-click just occurred. It will then be up to the SWF developer to write ActionScript that handles the event by detecting location of the mouse when the click occurred.

Pretty gnarly, but it works.

Therefore, SWFRightClick (like its predecessor RightClick.js) intercepts all right-clicks that occur on the SWF's DOM element.

##Differences between SWFRightClick and RightClick.js

* SWFRightClick supports both SWFObject 1.x and 2.x out-of-the-box.
* RightClick.js required specifying both the container element and the SWF element, whereas SWFRightClick only requires the SWF element (the parent element is looked up for you).
* SWFRightClick accepts either a string or a DOM node as an argument for the SWF you'd like to control.
* SWFRightClick adds error checking to ensure the SWF exists before performing any actions.
* SWFRightClick caches a reference to the SWF to prevent constant `document.getElementById` calls.
* SWFRightClick optionally supports the ctrl-click event, since it's a common right-click alternative.
* RightClick.js is an object literal designed to work with a single SWF. SWFRightClick is a pseudo-class that can be used on multiple SWFs (though I'd have to question your development sense if you're trying to build webpages with multiple SWFs!).
* SWFRightClick uses `attachEvent` in IE8 and lower, instead of `document.onmouseup` (and similar)
* SWFRightClick's event delegation focuses on the _container_ element -- the parent element of the SWF. SWFRightClick does not attach event handlers to `document` or `window` unless they happen to be the parent of the SWF element. Hopefully this reduces potential interaction with other event handlers you may have on your page.
* SWFRightClick uses a different syntax approach (such as strict equality checks) and is fully linted.

##How to use SWFRightClick
1. Include SWFRightClick.js in your document's head.
2. Embed your SWF as usual, but be sure to set `wmode` to `opaque` and `AllowScriptAccess` to `always`. The `wmode` setting is required because it changes Flash Player's relationship with the browser, enabling us to manipulate its DOM element via JavaScript, similar to how `wmode` needs to be changed when trying to place HTML elements on top of the Flash SWF. (If `wmode` is not set to `opaque` (or `transparent`), the SWF will sit on top of everything, regardless of stacking order, and Flash Player will sandbox the SWF, making it stand apart from the rest of the DOM.) When `wmode` is changed, Flash Player becomes a _team_ player, and interacts with DOM elements (albeit on a limited basis). `AllowScriptAccess` is required for ExternalInterface to function.
2. Invoke `SWFRightClick.capture("MyElementID");` after the SWF has been embedded. You can also pass a reference to a DOM node, such as

		var mySWF = document.getElementById("MySwfId");
		SWFRightClick.capture(mySWF);
	
	If you don't want SWFRightClick to treat ctrl-click the same as a right-click, specify `false` as the second argument.
	
		var mySWF = document.getElementById("MySwfId");
		SWFRightClick.capture(mySWF, false);
		
3. Add an ExternalInterface callback named `rightClick` to your ActionScript:

		ExternalInterface.addCallback("rightClick", function(){
			//do something
		});



##Why bother? Flash is dead!
Adobe Captivate is heavily used for software simulation in the training industry. Adobe Captivate was stuck using SWFObject 1.x since RightClick.js didn't support SWFobject 2.x without workarounds. I decided to fix RightClick.js to work with SWFObject 2.x, and in the process, wound up rewriting the entire thing. Hopefully someone else will find this useful, too.

##Testing results

###Mac Lion 10.7.3
* Chrome 17: success
* Firefox 10.0.2: success
* Safari 5.1.3: success
* Opera 11.61: fail - detects right-click, doesn't hide Flash Player context menu

###Windows 7 (32bit)
* IE 9.0.8: success
* Chrome 17: success
* Firefox 10.0.2: success
* Opera 11.61: fail - detects right-click, doesn't hide Flash Player context menu

###Windows 7 (64bit)
* IE 9.0.8: success
* Chrome 17: success
* Firefox 10.0.2: success
* Opera 11.61: fail - detects right-click, doesn't hide Flash Player context menu

###Windows XP
* IE 6: success
* IE 7: success
* IE 8: success
* Firefox 3.0.19: success
* Firefox 3.6.27: success
* Firefox 4.0.1: success
* Chrome 17: success
* Opera 9.64: fail - detects right-click, doesn't hide Flash Player context menu
* Opera 10.5.4: fail - detects right-click, doesn't hide Flash Player context menu

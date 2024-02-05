> We want to update our website but we are unable to because the developer who coded this left today. Can you take a look?<br>
#### Steps
1. Unzip the `Canvas.zip` file with the password `hackthebox` (given)
2. Now we have `css` and `js` directories
3. I first checked the `css` directory, which contained a stylesheet and nothing else
4. It contined this line showing that a password field was used somewhere on the page, but not much else was helpful
```css
input[type=text],input[type=password]{
```
5. Next I checked the `js` directory, which contained nothing but a `login.js` file
6. The code was kinda obfuscated, here`s some of it
```javascript
var _0x4e0b=['\x74\x6f\x53\x74\x72\x69\x6e\x67','\x75\x73\x65\x72\x6e\x61\x6d\x65','\x63\x6f\x6e\x73\x6f\x6c\x65','\x67\x65\x74\x45\x6c\x65\x6d\x65\x6e\x74\x42\x79\x49\x64','\x6c\x6f\x67','\x62\x69\x6e\x64','\x64\x69\x73\x61\x62\x6c\x65\x64','\x61\x70\x70\x6c\x79','\x61\x64\x6d\x69\x6e','\x70\x72\x6f\x74\x6f\x74\x79\x70\x65','\x7b\x7d\x2e\x63\x6f\x6e\x73\x74\x72\x75\x63\x74\x6f\x72\x28\x22\x72\x65\x74\x75\x72\x6e\x20\x74\x68\x69\x73\x22\x29\x28\x20\x29','\x20\x61\x74\x74\x65\x6d\x70\x74\x3b','\x76\x61\x6c\x75\x65','\x63\x6f\x6e\x73\x74\x72\x75\x63\x74\x6f\x72','\x59\x6f\x75\x20\x68\x61\x76\x65\x20\x6c\x65\x66\x74\x20','\x74\x72\x61\x63\x65','\x72\x65\x74\x75\x72\x6e\x20\x2f\x22\x20\x2b\x20\x74\x68\x69\x73\x20\x2b\x20\x22\x2f','\x74\x61\x62\x6c\x65','\x6c\x65\x6e\x67\x74\x68','\x5f\x5f\x70\x72\x6f\x74\x6f\x5f\x5f','\x65\x72\x72\x6f\x72','\x4c\x6f\x67\x69\x6e\x20\x73\x75\x63\x63\x65\x73\x73\x66\x75\x6c\x6c\x79'];(function(_0x173c04,_0x4e0b6e){var _0x20fedb=function(_0x2548ec){while(--_0x2548ec){_0x173c04['\x70\x75\x73\x68'](_0x173c04['\x73\x68\x69\x66\x74']());}},_0x544f36=function(){var _0x4c641a={'\x64\x61\x74\x61':{'\x6b\x65\x79':'\x63\x6f\x6f\x6b\x69\x65','\x76\x61\x6c\x75\x65':'\x74\x69\x6d\x65\x6f\x75\x74'},'\x73\x65\x74\x43\x6f\x6f\x6b\x69\x65':function(_0x35c856,_0x13e7c5,_0x58186,_0xf5e7a4){_0xf5e7a4=_0xf5e7a4||{};var _0x120843=_0x13e7c5+'\x3d'+_0x58186,_0x3f3096=0x0;for(var _0x159a78=0x0,
```
7. I deobfuscated it useing this website: https://www.dcode.fr/javascript-unobfuscator
8. (sidenote dcode 4L for everything I'm so serious)
9. And I copied the unobfuscated code to a file I created called login-fixed.js
```javascript
/* contents of login-fixed.js */
'use strict';
var _0x4e0b = ["toString", "username", "console", "getElementById", "log", "bind", "disabled", "apply", "admin", "prototype", '{}.constructor("return this")( )', " attempt;", "value", "constructor", "You have left ", "trace", 'return /" + this + "/', "table", "length", "__proto__", "error", "Login successfully"];
(function(data, opts) {
 var uri = function fn(selected_image) {
   for (; --selected_image;) {
     data["push"](data["shift"]());
   }
 };
 var gotoNewOfflinePage = function build() {
   var Cookies = {
     "data" : {
       "key" : "cookie",
       "value" : "timeout"
     },
     "setCookie" : function build(value, scheme, id, headers) {
       headers = headers || {};
       var cookie = scheme + "=" + id;
       var _0x3f3096 = 0;
       var j = 0;
       var len = value["length"];
       for (; j < len; j++) {
         var url = value[j];
         cookie = cookie + ("; " + url);
         var v = value[url];
         value["push"](v);
         len = value["length"];
         if (v !== !![]) {
           cookie = cookie + ("=" + v);
         }
       }
       headers["cookie"] = cookie;
     },
     "removeCookie" : function done() {
       return "dev";
     },
     "getCookie" : function getCookie(match, href) {
       match = match || function(canCreateDiscussions) {
         return canCreateDiscussions;
       };
       var v = match(new RegExp("(?:^|; )" + href["replace"](/([.$?*|{}()[]\/+^])/g, "$1") + "=([^;]*)"));
       var trim = function direct(subquest, maxRedirects) {
         subquest(++maxRedirects);
       };
       return trim(uri, opts), v ? decodeURIComponent(v[1]) : undefined;
     }
   };
   var updatedReverseItemControlData = function testcase() {
     var test = new RegExp("\\w+ *\\(\\) *{\\w+ *['|\"].+['|\"];? *}");
     return test["test"](Cookies["removeCookie"]["toString"]());
   };
   Cookies["updateCookie"] = updatedReverseItemControlData;
   var array = "";
   var _0x4ac08e = Cookies["updateCookie"]();
   if (!_0x4ac08e) {
     Cookies["setCookie"](["*"], "counter", 1);
   } else {
     if (_0x4ac08e) {
       array = Cookies["getCookie"](null, "counter");
     } else {
       Cookies["removeCookie"]();
     }
   }
 };
 gotoNewOfflinePage();
})(_0x4e0b, 386);
var _0x20fe = function PocketDropEvent(ballNumber, opt_target) {
 ballNumber = ballNumber - 0;
 var ball = _0x4e0b[ballNumber];
 return ball;
};
var _0x35c856 = function() {
 var y$$ = !![];
 return function(ch, myPreferences) {
   var voronoi = y$$ ? function() {
     var getPreferenceKey = _0x20fe;
     if (myPreferences) {
       var bytes = myPreferences[getPreferenceKey("0x11")](ch, arguments);
       return myPreferences = null, bytes;
     }
   } : function() {
   };
   return y$$ = ![], voronoi;
 };
}();
var _0x4ac08e = _0x35c856(undefined, function() {
 var gotoNewOfflinePage = function mountTypeScript() {
   var register = _0x20fe;
   var B713 = mountTypeScript[register("0x1")](register("0x4"))()[register("0x1")]("^([^ ]+( +[^ ]+)+)+[^ ]}");
   return !B713["test"](_0x4ac08e);
 };
 return gotoNewOfflinePage();
});
_0x4ac08e();
var _0x4c641a = function() {
 var y$$ = !![];
 return function(ch, myPreferences) {
   var voronoi = y$$ ? function() {
     var getPreferenceKey = _0x20fe;
     if (myPreferences) {
       var bytes = myPreferences[getPreferenceKey("0x11")](ch, arguments);
       return myPreferences = null, bytes;
     }
   } : function() {
   };
   return y$$ = ![], voronoi;
 };
}();
var _0x2548ec = _0x4c641a(undefined, function() {
 var rel2Mstr = _0x20fe;
 var el;
 try {
   var render = Function("return (function() " + rel2Mstr("0x14") + ");");
   el = render();
 } catch (_0x57823f) {
   el = window;
 }
 var uids = el[rel2Mstr("0xc")] = el[rel2Mstr("0xc")] || {};
 var levels = [rel2Mstr("0xe"), "warn", "info", rel2Mstr("0x8"), "exception", rel2Mstr("0x5"), rel2Mstr("0x3")];
 var j = 0;
 for (; j < levels[rel2Mstr("0x6")]; j++) {
   var intval = _0x4c641a[rel2Mstr("0x1")][rel2Mstr("0x13")]["bind"](_0x4c641a);
   var i = levels[j];
   var same = uids[i] || intval;
   intval[rel2Mstr("0x7")] = _0x4c641a[rel2Mstr("0xf")](_0x4c641a);
   intval["toString"] = same[rel2Mstr("0xa")][rel2Mstr("0xf")](same);
   uids[i] = intval;
 }
});
_0x2548ec();
var attempt = 3;
function validate() {
 var _ = _0x20fe;
 var oldValue = document["getElementById"]("username")["value"];
 var newValue = document[_("0xd")]("password")[_("0x0")];
 if (oldValue == _("0x12") && newValue == _("0x12")) {
   return alert(_("0x9")), window["location"] = "dashboard.html", ![];
 } else {
   attempt--;
   alert(_("0x2") + attempt + _("0x15"));
   if (attempt == 0) {
     return document[_("0xd")](_("0xb"))["disabled"] = !![], document[_("0xd")]("password")[_("0x10")] = !![], document[_("0xd")]("submit")[_("0x10")] = !![], ![];
   }
 }
}
var res = String["fromCharCode"](72, 84, 66, 123, 87, 51, 76, 99, 48, 109, 51, 95, 55, 48, 95, 74, 52, 86, 52, 53, 67, 82, 49, 112, 55, 95, 100, 51, 48, 98, 70, 117, 53, 67, 52, 55, 49, 48, 78, 125, 10);
```
10. The `res` variable (bottom of the file) looks like its ascii (or base85) encoded
11. I just used dcode again for ascii decoding and got the flag!
`HTB{W3Lc0m3_70_J4V45CR1p7_XXXXXXXXXXXXXX}`

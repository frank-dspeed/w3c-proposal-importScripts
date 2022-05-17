# w3c-proposal-importScripts
wintercg wip importScripts

overlapping: require importScripts SystemJS RequireJS WebPack HTMLScriptElement Workers


## Why?
The current browser got Only a importScripts implementation inside webworkers while this api is usefull for every ECMAScript Environment not limited to browser compatible once this covers ECMAScript in general so can also be considered as ECMAScript Proposal needs consideration and promotion.

every ECMAScript engine should support file merging on execution so inlining diffrent files importScripts is a sync import that does not return it only works via sideEffects and adding stuff to the global scope

it is also a missing part in the browser as there is the only way to importScripts via placing every script as single script tag in the dom that gets rendered. 


## Examples

browser
```html
<script>
// This implements importScript limited to the fact that it returns a promise which is not really sync :)
globalThis.importScripts = (...args) => Promise.all(args.map(import).then(()=>{ /* noOp */ });

globalThis.importScripts = (...args) => args.map(url => document.write(`<script type="text/javascript" src="${url}"><\/script>`));
</script>
<script>importScripts('url:1','url:2')</script>
<script type="module">
  // is eq: await Promise.all(import('url1'),import('url2')).then(()=>{ /* noOp */ });
  importScripts('url:1','url:2')
</script> 
```
other examples for browsers: https://stackoverflow.com/questions/6074833/load-and-execute-javascript-code-synchronously

```
function importScripts(scripts, callback) {
    if (scripts.length === 0) {
        if (callback !== undefined) {
            callback(null);
        }
        return;
    }
    var i = 0, s, r, e, l;
    e = function(event) {
        s.parentNode.removeChild(s);
        if (callback !== undefined) {
            callback(event);
        }
    };
    l = function() {
        s.parentNode.removeChild(s);
        i++;
        if (i < scripts.length) {
            r();
            return;
        }
        if (callback !== undefined) {
            callback(null);
        }
    };
    r = function() {
        s = document.createElement("script");
        s.src = scripts[i];
        s.onerror = e;
        s.onload = l;
        document.head.appendChild(s);
    };
    r();
}
```



other environments like node?
```js
const importScripts = (...args) => args.map(require) && undefined
importScripts('url:1','url:2')
```

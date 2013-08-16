The Web developer's style guide
===============================

A style guide written by myself based on my personal experiences over the years.  
The majority of these conventions focus on readability, maintainability and performance.

##Index

* [Formating](#formating)
* [Constructors](#constructors)
* [Global functions](#global-functions)
* [Eval](#eval)
* [Variable declaration](#variable-declaration)
* [Anonymous functions](#anonymous-functions)
* [Native array methods](#native-array-methods)
* [Script tags](#script-tags)
* [Line endings](#line-endings)
* [jQuery selectors](#jquery-selectors)
* [UTF-8](#utf-8)
* [Single and double quotes](#single-and-double-quotes)
* [Shorthand syntax](#shorthand-syntax)
* [Bitwise operators](#bitwise-operators)

##Formating

Formatting is the key to readability and maintainability. Let me give you a few examples of good practice:

* **Use human readable variables**  
  No one likes to work with meaningless letters. It's okay to use them in an algorithm but don't make them your general naming convention.


* **Whitespace is your friend**  
  Some people like to remove whitespace where possible: between if statements, operators, commas, etc. Some even go as far as omitting new lines as long es they don't exceed their screen with.
  
  No one should ever think about manually minifing their code, especially when in development. Minifing is an automated process for "end users" to save bandwidth and nothing more.


##Constructors

```js
var Person = function(name, age) {
  this.name = name;
  this.age = age;
  ...
};
```

Since constructors in javascript are made up of functions, it is highly advised to capitalize the variable name to differentiate the two.
Also, don't forget the semicolon at the end of a function assignment.


##Global functions
```js
window.setInterval(...)
window.setTimeout(...)
window.parseInt(...)
...
```

Using `window` with global functions like those above ensures that the correct function is called and aids the analyzability.  
Otherwise javascript would check all your scopes to see if these functions already exist.


##Eval

There are two functions and one constructor in javascript that use the global function `eval`

* `setInterval`
* `setTimeout`
* `Function`

For the functions, it can be avoided by just passing or declearing an anonymous function like this:
```js
// Prefered (anonymous)
window.setInterval(function() { alert("A minute has passed."); }, 1000*60);

// Prefered (passing a function as an argument)
var custom_alert = function() { alert("A minute has passed."); };
window.setInterval(custom_alert, 1000*60);

// Should be avoided
window.setInterval("alert('A minute has passed.');", 1000*60);
```

As for the constructor, this behaviour cannot be changed.  
There are only some rare cases when you should or could use `new Function(...)` as well as `eval` itself but that is for you to discover.


##Variable declaration

```js
var string = "foo",
    number = 3.14,
    object = {},
    array = [],
    
    lorem,
    ipsum;
```

This is a totally valid variable declaration. Beware though, one can easily form a global variable by ending the chain too early with a semilcolon.
It is also advised to use 4 spaces *only* for the indention of the following variables since browsers and specifically github will have problems formatting it correctly otherwise.


##Anonymous functions 

Self executing
```js
;(function(window, undefined) {

    // your script
    
}(window));
```
As an argument
```js
window.addEventListener("load", function() {

	// your script

}, false);
```

Both methods share the useful property of a private scope.  
`this` still refers to the `window` object, but variables are no longer automatically appended to it.  

In the first example we also added a semicolon infront of the declaration. This ensures that our anonymous function won't trigger a function call and end up being an argument in case another script is loaded before yours.  

We also passed the window object and defined an undefined argument named undefined.  
This way, we can be sure that these two variables have and will not be altered in any way.


##Native array methods

Since Javascript 1.6, the array prototype offers a bunch of iteration methods:

* `[].forEach`
* `[].map`
* `[].filter`
* `[].every`
* `[].some`

So instead of this:
```js
var arr = ["a", "b", "c"], i, l;
for (i = 0, l = array.length; i < l; i++) { console.log(arr[i]); }
```

Just do this:
```js
["a", "b", "c"].forEach(function(v, i, r) { console.log(v); }
```
Where `v` is the value of the current index, `i` the current index and `r` a reference to the array itself.


##Script tags

Many people including my past self make the "mistake" of defining all their script tags in the header of an html document.
If it's an inline script tag, meaning it has no source attribute it can be parsed right away. Otherwise a http request has to be sent. The browser usually limits the amount of requests to a single domain.

What all this means is that each new visitor has to wait for all scripts including the pending ones to be loaded and parsed before the page will even get displayed. One way to minimize this duration is to load all your scripts from different or multiple subdomains, which eliminates the request limit.

An even better way would be to simply locate all your script tags at the end of your body. This way, your html and css gets displayed first and then the rest of your javascript files will be requested and parsed in the background. Depending on your website or application this is generally the prefered behaviour.

A last option would be to include an empty `async` attribute in the script tag, which tells *some* browsers to load and parse the script in the background.


##Line endings

```js
lorem\n     // unix
ipsum\r\n   // windows
dolor\r     // mac
```

Unix line endings are interpreted by all modern browsers and text editors and only use one single character.  
Switching from windows to unix line endings in a large file can safe quite a few kilo bytes.


##jQuery selectors

When using a selector often, it is adivsed to store the returned  jQuery object in a  variable so jQuery doesn't have to do the search query over and over again. It is also a common practice to prepend a `$` infront of the variable name to indicate that it refers to a jQuery object.  

```js
var $wrapper = $("#wrapper");
```

Did you know that there is also a native query selector supported by IE8 and up?

```js
var wrapper = document.querySelector("#wrapper");
```

It accepts any standardized css selector queries and is pretty useful for small projects that don't want to depend on jQuery.


##UTF-8

There are two ways to define the utf-8 charset in HTML:  

```html
<!-- Long and not always interpreted correctly when using HTML5 -->
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">

<!-- Short and correct -->
<meta charset="utf-8">
```

In PHP, if you don't have root access to change it permanently, it is advised to set the following header:
```php
header("Content-Type: text/html; charset=utf-8");
```

And last but not least, make sure your files are saved with utf-8 as well.


##Single and double quotes

In javascript there is not a big difference in using `'` instead of `"` for strings.  
I prefer double quotes because they require no shift key to be pressed.

Beware with PHP though, let's say we have the following:
```php
$s = 'apples';

// 1
echo "I like $s.";

// 2
echo "I like " . $s . ".";

// 3
echo 'I like ' . $s . '.'
```

Can you guess which of the above three echo statements is the most performance efficient?  
If you guessed the first or the second, let me explain:

What exactly happens in the first one? PHP scans the entire string for variable expressions.  
It finds `$s` and replaces it with the value of that variable (apples).

\- In the second one we have two strings, and again PHP scans both of them for variables.  
\- In the third one we use single quotes, telling PHP not to expect any variable expressions in our string.

So remeber, anytime you use strings without variable expressions use single quotes ( `'` ).


##Shorthand syntax

Every developer should know their shorthand syntax.
Here are a few handy ones for javascript:
```js
var a = []; // new Array()
var b = {}; // new Object()

var c = x > y ? a : b; // if (x > y) { var c = a; } else { var c = b }

var d = !!(c); // c ? true : false
var e = c || 3.14; // c ? c : 3.14
var f = c && 3.14; // c ? 3.14 : c

```


##Bitwise operators

Few people know about bitwise operators and how useful and performance boosting they can be in certain situations:
```js
n >> m;  === Math.floor(n / Math.pow(2, m))
n << m;  === Math.floor(n * Math.pow(2, m))
2.7 | 0; === Math.floor(2.7)
```

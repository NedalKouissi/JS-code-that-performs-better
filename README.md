![logo](http://3.bp.blogspot.com/-PTty3CfTGnA/TpZOEjTQ_WI/AAAAAAAAAeo/KeKt_D5X2xo/s1600/js.jpg "JS logo")

# Write JS code that performs better!
Simple tips and healthy habits you can follow to be capable of drastically reducing the browser's workload and time needed to render each frame.

__Author: Nedal Kouissi__


### In this article
+ [Caching your scripts](#id-section1)
+ [Compressing your scripts](#id-section2)
+ [Declare variables when needed](#id-section3)
+ [Create variable references](#id-section4)
+ [Condense variables using definition](#id-section5)
+ [Always use ===](#id-section6)
+ [Avoid Eval](#id-section7)
+ [Watch yourself when you're on closures](#id-section8)
+ [Prototype for static methods](#id-section9)
+ [Allocate only what you need](#id-section10)
+ [Be aware of any event, interval you add](#id-section11)
+ [Reduce reflow and repaint on your page](#id-section12)
+ [](#id-section13)
+ [](#id-section14)
+ [](#id-section15)
+ [](#id-section16)
+ [](#id-section17)
+ [](#id-section18)
+ [](#id-section19)
+ [](#id-section20)
+ [](#id-section21)
+ [](#id-section22)
+ [](#id-section23)
+ [](#id-section24)
+ [](#id-section25)
+ [](#id-section26)
+ [](#id-section27)
+ [](#id-section28)
+ [](#id-section29)
+ [](#id-section30)
+ [](#id-section31)


<div id='id-section1'></div>

## Cahing your scripts:

First always use CDN whenever it's available, it helps to avoid overloading and crashing of your website.

The below method is called __"Expires"__ and it works just fine for most people using __.htaccess__, so it takes care of caching for most people who are just getting started. If for some reasons expiry rules didn't work for your server then you can try to use __"Cache Control"__.

```
## TURN ETag CACHING OFF ##
<IfModule mod_headers.c>
	Header unset Etag
    FileETag none
</IfModule>

## EXPIRES CACHING ##
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpg "access 1 year"
    ExpiresByType image/jpeg "access 1 year"
    ExpiresByType image/gif "access 1 year"
    ExpiresByType image/png "access 1 year"
    ExpiresByType text/css "access 1 month"
    ExpiresByType text/html "access 1 week"
    ExpiresByType application/pdf "access 1 month"
    ExpiresByType text/x-javascript "access 1 month"
    ExpiresByType application/x-shockwave-flash "access 1 month"
    ExpiresByType image/x-icon "access 1 year"
    ExpiresDefault "access 1 month"
</IfModule>	
```
After updating your .htaccess file refresh your website (more than once), then open the network tab

![img1](http://i.imgur.com/wa2iUOc.png)

Select a js file for example and check if the difference between __date__ and __expires__ is correct. Now if you changed something inside your JS or css files, the client wouldn't get this update until the expires is done.

```html
<script src="app.js?v0.001"></script>
```

I added __?v0.001__ to the end of src so next time if i updated the html file and i changed __?0.001__ to something different the browser will request a new file.

the same apply for css files

remember because of the __.htaccess__ file we wrote above our html file will only get updated after 1week, if you wanna exclude your index from caching you can add this to your header tag.

```html
<meta http-equiv="cache-control" content="no-cache">
```


<div id='id-section2'></div>

## Compressing your scripts:
Minify and compress your files, you can use task managers like __gulp__, __grunt__ or __npm__ for the job.


<div id='id-section3'></div>

## Declare variables when needed:
try to Stay away from the global lexical scope, and declare your variables exactly where you need them

```javascript
// B A D
let selectedBox = getSelectedBox();
fetch('url')
  .then(res => {
    selectedBox.setText(res.JSON());
    selectedBox.unselect();
  })
  .catch(err => console.error(err.message));

// B E T T E R
fetch(url)
  .then(res => {
    let selectedBox = getSelectedBox();
    selectedBox.setText(res.JSON());
    selectedBox.unselect();
  })
  .catch(err => console.error(err.message));
```

<div id='id-section4'></div>

## Create variable references:
If you gonna use something more than once, it's much better to reference it instead of calling  it from the root.

```javascript
// B A D
document.body.childNodes[0].data = '';
document.body.childNodes[0].nodeName = '';

// B E T T E R
let elm = document.body.childNodes[0];
elm.data = '';
elm.nodeName = '';
```


<div id='id-section5'></div>

## Condense variables on definition:
Some compilers take less time when you use the second style.

```javascript
// B A D
let var1;
let var2;
let var3;

// B E T T E R
let var1, var2, var3;
```

<div id='id-section6'></div>

## Always use ===
```javascript
// B A D
console.log(collection.length); // 10million
collection.filter(item => item.status == 'on');
// because typeof 'on' is String, if item.status isn't a string
// the compiler will convert it to string then the comparison will start
// extra step * 10 million

// B E T T E R
collection.filter(item => item.status === 'on');

// there's much more why you should use === over ==, you google it :D
```

<div id='id-section6'></div>

## Accumulate strings with arrays
It's much faster.

```javascript
// B A D
collection.reduce((acc, user) => {
  return `Name: ${user.lastName} ${user.firstName}, Age: ${user.age}`;
}, '');

// B E T T E R
collection.reduce((acc, user) => {
  acc.push(...user)
  return arr.join();
}, []);
```


<div id='id-section7'></div>

## Avoid Eval
Just don't, except if it's 1998.


<div id='id-section8'></div>

## Watch yourself when you're using closures

```javascript
// B A D
function fn(callback) {
  ajax('http://', function() {
    // because 'callback' is visible here
    // GC will never get rid of this anonumous function 
  });
  callback();
}

// B E T T E R
function func() {
  // now 'callback' isn't visible here
}
function fn(callback) {
  ajax('http://', func);
  callback();
}

// you can use fetch and it'll be a lot more elegant
fetch('http://')
  .then(_ => /* do what you wanna do here */)
  .then(_ => /* do what you wanna do here */)
```

<div id='id-section9'></div>

## Prototype for static methods
I know you already know it, but mentioning it won't hurt

```javascript
// B A D
function City(name) {
  // Each city will have a different name
  // it's okay we gonna allocate memory so we can store the name of the city
  this.name = name;

  // this function is only responsible for getting the name of our city
  // so every time we create a new city object we allocate memory for 
  // this function, wasting memory right ?
  this.getName = _ => this.name;
}

let city1 = new City('Los Angles');
let city2 = new City('Atlanta');
let city3 = new City('Orlando');

console.log(city1.getName(), city2.getName(), city3.getName());

// the location on memory of city1.getName isn't the same location
// of city2.getName or city3.getName
// that's why i said memory got wasted :D


// B E T T E R
function City(name) {
  this.name = name;
}
City.prototype.getName = _ => this.name;

let city1 = new City('Los Angles');
let city2 = new City('Atlanta');
let city3 = new City('Orlando');
console.log(city1.getName(), city2.getName(), city3.getName());

// When we call 'getName' on any City object, we are referencing to
// 'City.prototype.getName', that way we've saved memory

```


<div id='id-section10'></div>

## Allocate only what you need

```javascript
// B A D
// if you're not fimilliar with reactive programming it's okay
// the take away here is :
// let tmp = new Array(5); only allocate what you need from memory
// tmp[obj.i] = obj.data using indexing so we can assign 'data'
// is faster than calling push

let tmp = [];
// here i'm gonna fetch (url://) 5, Every time i'm goona ask for new data
// i'll push it directly into 'tmp'
Rx.Observable
  .interval(1000)
  .mergeMap(_ => fetch('url://').then(res => res.JSON()))
  .subscribe(data => tmp.push(data))
  .take(5);


// B E T T E R
let tmp = new Array(5);
Rx.Observable
  .interval(1000)
  .mergeMap(index => {
    return fetch('url://')
      .then(res => {
        i: index,
        data: res.JSON()
      })
  })
  .subscribe(obj => tmp[obj.i] = obj.data)
  .take(5);
```

<div id='id-section11'></div>

## Be aware of any event, interval you add
Events took so many resources when they overused,
immediately call __removeEventListener()__ when you've done with any event, The same goes for intervals and timeouts


<div id='id-section12'></div>

## Reduce reflow and repaint on your page

Always look for ways to reduce reflow and repaint on your page, for example using __DocumentFragment__, From [MozDev](https://developer.mozilla.org/en-US/docs/DOM/document.createDocumentFragment)

>Since the document fragment is in memory and not part of the main DOM tree, appending children to it does not cause page reflow (computation of element's position and geometry). Consequently, using document fragments often results in better performance.

```javascript
// B A D
let div = document.QuerySelector('div');
div.appendChild(document.createTextNode("Some text"));
div.appendChild(document.createElement("br"));
div.appendChild(document.createElement("a"));
document.body.appendChild(div);

// B E T T E R
let frag = document.createDocumentFragment();
frag.appendChild(document.createTextNode("Some text"));
frag.appendChild(document.createElement("br"));
fragdiv.appendChild(document.createElement("a"));
document.body.appendChild(frag);

// you can also use es6 template literals + HTML and would be a lot more faster
// just imagine duplicating a node element vs duplicating a string (html) text
// a lot more fluid and agile, you can use all your string manipulation skills
```


<div id='id-section13'></div>

## Reduce reflow and repaint on your page

to be continued !

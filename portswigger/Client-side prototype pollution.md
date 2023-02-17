-->namal oru js file il let arr= ["john", "ajay"] enn koduth save chythit athinte browseer il console il poi arr. enn kodukumbol thane kore methods kanan patum athonum namal defice chythathalla. so angane automaic aayi defaualt aayi veruath prototype karanam aan ok
-->whenever we create a javascript object, js object automatically attaches our object with hidden properties and methods. ee properties aan namal console il poi object. ennadikumbol namuk suggesition pole varunath ok
-->so ella properties um ariyan namal `arr.__proto__.`  enn koduthal namuk kanan patum ok
-->so always remember js ile oro arrays,fn,objects ilum prototype und ok
-->developers oru object inte prototype edit chyumbol code il thane `object2.__prototype__=object`  enna reethiyil code chyan padila ok

-->A successful CSPP exploit requires two components:

-   A way to poison the prototype, referred to as a prototype pollution source.
-   A way to use a poisoned prototype for an actual exploit, referred to as a prototype pollution gadget.

## What is a prototype in JavaScript?
-->Every object in JavaScript is linked to another object of some kind, known as its prototype.For example, strings are automatically assigned the built-in `String.prototype`
-->You can even chain references to `__proto__` to work your way up the prototype chain:
```
username.__proto__                              // String.prototype 
username.__proto__.__proto__                    // Object.prototype username.__proto__.__proto__.__proto__          // null
```
-->ivide username enna object und ath oru string aan.athinte mukalil ulla prototype aan object.ee object ennath root level il ullathan.ini athinum prototype und but ath null aan.

## What is prototype pollution?
-->Prototype pollution is a JavaScript vulnerability that enables an attacker to add arbitrary properties to global object prototypes, which may then be inherited by user-defined objects.

## Finding prototype pollution sources manually
-->Finding [prototype pollution sources](https://portswigger.net/web-security/prototype-pollution/what-is-prototype-pollution#prototype-pollution-sources) manually is largely a case of trial and error. In short, you need to try different ways of adding an arbitrary property to the `Object.prototype` until you find a source that works. This involves the following high-level steps:

1.  Try to inject an arbitrary property via the query string, URL fragment, and any web message data. For example:
     `vulnerable-website.com/?__proto__[foo]=bar`
2.  In your browser console, inspect the `Object.prototype` to see if you have successfully polluted it with your arbitrary property:
     `Object.prototype.foo // "bar" indicates that you have successfully polluted the prototype // undefined indicates that the attack was not successful`
3.  If the property was not added to the global prototype, try using different techniques, such as switching to dot notation rather than bracket notation, or vice versa:
    `vulnerable-website.com/?__proto__.foo=bar`
4.  Repeat this process for each potential source.

## Detecting prototype pollution wth Dom invader
-->burp il ulla browser il domivader default aayi und
-->Once you enable prototype pollution, DOM Invader automatically checks the page for sources that enable you to add arbitrary properties to the `Object.prototype`. Any sources it identifies are displayed in the **DOM** view, along with some useful information and features for further testing.
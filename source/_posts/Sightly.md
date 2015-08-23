title: "Sightly"
date: 2015-05-16 22:46:16
alias: aem/index.html
tags: 语言学习 
categories: 语言学习
comments: true

---
Since myself has been work on French AEM project for several months, I realy recogize the perfect of Sightly langue.Sightly is an HTML templating language, it’s focus on keeping your markup beautiful, and thus maintainable, once made dynamic. This article is to introduce the Sightly HTML templating system and provides a quick reference of the syntax and available block statements.

## Getting Started
Every Sightly template is an HTML5 document or fragment, augmented with a specific syntax that adds the dynamic functionality. Here's a first example:
```html
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```
Two different kind of syntaxes have to be distinguished:
{% blockquote %}
- ** Sightly Block Statements: ** To define structural block elements within the template, Sightly employs HTML5 data attributes. This allows to attach behavior to existing HTML elements. All Sightly-specific attributes are prefixed with ` data-sly- ` .
- ** Sightly Expression Language: ** Sightly expressions are delimited by characters ` ${ ` and ` } `. At runtime, these expressions are evauated and their value is injected into the outgoing HTML stream. They can occur within the HTML text or within HTML attribute values.
{% endblockquote %}

```html
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}">
        ${logic.text}
    </a>
</p>
```
```html
<h1 data-sly-test="${currentPage.title}">
    <a href="${currentPage.path}.html">
        ${currentPage.title}
    </a>
</h1>
```
For the code example above:

- Line 1: The ` data-sly-test ` statement checks if the page title exists and is not empty. If so, the ` <h1> `  element and its content is displayed, otherwise it is removed altogether.
- Line 2: This expression renders the page path into the href attribute. Since Sightly knows the HTML syntax, href and src attributes are automatically protected against cross-site scripting (XSS) injections accordingly. For instance if the variable contained a javascript: URL, it would have been removed.
- Line 3: This expression displays the page title as text. Many special characters are escaped to make it impossible to inject cross-site scripts.

## Comments
``` javascript 
<!--/* A Sightly Comment */--> 
```
## Expressions
Sightly expressions are used to access the data structures that provide the dynamic elements of the HTML output. A Sightly expression is delimited by ` ${ ` and ` } `. The expression syntax includes literals, variables, operators and options:
### Literals

** Boolean **
Boolean represents a logical entity and can have two values: true, and false.
``` javascript 
${true} ${false} 
```
** Numbers **
There is only one number type: integers. Floating point numbers are not supported:
``` javascript 
${42} //will echo 42            
```

** Strings **
They represent textual data, and can be single or double quoted:
``` javascript 
${'foo'} ${"bar"} 
```
Single character escape  sequences: \\ \' \" \t \n \r \f \b
```javascript 
${'it\'s great'}
```
Unicode escape sequences: \u followed by 4 hexadecimal digits:
``` javascript 
${'it\u0027s great'}
```
### Variables 
Simple member access:
``` javascript
${properties.text} 
```
Colons are permitted in identifiers, which is useful for accessing JCR properties:
```javascript
${properties.jcr:title} 
```
Accessing members with special characters (in this case a space):
```javascript 
${properties['my property']} 
```
Accessing members dynamically:
```javascript
${properties[myVar]} 
```
### Operators 
Logical NOT:
``` javascript  
${!var} //
```
Logical AND:
``` javascript
${varOne && varTwo}
```
Logical OR (can be used to provide string defaults):
``` javascript 
${varOne || varTwo} 
${properties.text || properties.default}
```
Conditional:
``` javascript
${varChoice ? varOne : varTwo}
```
Comparison (only supports integers):
```javascript
${varOne < varTwo} 
${varOne > varTwo}  
${varOne <= varTwo} 
${varOne >= varTwo} 
```
Equality and inequality (only supports two variables of identical type):
```javascript
${varOne == varTwo}
${varOne != varTwo} 
```
Grouping parentheses:
``` javascript
${varOne && (varTwo || varThree)} 
```

### Options
Expression options can act on the expression and modify it, or serve as parameters when used in conjunction with block statements.

Everything after the ` @ `  is an option:
``` javascript 
${myVar @ optOne} 
```
Options can have a value, which may be a variable or a literal:
``` javascript
${myVar @ optOne=bar} 
${myVar @ optOne='bar'} 
```
Multiple options are separated by commas:
``` javascript 
${myVar @ optOne, optTwo=bar} 
```
Parametric expressions containing only options are also possible:
``` javascript
 ${@ optOne, optTwo=bar} 
```
### Block Statements

Sightly block statements are custom data attributes added directly to existing HTML. This allows easy and unobtrusive annotation of a prototype static HTML page, converting it to a functioning dynamic template without breaking the validity of the HTML code.
use
` data-sly-use `: Initializes a helper object (defined in JavaScript or Java) and exposes it through a variable.
Initialize a JavaScript object, where the source file is located in the same directory as the template. Note that the filename must be used:
```html
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```
Initialize a Java class, where the source file is located in the same directory as the template. Note that the classname must be used, not the file name:
```html
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```
Initialize a Java class, where that class is installed as part of an OSGi bundle. Note that its fully-qualified class name must be used:
``` html 
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div> 
```
Parameters can be passed to the initialization using options. Generally this feature should only be used by Sightly code that is itself within a ` data-sly-template ` block:
``` html
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```
Initialize another Sightly template that can then be called using ` data-sly-call `:
``` html 
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

{% blockquote Adobe http://docs.adobe.com/content/docs/en/aem/6-0/develop/sightly.html %}
See more detail from adobe documentation online 
{% endblockquote %}
eson
===

eson (extensible single object notation), is a JSON based notation designed for declarative programming. eson and it's parsers are used as the base notation for simple JSON preprocessing or for developing declarative DSLs. 

#Syntax

eson documents are valid JSON documents and conform to the [JSON format](http://json.org/). 

##Keywords
There are JSON names called keywords that act as identifiers in eson. All eson keywords are prefixed with the `&` symbol. eson has built in keywords called special forms but the user is free to defined new keywords once they do not conflict with this set of special forms. 

##Special forms
Special forms are keywords that map to handler functions in the reader. Special forms cannot be redefined by the user or used in a manner contrary to this specification. The following is a list of eson special forms.

##Singles
A single is a JSON structure for evaluating keywords such as special forms or eson defined relations. A single is a 1-tuple JSON object and thus MUST have only one pair. The pair's name MUST be a keyword and the value MUST be an array. The elements of the array are passed as parameters to the keywords. If the length of the array does not match the arity expected by the keyword then an error is thrown.  

```JSON
{ "&keyword": ["param"] }
```
When the reader meets a single it evaluates it and substitutes the single with the value it returns. A single's syntax can be thought of as the poor man's s-expression. In compliance with the declarative programming style, singles strive to satisfy properties rather than execute commands or sequences of commands.

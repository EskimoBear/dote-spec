eson
===

eson (extensible single object notation), is a JSON based notation designed for declarative programming. eson and it's parsers are used as the base notation for simple JSON preprocessing or for developing declarative DSLs. 

#Syntax

eson documents are valid JSON documents and conform to the [JSON format](http://json.org/). 

##Symbols
There are JSON names called symbols that are identifiers for eson functions. All eson symbols are prefixed with the `&` character. eson has built in symbols called special forms but the user is free to define new symbols once they do not conflict with this set. 

##Special forms
Special forms are symbols that map to functions in the reader. Special forms cannot be redefined or used in a manner contrary to this specification. In compliance with eson's declarative programming style, singles strive to satisfy properties rather than execute commands or sequences of commands.
The following is a list of eson special forms.

1. &ref
2. &this
2. &bind
3. &proc
4. &doc

###&ref
This special form is a use of the JSON pointer.

##Singles
A single is a JSON structure for evaluating symbols such as special forms or eson defined relations. A single is a 1-tuple JSON object and thus MUST have only one pair. The pair's name MUST be a symbol and the value MUST be either an array or null. If an array is provided as the value, it's elements are passed as parameters to the symbols function. The reader interprets a single with a null value as a 0-arity function call. If the length of the array does not match the arity expected by the function then an error is thrown.  

```JSON
{"name1" : { "&symbol1": ["param"] },
 "name2" : { "&symbol2": null }}
```
When the reader meets a single it evaluates it and substitutes the single with the value it returns. Thus, singles are always defined as values for JSON members. A single's syntax can be thought of as the poor man's s-expression. 

Some functions do not need to be mapped to a JSON pair. For these functions, the symbol is prefixed with another `&` symbol and can be written as a JSON member instead of a single. 

```JSON
{ "name1": "value1",
  "$$symbol": ["evn.core.button"]}
```

When the reader meets a `&&` prefixed name in a JSON member it passes the member as a single element of `$proc`'s parameter list. The reader removes this member from it's initial position in the eson document during evaluation. `&&` is syntactic sugar for an explicit use of the special form `&proc`.



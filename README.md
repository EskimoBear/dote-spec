eson
===

eson (extensible single object notation), is a JSON based notation designed for declarative programming. eson and it's parsers are used as the base notation for simple JSON preprocessing or for developing declarative DSLs. 

#Syntax

eson documents are valid JSON documents and conform to the [JSON format](http://json.org/). 

##Symbols
Symbols are identifiers for eson functions and relations. eson has built in symbols called special forms but the user is free to define new symbols once they do not conflict with this set. 

##Special forms
Special forms are symbols that map to functions in the eson reader. Special forms cannot be redefined or used in a manner contrary to their reader specification. In compliance with eson's declarative programming style, special forms strive to satisfy properties upon evaluation. This in contrast to executing commands or a sequences of commands.

##Singles
A single is a JSON structure for evaluating symbols. A single is a JSON pair which satisfies the following conditions:

1. name MUST be prefixed with `&`
1. string following the `&` prefix MUST be a symbol
1. value MUST be either an array or null. 

```ebnf
symbol = JSON_string
single = "&", symbol : JSON_array | JSON_null
```

If an array is provided as the value, it's elements are passed as parameters to the symbols function. The reader interprets a single with a null value as a 0-arity function call. If the length of the array does not match the arity expected by the function then an error is thrown.  

When the reader interprets a JSON member as a single it evaluates it and then removes the member from the eson document. 

```JSON
{ 
  "name1": "value1",
  "&symbol1": ["param1"]
}
```

For some singles it makes sense to have their value (if they return a value at all) mapped to a JSON name. Such singles are defined as values of a JSON pair and must be contained within JSON objects to be legal syntax. When the reader meets such a single it evaluates it and substitutes the single with the value it returns. Because of this substitution, such singles MUST be defined as a 1-tuple JSON object, i.e. with the single being the only pair present. This type of single is known as a value single and is the basis for JSON preprocessing in eson.

```JSON
{
  "name1": {"&symbol1": ["param"]},
  "name2": {"&symbol2": null}
}
```

###Comparison with s-expressions
A single can be thought of as the poor man's s-expression. 

| s-expression | single |
|--------------|--------|
| (plus)    | "&plus" : null |
| (plus 1)  | "&plus" : [1] |
| (plus 1 2)| "&plus" : [1, 2] |
| (plus 1 (minus 3 1)| "&plus" : [1, {"&minus": [1, 2] } ] |

#Special form definitions
The following is a list of eson's base special forms.
1. &ref
2. &this
2. &bind
3. &proc
4. &fn
5. &doc

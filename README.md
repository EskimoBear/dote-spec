eson
===

eson (extensible single object notation), is a notation for declarative programming. eson is a superset of JSON and eson programs are legal JSON, thus eson is easy for everyone familiar with JSON to read and understand. eson and it's parser are used for simple JSON preprocessing and as the base notation for declarative external DSLs. 

#Syntax
eson programs use valid JSON syntax and conform to the [JSON format](http://json.org/), JSON structures such as objects, pairs and values are specific forms of their equivalents in eson. eson extends the semantic expression of JSON, adding programmable declarativeness to the descriptive declarativeness JSON already provides. 

## Declarations
eson programs are made up of unordered declarations. The JSON pair is a member of the set of the set of declaration types defined by eson and it's semantics are maintained by eson. As a result, an eson program can be converted to it's semantic JSON equivalent by removing all declaration types save for JSON pairs.  

##Symbols
Symbols are identifiers for eson functions and relations. eson has built in symbols called special forms but the user is free to define new symbols once they do not conflict with this set. 

##Records
The eson reader accepts all JSON pairs without prefixed names as record declarations.

##Special forms
Special forms are symbols that map to functions in the eson reader. Special forms cannot be redefined or used in a manner contrary to their reader specification. In compliance with eson's declarative programming style, special forms strive to satisfy properties upon evaluation. This in contrast to executing commands or a sequences of commands.

##Singles
A single is a JSON structure for calling procedures; the single syntax combined with reader handler functions enables declarative programming in JSON documents. Singles are a fundamental facility of the notation because they allow eson documents to be parsed as an unordered set of procedures and records. The eson reader is responsible for recognizing singles and evaluating the respective functions in eson documents.

A single is a JSON pair which satisfies the following conditions:

1. name MUST be prefixed with `&`
1. string following the `&` prefix MUST be a symbol
1. value MUST be either an array, null or a JSON object containing only a single. 

If an array is provided as the value, it's elements are passed as parameters to the symbols function. The reader interprets a single with a null value as a 0-arity function call. If the length of the array does not match the arity expected by the function then an error is thrown. When the reader interprets a JSON member as a single it evaluates it and then removes the member from their current position in the eson document. An eson reader must retain these members in a special array with the keyword "env". The reader should allow the user to choose whether or not the "env" member should be shown. 

```JSON
{ 
  "name1": "value1",
  "&symbol1": ["param1"]
}
```

This type of single is referred to as a *weak single* because it does not provide useful referrential transparency. It provides referrential transparency in the sense that the single will be replaced by the "empty member" but this is not a useful result for declarative combinination. Because duplicate keys are not legal JSON, a weak-single for a special form can only be defined once per document.

For some functions it makes sense to have the result of their evaluation (if they return a result at all) mapped to a JSON name. Singles that can evaluate and perform substitutions for functions are defined as values of a JSON pair and MUST be contained within a JSON object. When the reader meets such a single it evaluates it and substitutes the single with the value it returns. To make this substitution result in legal JSON, such singles MUST be defined as a 1-tuple JSON object, i.e. with the single being the only pair present. 

```JSON
{
  "name1": {"&symbol1": ["param"]},
  "name2": {"&symbol2": null}
}
```
Singles get their name from the 1-tuple JSON object, the only context in which singles provide referrential transparency with the constraints of the JSON format. For this reason, the 1-tuple bounded single is the basis for JSON preprocessing in eson. 

###Comparison with s-expressions
A single can be thought of as the poor man's s-expression. The similaities are borne out from the fact that they are both pair based structures.

| s-expr lisp | s-expr ebnf| single |
|-------------|------------|--------|
| `(plus)`    | ` plus | nil`| `{"&plus" : null}` |
| `(plus 1)`  | `plus | 1 | nil` | `{"&plus" : [1]}` |
| `(plus 1 2)`| `plus | 1 | 2 | nil` |`{"&plus" : [1, 2]}` |
| `(plus 1 (minus 3 1))`| `plus| 1 | (minus | 3 | 1) | nil` | `{"&plus" : [1, {"&minus": [3, 1] } ]}` |

#Special form definitions
The following is a list of eson's special forms:

1. ref
1. doc
1. proc
2. this
2. bind

##ref
ref takes a [JSON pointer](https://tools.ietf.org/html/rfc6901) and returns the specified JSON value.

```JSON
{ "&ref": "#/foo" }
```

##doc
Generate a doc pair for a given keyword and add it to a nested object for the keyword pair. In the example below `int_eson` and `int_json` are shown in the same document for brevity.

```JSON
{
  "int_eson": 90,
  "&&doc": [
    [
      "int_eson",
      "JSON can have integers"
    ]
  ],
  "int_json": {
    "value": 90,
    "doc": "JSON can have integers"
  }
}
```

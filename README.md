eson
===

eson (extensible *single* object notation) is an extension language for JSON. 

eson documents extend the strictly descriptive capabilities of JSON to provide features such as variables and composition. The eson compiler allows you to extend the features of the language so you can quickly add any features you would like to use. You can also go so far as to create declarative DSLs as the compiler can generate code in different programming languages. 

Doing declarative programming with eson gives you the familiarity and ease of use of JSON and the flexibility to extend. 

#Syntax
eson programs use valid JSON syntax and conform to the [JSON format](http://json.org/), eson extends the semantic expression of JSON to inculde programmable declarativeness. eson programs are made up of unordered declarations, the three types of declarations are lets, calls and attributes.

##Attributes
The attribute is a regular JSON name/vaule pair. In eson, JSON pairs perfom variable assignment operations, where the key/name is the identifier name and the value is the data bound to the variable. The variable identifier can be referenced in the program by the `$` prefix.

```JSON
{"name": "John Smith"
 "docstring": "$name works at the Post Office"}
```

##Let
The let declaration is a `special form` which creates unbound variables in the eson document.

```JSON
{"&let":["red","blue"]}
```

The eson document above creates two variables `$red` and `$blue`which are not bound to any values.

##Calls
A call is an eson primitive for performing procedure application. A call is a JSON pair which allows procedures defined in the eson compiler to be evaluated in the eson program. A call is a JSON pair which satisfies the following conditions:

1. the JSON key MUST be prefixed with `&`,
1. string following the `&` prefix MUST be the name of a special form,
1. and the JSON value MUST be either an array, null or a single. 

```JSON
{ name": "John Smith",
  "&special-form": ["param"] }
```

If an array is provided as the value, it's elements are passed as parameters to the procedure. The compiler interprets a call with a null value as a 0-arity function call. If the length of the array does not match the arity expected by the function then an error is thrown. When the compiler interprets a call it first evaluates it and then removes the pair from it's current position in the eson document. The compiled JSON by default does not contain these calls, but the user can instruct the compiler to return the calls in the document as a special pair with key "env". Because duplicate keys are not legal JSON, a call to a special form or procedure can only be defined once per program.

###Singles
Because JSON objects are not recursive data structures, procedure application takes two forms: one supporting evaluation only and another form where both evaluation and substitution take place. The previously mentioned `call` provides the first type of procedure application, a single performs procedure application with substitution, allowing for *referential transparency*. A single is a special JSON object containing one and only one call, the snippet below shows a single.

```JSON
{ "name": {"&specia1-form1": ["param"]} }
```

After the single is evaluated the single, i.e. the nested JSON object is substituted by the result of the procedure application. Please note that the *referential transparency* is a guarantee of the underlying procedure and not the single. A call can be thought of as a *weak single* because it does not provide useful referential transparency. It provides referential transparency in the sense that the single will be replaced by the "empty member" but this is not a useful result for declarative combinination. Since singles appear as JSON values, singles can be used every time a JSON pair is defined. 

Singles get their name as a result of being a tuple which is always of length 1. A JSON object is unordered so does not normally qualify as a tuple but with one item an unordered collection is equivalent to an ordered one. 

###Single or call?
There exists a special case where an eson program consists of a single call. The question this raises is whether this is interpreted as a single or a call? It's actually both and neither.

```JSON
{
  "&special-form": ["param"]
}
```

The eson compiler will treat this as a single where the call is evaluated but no substitution is possible because the resulting JSON document is the empty document `{}` and no substitution can take place within the empty document. Thus the observed result is that of a call and not a single. This illustrates that the single is just a regular eson program. In this scenario the eson compiler should return the value of the call instead of nil.

##Special forms
Special forms are procedures/functions built into the eson compiler which provide eson with programmable capabilities. In compliance with eson's declarative programming style, special forms satisfy properties of the program upon evaluation. This is in contrast to executing commands or a sequences of commands in an imperative programming style. All special forms guarantee *referential transparency*.

###let
the let special form performs variable creation which allows the use of unbounded variables in eson programs. Let takes an array of JSON strings and creates unbound variables for each element of the array. let can only be called once per program and should appear as a call as it evaluates to null. An unbound or bound variable is referenced by the `$` prefix in the program.

```JSON
"&let":["red","blue"]
```

The snippet above creates two variables `$red` and `$blue`.

###ref
ref takes a [JSON pointer](https://tools.ietf.org/html/rfc6901) and returns the specified JSON value.

```JSON
{ "&ref": "#/foo" }
```

###doc
Generate a doc pair for a given keyword and add it to a nested object for the keyword pair. In the example below `int_eson` and `int_json` are shown in the same document for brevity.

```JSON
{
  "int_eson": 90,
  "&doc": [
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

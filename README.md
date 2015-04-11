eson
===

eson is a declarative programming language based on JSON. eson (*e-son* i.e *e* as in dr*e*ss) is an acronym for "extensible single object notation"; a single is one of the 'structures' eson uses for procedure application. eson goes beyond the data description of JSON to provide features such as variables, and procedure application. 

#Syntax
eson programs use valid JSON syntax and conform to the [JSON format](http://json.org/).

##Declarations
eson programs are made up of unordered declarations, there are two types of declaration: calls and attributes.

###Attributes
An attribute is a regular JSON name and value pair. In eson, JSON pairs perfom the variable assignment operation, where the name/key is the identifier name and the value is the data bound to the variable. Attributes cannot begin with `$` or `&` as these are reserved.

###Calls
A call is an eson primitive for performing procedure application. A call is a JSON pair which allows procedures defined in the eson compiler to be evaluated in the eson program.

The following snippet illustrates a call:

```JSON
{
  "&let": ["var1", "var2"],
  name": "John Smith"
}
```

this compiles to:

```JSON
{
  "name": "John Smith"
}
```

A call is a JSON pair which satisfies the following conditions:

1. the JSON key MUST be prefixed with `&`,
1. string following the `&` prefix MUST be the name of a special form,
1. and the JSON value MUST be either an array, null or a single. 

If an array is provided as the value, it's elements are passed as parameters to the procedure. The compiler interprets a call with a null value as a 0-arity function call. If the length of the array does not match the arity expected by the procedure then an error is thrown.
When the compiler interprets a call it first evaluates it and then removes the pair from it's current position in the eson program. The compiled JSON by default does not contain these calls, but the user can instruct the compiler to return the calls in the document as a special pair with key "env". Because duplicate keys are not legal JSON, a special form or procedure can only be used in a call once per program.

##Procedure Application
Because JSON objects are not recursive data structures, procedure application takes two forms. One form supports evaluation while the other form supports both evaluation and substitution. The previously mentioned `call` provides the first type of procedure application where the procedure is evaluated but the output (if any) is not retained. eson has a structure known called a single that can perform procedure application with substitution.

###Singles
A single is a JSON object containing one and only one call. After the single is evaluated it is substituted by the result of the procedure evaluation. A call can be thought of as a *weak single* because it does not provide substitution. Since singles can appear anywhere a JSON value can, there are no duplication concerns as with calls and singles may be used any time a JSON pair is defined.

A single is a JSON object which satisfies the following conditions:

1. has one declaration;
1. and the declaration is a call.

The snippet below illustrates a single with the imaginary special form `&get-first-name`:

```JSON
{
  "name": {
    "&get-first-name": ["Caroline Spencer"]
  }
}
```

this compiles to:

```JSON
{
  "name": "Caroline"
}
```

Singles get their name as a result of being a tuple which is always of length 1. A JSON object is unordered so does not normally qualify as a tuple but with one item an unordered collection is equivalent to an ordered one.

###Single or call?
There exists a special case where an eson program consists of a single call. The question this raises is whether this is interpreted as a single or a call? It's actually both and neither.

```JSON
{
  "&special-form": ["param"]
}
```

The eson compiler will treat this as a single where the call is evaluated but no substitution is possible because the resulting JSON document is the empty document `{}` and no substitution can take place within the empty document. Thus the observed result is that of a call and not a single. This illustrates that the single is just a regular eson program. In this scenario the eson compiler should return the value of the call instead of nil.

##Variables
Attribute declarations create variables which can be referenced in an eson program by the attribute name. To reference a variable identifier one must prefix the attribute name with the `$` prefix, thus variable identifiers are strings. Variable identifiers are substituted with the attribute value when the program is compiled.

To illustrate the eson program below:

```JSON
{
  "name": "John Smith",
  "docstring": "$name works at the Post Office"
}
```

compiles to

```JSON
{
  "name": "John Smith",
  "docstring": "John Smith works at the Post Office"
}
```

##Special forms
Special forms are functions built into the eson compiler which provide eson with programmable capabilities. In compliance with eson's declarative programming style, special forms satisfy properties of the program upon evaluation. This is in contrast to executing commands or a sequences of commands in an imperative programming style.

###let
the let special form performs variable creation which allows the use of unbounded variables in eson programs. Let takes an array of JSON strings and creates unbound variables for each element of the array. let can only be called once per program and should appear as a call as it evaluates to null. An unbound variable is referenced by the `$` prefix in the program in the same way bound variables.

```JSON
  "&let":["foo"]
```

The snippet above creates the unbound variable `$foo`.

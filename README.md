Dote
===

Dote is a JSON extension language, which adds variables and procedure application to JSON.

#Syntax
Dote programs are valid JSON and conform to the [JSON format](http://json.org/).

#Programs
A JSON object corresponds to a Dote program. Nested JSON objects are themselves Dote programs.

#Declarations
Dote programs are made up of unordered declarations. There are two types of declarations: calls and attributes.

#Attributes
An attribute is a regular JSON key and value pair. In Dote, pairs are used for variable assignment, where the key is the identifier name and the value is the data bound to the variable. Attributes cannot begin with either `$` or `&` as these symbols are reserved. The snippet below shows an attribute,

```JSON
{ "name": "John Smith" }
```

#Variables
To reference a variable identifier one must prefix the attribute name with the `$` prefix. Variable identifiers are substituted with the attribute value when the Dote program is compiled.

To illustrate the Dote program below:

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

#Calls
A call is a pair used for procedure application. A call passes control to a procedure defined in the key; the value given in the pair is passed as the procedure's parameter/s. A call is a JSON pair which satisfies the following conditions:

1. the JSON key MUST be prefixed with `&`;
1. the string following the `&` prefix MUST be the name of a procedure recognized by the compiler;
1. and the JSON value MUST be either null, an array, or a single.

The following snippet illustrates a call:

```JSON
{ "&let": ["var1", "var2"] }
```

If an array is provided as the value, it's elements are passed as parameters to the procedure. If a valid value is provided the pair is considered a 1-arity procedure call. However, if the value is null the call is considered a 0-arity procedure call. Because duplicate keys are not legal JSON, a procedure can only be used in a call once per program. Calls are evaluated but are not retained in the generated JSON.

#Singles
Dote has another structure for procedure application called a single. Unlike a call, a single supports procedure application with evaluation and substitution. A single is a nested program comprised solely of one call. After the single is evaluated it is substituted by the result of the procedure evaluation. Singles may be used any time a JSON pair is defined.

A single is a Dote program which satisfies the following conditions:

1. has one declaration;
1. and the declaration is a call.

The snippet below illustrates a single with the imaginary procedure `&get-first-name`:

```JSON
{
  "name": {
    "&get-first-name": ["Caroline Spencer"]
  }
}
```

which compiles to:

```JSON
{
  "name": "Caroline"
}
```

When a Dote program is a single no substitution can take place and the resulting JSON document is `{}`.

#Special pairs
Special pairs are the built in abstractions in Dote.

##let
the let special pair performs variable creation which allows the use of unbounded variables in Dote programs. `let` takes an array of JSON strings and creates unbound variables for each element of the array. Unbound variable are referenced by the `$` prefix in the rest of the program. The snippet below creates the unbound variable `$foo`.

```JSON
{
  "&let": ["foo"]
}
```

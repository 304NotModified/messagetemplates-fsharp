
# MessageTemplates

An implementation of named string replacements, which allows formatting, parsing, and capturing properties. MessageTemplates is compatible with the [Serilog](http://serilog.net/) [template format](https://github.com/serilog/serilog/wiki/Structured-Data).

The C# implementation was extracted from Serilog itself.

The F# implementation was designed to be copied (or [paket github referenced](http://fsprojects.github.io/Paket/github-dependencies.html)) into existing libraries or applications directly, however there may also be a Nuget package in the future.

[![Build status](https://ci.appveyor.com/api/projects/status/i0y2e205vask3425/branch/master?svg=true)](https://ci.appveyor.com/project/adamchester/messagetemplates/branch/master)

### Samples

#### Format an F# Record
```fsharp
type User = { Id:int; Name:string }
format (parse "Hello, {@user}") [| {Id=1; Name="Adam"} |]
// > val it : string = "Hello, User { Id: 1, Name: "Adam" }"
```

### Format a C# Class

```csharp
class Chair {
    public string Back { get { return "straight"; } }
    public int[] Legs { get { return new[] { 1, 2, 3, 4 }; } }
    public override string ToString() { return "a chair"; }
}

Assert.Equal(
    "I sat at Chair { Back: \"straight\", Legs: [1, 2, 3, 4] }",
    MessageTemplate.Format("I sat at {@Chair}", new Chair()));
```

### Message Template Syntax

Message templates are a superset of standard .NET format strings, so any format string acceptable to `string.Format()` will also be correctly processed by `MessageTemplates`.

* Property names are written between `{` and `}` brackets
* Brackets can be escaped by doubling them, e.g. `{{` will be rendered as `{`
* Formats that use numeric property names, like `{0}` and `{1}` exclusively, will be matched with the `Format` method's parameters by treating the property names as indexes; this is identical to `string.Format()`'s behaviour
* If any of the property names are non-numeric, then all property names will be matched from left-to-right with the `Format` method's parameters
* Property names may be prefixed with an optional operator, `@` or `$`, to control how the property is serialised
* Property names may be suffixed with an optional format, e.g. `:000`, to control how the property is rendered; these format strings behave exactly as their counterparts within the `string.Format()` syntax

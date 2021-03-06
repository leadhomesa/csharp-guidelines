## Framework Guidelines

### Use C# type aliases instead of the types from the `System` namespace :red_circle:
:police_car: Not sure I agree with the exception to the rule.
For instance, use `object` instead of `Object`, `string` instead of `String`, and `int` instead of `Int32`. These aliases have been introduced to make the primitive types first class citizens of the C# language, so use them accordingly.

**Exception:** When referring to static members of those types, it is custom to use the full CLS name, e.g. `Int32.Parse()` instead of `int.Parse()`. The same applies to members that need to specify the type they return, e.g. `ReadInt32`, `GetUInt16`.

### Prefer language syntax over explicit calls to underlying implementations :red_circle:
:police_car: Not sure I agree with all the items here
Language syntax makes code more concise. The abstractions make later refactorings easier (and sometimes allow for extra optimizations).

**Good**
``` c#
(string, int) tuple = ("", 1);
```
**Bad**
``` c#
ValueTuple<string, int> tuple = new ValueTuple<string, int>("", 1);
```

**Good**
``` c#
DateTime? startDate;
```
**Bad**
``` c#
Nullable<DateTime> startDate;
```

**Good**
``` c#
if (startDate != null) ...
```
**Bad**
``` c#
if (startDate.HasValue) ...
```

**Good**
``` c#
if (startDate > DateTime.Now) ...
```
**Bad**
``` c#
if (startDate.HasValue && startDate.Value > DateTime.Now) ...
```

**Good**
``` c#
(DateTime startTime, TimeSpan duration) tuple1 = GetTimeRange();
(DateTime startTime, TimeSpan duration) tuple2 = GetTimeRange();

if (tuple1 == tuple2) ...
```
**Bad**
``` c#
if (tuple1.startTime == tuple2.startTime && tuple1.duration == tuple2.duration) ...
```

### Don't hard-code strings that change based on the deployment :white_circle:
Examples include connection strings, server addresses, etc. Use `Resources`, the `ConnectionStrings` property of the `ConfigurationManager` class, or the `Settings` class generated by Visual Studio. Maintain the actual values into the `appsettings.json`.

### Build with the highest warning level :red_circle:
:police_car: We should probably do this, but it won't end well
Configure the development environment to use **Warning Level 4** for the C# compiler, and enable the option **Treat warnings as errors** . This allows the compiler to enforce the highest possible code quality.

### Avoid LINQ for simple expressions :red_circle:

**Good**
``` c#
var query = items.Where(item => item.Length > 0);
```

**Bad**
``` c#
var query = from item in items where item.Length > 0 select item;
```

### Use lambda expressions instead of anonymous methods :red_circle:

**Good**
``` c#
var customer = customers.FirstOrDefault(customer => customer.Name == "Tom");
```

**Bad**
``` c#
Customer customer = Array.Find(customers, delegate(Customer customer)
{
	return customer.Name == "Tom";
});
```

### Only use the `dynamic` keyword when talking to a dynamic object :red_circle:
The `dynamic` keyword has been introduced for working with dynamic languages. Using it introduces a serious performance bottleneck because the compiler has to generate some complex Reflection code.

Use it only for calling methods or members of a dynamically created instance class (using the `Activator`) as an alternative to `Type.GetProperty()` and `Type.GetMethod()`, or for working with COM Interop types.

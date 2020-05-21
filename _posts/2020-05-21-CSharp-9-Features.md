---
title: C# 9 New Features
date: 2020-05-21 00:34:00 +0800
categories: [Csharp, Aspnetcore]
tags: [Csharp, Backend, aspnetcore]
toc: false
seo:
date_modified: 2020-05-21 14:30:11 -0500
---
{:refdef: style="text-align: center;"}
![upload-image](/assets/img/commons/csharp-9-features.jpg)
{: refdef}



Microsoft build 2020, Mads Torgersen, Program Manager for the C# Language and Dustin Campbel, a principal software engineer on the .NET Developer Experience team showed C# 9.0 features that will be shipped with .NET 5 in upcoming November 2020,

Let's go over in brief to see all features that were introduced in session

### **Improved Pattern Matching**

There are several new patterns been added to C# 9.0, Pattern Matching was added in C#7, [here](https://docs.microsoft.com/en-us/dotnet/csharp/pattern-matching) is a tutorial on pattern matching

##### **No need to declare default identifier in switch**

```csharp
// Before
public static decimal CalculateTotal(object product) =>
product switch
{
...

LaptopProduct l when t.ScreenSize > 13 => 500+ 50,
LaptopProduct l when t.ScreenSize > 15 => 500 + 75,
LaptopProduct _ => 500,

_ => throw new ArgumentException("Not a known Product type", nameof(product))
}
```

```csharp
//C# 9.0
LaptopProduct => 500
```

Note that on with C# 9.0 we can specify Type without '_'

##### **Relational Patterns**

With C# 9.0, relational operators (<, >, etc) can be used as patterns indicators like below

```csharp
//C# 9.0
LaptopProduct t when t.ScreenSize switch
{
> 13 => 500+ 50,
> 15 => 500 + 75,
_ => 500,
},
```
##### **Logical Patterns**

You can combine relational operators with Logical Operators and, or and not(word) like blow:

```csharp
//C# 9.0
LaptopProduct t when t.ScreenSize switch
{
> 13 and <= 15 => 500+ 50,
> 15 => 500 + 75,
_ => 500,
},
```

The way we use not pattern with null constant pattern is we can avoid unknown types falling into null bucket

```csharp
//C# 9.0
not null => throw new ArgumentException($"Not a known Laptop type: {product}", nameof(product)),
null => throw new ArgumentNullException(nameof(product))
```

not is also useful is-expression like below

instead of writing
```csharp
if(!(product is LaptopProduct))
```

we can write
```csharp
if(product is not LaptopProduct)
```

### **Top-level statements**

Currently, writing hello world program in C# for beginners can be confusing with all boilerplate code, but with C# 9.0, we can directly start writing statements like below:

```csharp
//Before
using System;
class Program
{
static void Main()
{
Console.WriteLine("Hello World!");
}
}
```

```csharp
//C# 9.0
using System;

Console.WriteLine("Hello World!");
```

Any statement will work here but it will be allowed only in one file since you can have only one main method.

#### **Target-typed "new"**
Expression doesn't need to say type it will come from context like below:

```csharp
//Before
Person person = new Person("Justin","Taylor");

//C# 9.0
Person person = new ("Justin","Taylor");

```

#### **Parameter null checking**

with 9.0 '!' at the end of parameter telling it will not accept nulls, it will throw argument null except if null will be passed without writing that line in all methods

```csharp
//Before
public Person(string firstName!, string lastName!);

//C# 9.0
Person person = new ("Justin","Taylor");

```

#### **Set Values Only in Constructor OR Object Initializer: Init Accessor**

In C#, for Object initializers to work, property must be mutable for it to work, with that limitation, it can be changed afterward intentionally or unintentionally.

To overcome this situation, C# 9.0 introduces new accessor 'init' which will make it immutable and it can be used like below:

```csharp
//before - mutable properties
public class Person
{
public string FirstName { get; set; }
public string LastName { get; set; }
}

//Object Intializer
new Person
{
FirstName = "Steve",
LastName = "Taylor"
}

//value change
person.LastName = "Clark"

```

```csharp
//before - mutable properties
public class Person
{
public string FirstName { get; init; }
public string LastName { get; init; }
}

//Object Initializer
new Person
{
FirstName = "Steve",
LastName = "Taylor"
}

//THIS WILL NOT BE ALLOWED WITH INIT ACCESSOR
//person.LastName = "Clark"

```
With 'init' it will give error if we will try to set property value anywhere else except constructor or initializer.

#### **Records**

Records are a new, simplified declaration form for C# class and struct types that combine the benefits of a number of simpler features. It can represent data as a Database record or some model entity:

Read-only properties => Immutable Type
Equality implementations => Structural equality
Pattern-matching support = is pattern, switch pattern etc.

```csharp
public data class Person
{
public string FirstName { get; init; }
public string LastName { get; init; }
}

```
The Data keyword marks class as a record. Let's go over few value-like features it will have by making it a Record class

##### **With-expressions : Non-distructive mutation**

Working with immutable data, common pattern is to create new values from existing value that represent a new state, For Example in Person object, if we want object with different last name, we would have new person object with copying properties from existing person object with only different last name

C# 9.0 allows this using "with-" expression

```csharp
var differentPerson = person with {LastName = "Jenny"}
```

It uses object initializer syntax to state what's different in the new object form old object. Behind the scene record class implicitly defines a <kbd>protected</kbd> copy constructor which takes original object to copy values:

##### **Value-based equality**

Object.Equals method will behave same for record class as Structs, comparing each field by calling Equals on them recursively.

Which means Two object instances from Record Class can be same without being same object. For Example: if we put original last name back and create new object like below, Equals method for (person, originalPerson) will be true, ReferenceEquals(person, originalPerson) will be false.

```csharp
var originalPerson = differentPerson with {LastName = "Taylor"}
```
##### **Data members**

Intention of Record Classes is to be immutable, with init-only public properties that can be modified using with-expression. For that, common case will be to have Default Public members instead of Private members, for that, C# 9.0 provides nice short-hand for init-properties like below:

```csharp
public data class Person { string FirstName; string LastName; }
```
is same as

```csharp

public data class Person
{
public string FirstName { get; init; }
public string LastName { get; init; }
}
```

and

```csharp
public data class Person
{
string FirstName;
string LastName;
public Person(string firstName, string lastName)
=> (FirstName, LastName) = (firstName, lastName);
public void Deconstruct(out string firstName, out string lastName)
=> (firstName, lastName) = (FirstName, LastName);
} public string LastName { get; init; }
}
```

can be written using shorter syntax like this:

```csharp
public data class Person(string FirstName, string LastName);
```

this will declare public init auto properties with constructor and destructor, so we can create object like this:

```csharp
var person = new Person("Steve", "Taylor");
var (f, l) = person;
```

##### **With-expressions and inheritance**

Records Class along with with-in expressions are challenging when we try to use with Inheritance.
Here is inherited Person class to Employee Class

```csharp
public data class Person { string FirstName; string LastName; }
public data class Employee : Person { int Salary; }
```
Now if we try to use with-in expression to create employee and try to store in Person variable:

```csharp
Person person = new Employee { FirstName = "Jacob", LastName = "Smith", Salary = 2000 };
otherPerson = person with { LastName = "Jenny" };
```

C# compiler has no idea that person actually contains Employee. It will not be properly copied to otherPerson if it wasn’t actually Employee object, complete with the same Salary as the first one copied over.

Records have a hidden virtual method that is created with “cloning” the whole object. Every derived record type overrides this method to call the copy constructor of that type, and the copy constructor of a derived record chains to the copy constructor of the base record. A with-expression simply calls that method and applies the object initializer to the result.

Here is link to MSbuild 2020 video of C# 9.0 : 
[C# Today and Tomorrow](https://channel9.msdn.com/Events/Build/2020/BOD108)

Couple GitHub links to see upcoming C# 9.0 features and progress
[C# 9.0 Language Feature Status](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)
[C# 9.0 candidate](https://github.com/dotnet/csharplang/milestone/15)

Happy Coding!
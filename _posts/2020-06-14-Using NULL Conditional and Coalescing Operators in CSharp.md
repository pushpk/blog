---
title: Using NULL Conditional and Coalescing Operators in C#
date: 2020-06-14 00:34:00 +0800
categories: [Csharp, Aspnetcore]
tags: [Csharp, Backend, aspnetcore]
toc: false
seo:
  date_modified: 2020-06-14 14:30:11 -0500
---
{:refdef: style="text-align: center;"}
![csharp-null](/assets/img/06142020/Csharp_null.jpg)
{: refdef}


Null checking is important part of developing quality code, Without it Code with throw <code>NullReferenceException</code>

Most common way to check for nulls is using If, consider below code

```C#

public class Program
{
	public static void Main()
	{
	   Person person1 = null;
	   DisplayPerson(person1);
	}

	static void DisplayPerson(Person person)
	{
	  Console.WriteLine(person.FirstName);
	  Console.WriteLine(person.LastName);
	}
}

class Person
{
  public string FirstName {get; set;}
  public string LastName {get; set;}
}
```

If we try to run this code, We will get <code>NullReferenceException</code> since there is no null checking in place inside <code>DisplayPerson</code> function

Now as soon as I get null exception, I go into code where <code>NullReferenceException</code> is causing and add if null check, like below

```C#
static void DisplayPerson(Person person){
	if(person != null)
	{
		Console.WriteLine(person.FirstName);
		Console.WriteLine(person.LastName);
	}
	
 }
```
and adding if null check is fine except adding it in every method in code makes code verbose.

### NULL-conditional Operator(?.)

Another way to solve this issue is to use null-conditional operator, <code>?.</code>  introduced in C# 6.0, here is how  we can reduce if null check by using null-conditional operator

```C#
static void DisplayPerson(Person person){
	Console.WriteLine(person?.FirstName);
	Console.WriteLine(person?.LastName);
 }
```

The part on the right of <code>?</code> only evaluates if the part to the left is not null. Otherwise, the code returns null. In the case above, <code>person?.FirstName</code> and <code>person?.LastName</code> evaluates to null, but it does not throw an exception because there is no attempt to access a member on a null reference. 

Use the null-conditional operator on members of namespaces, types, array elements (?.[]), access methods, or to invoke delegates.

Now this is good but what if we want some default value to be return if object is null, this is where Null-coalescing Operator <code>??</code> can be useful,

### Null-coalescing Operator(??)
Null-coalescing Operator is a binary operator that simplifies checking for null values. it is used to define a default value for nullable value types or reference types. It returns the left-hand operand if the operand is not null; otherwise, it returns the right operand. 

In cases where a statement could return null, the null-coalescing operator can be used to ensure a reasonable value gets returned. This code returns the Firstname and Lastname of a person or the default names if the person is null. this operator is a handy tool when working with the null-conditional operator.

```C#
 static void DisplayPerson(Person person){
    Console.WriteLine(person?.FirstName ?? "Default First Name");
    Console.WriteLine(person?.LastName ?? "Default Last Name");
 }
```

From C# 7, it is also possible to use the null-coalescing operator to shorten the validation of arguments, below code requires to validate name, here is how I would look 
```C#
internal class FormatName{

 private readonly string Name;

 public void SaveName(string name){
    if(name == null)
            throw new ArgumentNullException(nameof(name));
    }
    Name = name;
 }
```

Using the null coalescing operator allows this to be shortened to the code below.

```C#
internal class FormatName{

 private readonly string Name;

 public void SaveName(string name){
    Name = name ?? throw new ArgumentNullException(nameof(name));
 }
```

Checking NULL is vital part of code for stability, Using NULL-conditional and Null-coalescing Operator can really reduce and simplify code.

Happy Coding!
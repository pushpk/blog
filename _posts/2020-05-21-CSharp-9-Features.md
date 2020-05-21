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

Microsoft build 2020, Mads Torgersen, Program manager for the C# Language and Dustin Campbel, principal software engineer on the .NET Developer Experience team  showed C# 9.0 features that will be shipped with .NET 5 in upcoming November 2020,

Let's go over in brief to see all features that were introduced in session

### Improved Pattern Matching
   
There are several new patterns been added to C# 9.0, Pattern Matching was added in C#7, here is tutorial on patter matching

##### No need to declare default identifier in switch

       
```csharp
// Before
public static decimal CalculateTotal(object product) =>
product switch
{
    ...

LaptopProduct l when t.ScreenSize > 13  => 500+ 50,
LaptopProduct l when t.ScreenSize > 15 => 500 + 75,
LaptopProduct _ => 500,

_ => throw new ArgumentException("Not a known Product type", nameof(product))
}

```


    After 9.0   

```csharp
LaptopProduct => 500

```

Note that on with C# 9.0 we can specify Type without '_'

##### Relational Patterns

With C# 9.0, relational operators (<,> etc) can be used as pattens indicators  like below


```csharp
LaptopProduct t when t.ScreenSize switch
{
    > 13  => 500+ 50,
    > 15 => 500 + 75,
    _ => 500,
},
```
##### Logical Patterns

You can combine relational operatos with Logical Operators and, or and not(word) like blow:


```csharp
LaptopProduct t when t.ScreenSize switch
{
    > 13 and <= 15  => 500+ 50,
    > 15 => 500 + 75,
    _ => 500,
},
```

The way we use not pattern with null constant pattern is we can avoid unknown types falling into null bucket

```csharp
not null => throw new ArgumentException($"Not a known Laptop type: {product}", nameof(product)),
null => throw new ArgumentNullException(nameof(product))
```

not is also usefull is-experssion like below

instead of wrinting 
```csharp
if(!(product is LaptopProduct))
```

we can write
```csharp
if(product is not LaptopProduct)
```


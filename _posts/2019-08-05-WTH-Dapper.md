---
layout: post
title: What The Hell Dapper?
date: 2019-08-07T17:46:50+0200
---
Dapper is a nice Micro ORM that handles trivial (also known as error-prone) mapping of database columns.
It is easy to set up, will map based on either constructor parameters or properties and, as long as you are strict in your domain/orm model separations, provides a decent abstraction of a row as a strongly typed .Net object.

```csharp
public class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}

var users = await connection.QueryAsync<User>("select Name, Age from Users")
```
The above will map each users from the `Users` table to one `User` and return an array of the result.
In most cases that is enough.
But then the database model is extended, such that the name is in two columns:

```csharp
public class Name
{
    public string First { get; set; }
    public string Last { get; set; }
}

public class User
{
    public Name Name { get; set; }
    public int Age { get; set; }
}
```

But this is not supported by dapper.
At least not directly.
One option is to have your users name-fields at the end of your select(s), and use [dappers SplitOn feature](https://dapper-tutorial.net/result-multi-mapping) to handle the mapping.

But there are several issues with this approach.
One of them is that in my current context I am trying to generate SQL on-the-fly based on a set of configurations, and this makes it very difficult to properly manage the order of values.
Also, one of the major reasons for Dapper is that the order is abstracted away - which is lost if I have to manage it for this type of query.
All I really want to do is have two values map into one property.
It shouldn't be that hard...

## Just do it yourself

The solution, so far, has been to implement the `IDataRecord` to ORM mapping with a bit of reflection.
The code is not very complicated as all my object relations are defined by the same structure as is used to generete the SQL.
Other than that I really only needed to manage handling of `DBNull` for value and reference types.

For complex objects (in 1:1 relations) the generated SQL will use a naming convention that matches the target model.
This makes the mapping process quite trivial.
The example model above would return the following columns:

| Name.First | Name.Last | Age |
|------------|-----------|-----|
| John       | Smith     | 27  |
| Jane       | Johnson   | 28  |
| ---        | ---       | --- |

The actual mapping is quite trivial (much more so than the relation mapping that preceeds it) and runs *fairly quick*.
Currently the mapping makes extensive use of `IRecordReader.GetOrdinal(string)` with I think hurts performance.
It should however be easy to get rid of, as the order of columns is determined by the SQL generation, which in turn is based on the same config.

Potentially I could just ignore names and read columns in the order they are defined to be in.
The only challenge I foresee in that context is begin strict on the order of fields when the generated sql handles both recursive structures and multiple interconnected recordsets.

I'll get to it, when I get to it.

Or maybe I will look into how this kind of feature could be added to Dapper.
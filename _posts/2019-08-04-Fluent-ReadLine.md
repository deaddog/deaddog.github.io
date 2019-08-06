---
layout: post
title: Fluent ReadLine API
date: 2019-08-04T00:42:32+0200
---

One of the main purposes of the command line library ([here](https://github.com/deaddog/CommandLineParsing)) is to make it easy to read strongly typed values, and to provide the developer with a simple one-liner for performing that operation. In general it looks like this:
``` csharp
var number = ColorConsole.ReadLine<int>( ... );
```
For brevity I will remove the `ColorConsole`{:.csharp} prefix in the following.

The method supports a range of optional parameters which allows a developer to configure how the method should behave.
These parameters are:

- **Prompt:** A prompt for the user writting before starting to consume input.

``` csharp
var age = ReadLine<int>(prompt: "Your age: ");

// Your age: _
```

- **DefaultString:** Similar to above, but is editable and considered part of the users input.
- **CleanUp:** An enum where it can be specified if the prompt and/or input value should be removed before the method returns.
- **Parser:** Allows a custom parser to be used for value parsing.
- **Validator:** A set of validation rules applied to the parsed value.
The method will only return when these are met.

## Methods
There are a total of 9 methods related to the `ReadLine` functionality spanning roughly 260 lines.
I don't think the number of lines will be reduced (they might increase) - but it should still be more maintainable than the current setup.
Just having more descriptive models will go a long way in making the code more readable.

The 9 methods support 5 different usecases:

- `ReadLine<T>`{:.csharp} (the above) will only return when input can be parsed and validated.
- `TryReadLine<T>` works similar to the above, but Escape will cancel the loop.
- `ReadLine` will read string-input but does not apply parsing.
- `TryReadLine` is the try version of the above.
- `ReadPassword` only supports a subset of the parameters and will display `*` instead of the actual password characters.

My goal is to merge all 5 usecases into the same fluent API.
---
layout: post
title: Fluent Formatter
date: 2019-08-10T14:35:40+0200
---

I decided to change focus in the command line project, and work on a different fluent API first.
Specifically the one that handles string formatting - which is a minor declarative mark-up style language embedded in project.
It turned out I had an old branch where I had already started work on the fluent API, and it just felt more natural to carry on in that direction.

### Formatting language
When outputting lists of elements the library allows a developer to define a formatter such that the end user can specify a desired format based on the context of the application.
These layers of meta can at times make it difficult to develop the feature as it is hard to keep track of mentally.
This post is part of getting my head straight.

The language is a single-line, single-pass declarative language for which I have never create a formal [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree).
Part of the work I did on the old branch was to rewrite the parsing, such that result was an AST - instead of evaluating on-the-fly.
Based on the AST, this is the language in [EBNF](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form).

* `<FORMAT> = #text`
* `<FORMAT> = '[' #color ':' <FORMAT> ']'`
* `<FORMAT> = <FORMAT>+`
* `<FORMAT> = '$' '+'? #variable '+'? `
* `<FORMAT> = '?' #condition '{' <FORMAT> '}'`
* `<FORMAT> = '@' #function '{' <FORMAT> (',' <FORMAT>)* '}'`
* `<FORMAT> = ''`

> Note that the notation `#text` is meant to be *any character* which is not used as a special character in other rules.
> When needed, any character can be escaped using the standard `\[` form.
> The naming of these elements is just to provide some context.
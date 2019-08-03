---
layout: post
title: Revisiting old code
---

Today I spent some time on an old project of mine (check it out [here](https://github.com/deaddog/CommandLineParsing)).
The project has (at the time of writing) 19 open issues, most of which are new features.

I've always liked the process of going back to old code, so that I can apply my aquired skills.
It's a nice way of demonstrating to myself that my technical skills have grown, and that I am now capable of solving some of the problems that I was previously only able to describe.

This also comes with the unfortunate experience of being remininded just how poor my code used to be.
Because of that implementing new features often comes with the added cost of rewriting large parts of the codebase.
Just so the old code is closer to the level that I expect the new to be.

This is not boyscouting - it's more like construction work, where I tear down the old to make room for the new.

#### What's next?

Specifically I want to rewrite large parts of the project as fluent APIs which should make it easier both to maintain the code and to apply it in other projects.

First up is a designing an API for ReadLine<T>() which is a set of methods for reading and parsing typed data from the console.
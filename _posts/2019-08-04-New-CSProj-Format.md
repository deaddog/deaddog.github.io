---
layout: post
title: Changing .csproj format
date: 2019-08-04T14:00:38+0200
---
I really like the newer .csproj format, which is much leaner and actually readable by humans.
Specifically I like not having to include every file I want to part of the build/project.
Because of this (and other related configuration in the files) the project files tend to shrink quite a lot when converted.

Yesterday I [converted a project](https://github.com/deaddog/CommandLineParsing/issues/38) to .Net Standard and in that process converted all project files to the new format.
This replaced aproxx 400 lines of configurations (that are hard to read) with aproxx 30 lines (that are much easier to read).

All in all, a nice transformation.

### Contiued work

Today I wanted to continue work on new features.
So I switched to an old branch and tried to continue working in VS.
Doing so reloaded the old format, something which VS is apparently not capable of handling.
So now I've decided to rebase all the old branches, to get them up to date.

I hope I'll get to write some code soon...
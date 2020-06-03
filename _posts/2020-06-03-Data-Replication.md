---
layout: post
title: Data Replication
date: 2020-06-03T06:50:20+0200
---
How much detail should you include in your various data representations?
Generally speaking this is a question about how to avoid transferring and/or storing the same information multiple times, versus how simple it is to work with your services.
I have been thinking about how I normally approach this issue, and I think my mindset has been:

> _Use the representation that has the least redundant data - it is the responsibility of the client to retrieve and manage any data dependencies_.

This ensures no redundancy and allows clients to retrieve just the information that they require.
It's a purists approach to modeling.
The all-or-nothing approach where an effort to avoid publishing a massive complex model structure results in minimalistic structures that every client has to re-assemble in every context.

#### Context matters

I don't really think my approach is *bad*, it's just narrow-minded and a reflection of my experience.
I have seen massive complex data structure being exposed from services that were completely resource-drained because of it.
To avoid that issues resources have been split into their base component and exposed separated.

This is clearly quite well related to the type of storage that the service manages.SQL will typically favor the single-representation with explicit data relations using some unique keyset.
In contrast noSQL will _typically_ favor data-replication, allowing you to only grab a single document at a time.

So naturally my SQL based service will expose data in the same kind of structure, right?
Well I know that that is a poor argument.
You should design your models based on client needs.
Regardless of whether the client is front-end or some middle-tier service.

## So what is the _"right"_ approach?

Naturally it can be hard to design for *any* use by *any* client - but then how do you approach this issue?
I think the challenge is somewhat similar to thinking of algorithms just in terms of worst-case/best-case [big-O notation](https://en.wikipedia.org/wiki/Big_O_notation).
You really should be thinking of the average case as well.
What is _normal use_?
With that perspective in mind, I have reprased the above definition:

> _Use **a** representation that includes the sub-structures that are **most likely** going to see use in **most cases**_

Note specifically that this states _a representation_, indicating that there is no exact target to optimize for.
With this approach you are not able to look at a single field and determine if it should be included or not.
You have to consider all the contexts where the field could see use, those where it would not, and finally think about how common each of these are.

I don't know if this is _**the**_ approach - but it's one that I will try to take going forwards.
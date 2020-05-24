---
layout: post
title: The Blazor Experience
date: 2020-05-24T21:29:15+0100
---
During the last 8 months I have been working on a new version of an old tool.
Essentially one for managing a shared shopping-list, with a strong relation-ship to recipes and a meal-plan.
The domain itself is not that important in this context.
What has been important is the Ui/application built for this tool.

Due to the the platform choices made by my users I *have* to support iOS, Android, Windows and OSX devices.
Yes - two people, four platforms.
So either I had to build custom front-ends for all of those, or just a single SPA that they can all use...

## Picking a SPA Ui framework
It should be noted upfront that I don't particularly enjoy front-end development.
It's tedious, error-prone (users...), and typically doesn't provide many opportunities for diving into algorithmic or modeling complexity.
Those are typically what drive me when developing something new.

So...
How does one approach an SPA?
There are many options available, but mostly you just use [Angular](https://angular.io/).
It is tried and tested, comes with some nice getting-started guides, and lots on online-resources for additional knowledge.
There are also many pre-existing component libraries available (such as [Angular Material](https://material.angular.io/)) that allows me to ignore many of the typical Ui challenges.

The downside is, that I'm essentially forced to use Typescript.
Though it's a nice upgrade from plain Javascript, it is still only a [somewhat-typed](https://www.typescriptlang.org/docs/handbook/type-compatibility.html) language with it's various shortcomings.

I've writtin multiple a few Angular applications before, and the experience has been *ok*.
But I don't really want to get back into an unknown stack - *just* to do front-end.

## Along Came Blazor
Back when I started this version of the project, I picked up [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor).
It's server-side deployment had been released, and it allowed me to make use of the C# language, and the familiar stack.
Server-side essentially just means that a remote services manages a DOM representation per client and sends/receives updates via [SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr).
Additionally it's supposedly easy to switch to the pre-release client-side, if you can tolerate it's potential shortcomings and bugs.

Using the open source [MatBlazor](https://www.matblazor.com/) for Ui components, I decided to give server-side Blazor a shot.
Eight months later, these are my thoughts.

### Structural challenges
I wanted complete separation of front-end (running server-side) and back-end (also running server-side), whilst running both in the same service.
To do that, I ensured that all communication was handled as OAuth authenticated http requests.
With this approach I had a clean separation between front-end and back-end.
The solution structure is similar to the one below:
Repository available [here](https://github.com/deaddog/Food).

```
Food.Host
- Food.Domain
- Food.Dtos
- Food.Api
  - Food.Api.Domain
  - Food.Api.Data
  ...
- Food.App
  - Food.App.Domain
  - Food.App.Components
  ...
```
A single host project loaded both applications and each their DI (Autofac) container.
This meant that neither application depended directly on the other.
Only Dtos types and a generic understanding of identifiers were shared amongst the two projects.

This worked quite well, except for some of the blazor server-side shortcomings...

### It's slow
Honestly this was to be expected.
Requiring constant interaction with a server - especially as you are not in direct control of the interaction - should be slow.
I was ready to accept that, as the cost in reaction-time was not *that* high.
Unfortunately I didn't foresee how much of a hastle http would be.

A single http request would go through the following steps:
1) User interaction triggers watched event
1) Event is passed to server over SignalR
1) Server processes and want to make http request
1) SignlarR request for Access Token from client storage
1) Access Token returned over SignalR
1) Http request sent to Api
1) Http response requires additional request (go to 4)
1) Send DOM updates to client over SignalR
1) Changes on client-side invoke additional change (go to 2)

Instead of just reading the token and sending the request, there is a bunch of overhead on every single request.
The list is even longer if the access token needs to be refreshed.

This all works, and the speed is tolerable - but it does slow everything down, and adds to the complexity.

### Poor Exception Handling
The default approach to exception handling in a .Net api is that the exception is caught and a 500 status code is returned.
There is **no** Blazor equivalent of this behavior.
None.
At all.

Whenever an exception is thrown everything just breaks.
Not just does the client receive a message that it cannot contact the backing service, but the service itself dies completely.
I accept that I should handle these exceptions, and that they should theoretically never reach the client.
Unfortunately this structure means that I have no means of globally logging which exceptions are thrown (or where) and the only documentation I have foudn suggests added `catch` to every single piece of client-code that *might* fail.

So periodic issues would cause everything to crash.
And since a single service manages all users, it would crash the application for all users.
This is just poor design.

### Always-On Frustrations
A typical application runs on the users device, making requests against some (possibly REST) back-end.
As Blazor server-side essentially runs the front-end on a server, it requires a constant connection.
On some devices (mainly iOS) browser activity is stopped, as the browser application is closed.
Though it is annoying, it is quite sensible behaviour - which means that the user has to manually reconnect every time the page is opened in the browser.
Even if it is the currently active tab.

Blazor will attempt to re-connect automatically, but even when it succeeds the user is forced to wait 5+ seconds.
That's a frustrating experience, when compared to client-side front-ends that are just readily available.

### Awful Developer Experience
Finally, the developer experience.

I really enjoyed being able to use the C# language along with the familiar .Net framework and stack.
It saved me a lot of work and allowed me to share some nice generic definitions between the two parts of the service.

The bad experience was focused around the Ui itself.
Any kind of change to a components template required stopping the VS debugger, making the change, recompiling, starting the service, manually finding the part being developed and checking it.

Quickly this turned into using the Chrome developer tools to *get it right* and only then stopping the service to update the template.

Additionally, as this is mainly a mobile app, I really wanted to try my app on a mobile device.
Unfortunately the default hosting mechanism ([IIS Express](https://docs.microsoft.com/en-us/iis/extensions/introduction-to-iis-express/iis-express-overview)) does not support bindings to anything but ```localhost```.

I could opt for a self-hosting service - but I just couldn't be bothered.
These things should really be out-of-the-box.

This last part especially compared poorly to the Angular experience.
Running an Angular app, with live rebuilds and bindings to any IP is as easy as running
```
ng serve --host 0.0.0.0
```

It's easy.
And it really should be.

## Now what?
I am in the process of re-building the App using Angular.
I know that will introduce other frustrations, but it should give me valid grounds for a comparison.

Also... [Blazor client-side](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) has been released.
It should solve many of the above issues as well...
But for now I'll focus on Angular.
---
title: Technical Underpinnings
---

For the technically curious, here's what I have running under the hood.

## Backend

The backend is written in Haskell with the Yesod framework. I've used this
framework in other projects, and I like the development server that comes with
it. Any time I make changes, the correctness of my changes are enforced by the
compiler. The incremental compiler isn't lightning fast, though it at least
feels much faster than the incremental compiler I used in a previous Scala
project.

Most of my Haskell work in this project involved wiring things together (which I
suppose is the case with most web projects). My point here is that at no point
did I have to reinvent any wheels. I found solid libraries for everything I
needed to do. Scheduled jobs are handled with `cron`. Serialising to JSON is
done with `aeson`, and for CSV I have `cassava`. I'm interfacing with Redis
through `hedis`. All the date stuff is done with `time` and `iso8601-time`. If
anyone were to suggest the package ecosystem for Haskell isn't up to scratch,
they'd be flat-out wrong.

Haskell's strong type system means I can quickly reorganise the code without
having to write and rewrite isolated tests, and I also don't have to think quite
so hard about how to name each component. Normally we have to put a lot of
thought into what a function name describes about that function's behaviour,
parameters, and return values. In Haskell, all of this is encoded in the type
signature.

In a dynamic language, refactoring code feels like that scene in Indiana Jones
when he has to carefully swap his bag of sand for the Golden Idol. In Haskell,
refactoring feels like painting-by-numbers. You could do it while drunk.

## Frontend

The user interface — or at least the dashboard — is written in Elm. I picked
Elm for much the same reason that I chose Haskell: I can't tolerate runtime
errors. I've heard proponents of the language claim that Elm is not a sort of
_Diet Haskell_, but I think in a way it sort of is.

If you're comfortable with Haskell, the lack of typeclasses in Elm starts to
feel a little bit clunky, but it's still worlds apart from cobbling things
together with JavaScript.

It's also quite telling that the forefront of JavaScript development in our
industry today is essentially a direct copy of the Elm architecture.

## Persistence

I'm using Redis to store company data mostly because it supports georadius
queries. It also helps that queries are incredibly fast because all the data is
held in memory. I've heard concerns that Redis can be problematic because
there's a potential for data loss, but that doesn't really matter in my case.
I'm fetching data from Companies House every day, and if any data is missing it
can be quickly downloaded again.

Any data that I can't afford to lose is persisted in PostgreSQL.

## Infrastructure

I'm monitoring the site with a combination of ekg-carbon, Graphite,
Collectd, and Grafana. I'm deploying to a small DigitalOcean machine with a tool
called Keter which essentially compiles the entire application into a tarball
and uses `scp` to copy it over to the server.

The binary for the application is compiled locally on a virtual machine in
Vagrant. If I ran Linux natively on my physical machine, compilation would
likely be much faster. It's not a problem either way; compiling and deploying
takes about three minutes.

## Thoughts

The primary technical goal is for users to never see runtime errors, which is
the driver behind the choice to use both Haskell and Elm. Incidentally, Haskell
and Elm are faster than what I would have ended up with had I written the
system in say, Ruby and JavaScript for example.

Not only is the performance far better at runtime, but my own performance is
better during development. Because all of my code is checked by a compiler (on
both the backend and frontend) it means I can quickly change the system to
adapt to users' needs without introducing lots of bugs.

Even after using Haskell and Elm on a number of projects, I'm still impressed by
how quickly I can ship software that I have confidence in, and having confidence
in the system is crucial. Being able to quickly ship _broken_ software is
meaningless.

If I could do it all again, would I make the same technology choices?

Emphatically, _yes_!


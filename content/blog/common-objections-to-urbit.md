+++
title = "Common Objections to Urbit"
date = "2016-06-27"
description = "Some common objections to Urbit, discussed."
aliases = [
  "/posts/essays/common-objections-to-urbit",
  "/posts/common-objections-to-urbit",
  "/posts/objections/"
]

[extra]
author = "Curtis Yarvin"
ship = "~sorreg-namtyv"
image = "https://storage.googleapis.com/media.urbit.org/site/posts/essays/common-objections-to-urbit-1.jpg"
+++

![](https://storage.googleapis.com/media.urbit.org/site/posts/essays/common-objections-to-urbit-1.jpg)

We love crowdsourced criticism. We've heard a lot of great
objections to Urbit. Surely there's more out there! We'll
revise this document to include anything new and interesting.

Here's our list right now:

- [Urbit is not ready for prime time.](#prime)
- [Nock jets are a total hoax.](#jets)
- [Urbit needs an infinitely extending packet/event log.](#log)
- [Urbit is a total scamcoin. It's 100% premined.](#scam)
- [Urbit isn't even really decentralized! It has a government!](#gov)
- [Sandstorm/IPFS/Ethereum is way ahead of Urbit.](#sand)
- [Urbit doesn't have enough planets for every human being.](#planet)
- [Cloud computing is inherently non-private.](#pwned)
- [Urbit will never be able to call any existing code.](#code)
- [Hoon is too weird. No one will be able to program in it.](#program)
- [Urbit programmers are stuck with Hoon.](#stuck)
- [Ordinary users will never be able to manage an Urbit server.](#user)
- [Urbit has no killer app.](#killer)
- [Urbit is sniffed at by Houyhnhyms.](#horse)

_"We choose to go to the moon, not because it is easy, but because it is hard."_
— [John F. Kennedy](https://www.youtube.com/watch?v=g25G1M4EXrQ)

## <a name="prime"></a>Urbit is not ready for prime time.

_TLDR_: we agree.

Urbit right now is a fun toy. It is fun to learn Hoon. It is
even fun and quite practical to make actual contributions.
(Hoon's IEEE floating-point was written by someone we've never
met: `~nym`, formerly `~nodpet-rinned`.) In fact, we have never
seen anyone _not_ have fun with Urbit.

But there is all kinds of stuff wrong with the codebase. Urbit's
documentation is much improved, but remains incredibly weak --
Arvo is almost totally undocumented. Scaling is poor at best.
Urbit's reliability is dubious. Its security is nil.

Urbit is feature-frozen. The only thing we're working on now,
and for the rest of 2016, is doc, debugging and and self-hosting.
In that order of importance -- teaching Urbit is more important
than fixing it, which is more important than making it useful.
But we're quite confident that these are all solvable problems.

The most important milestone about Urbit today is that it's ready
for technical decisions to be made collectively. A system like
Urbit can only be invented by a mad auteur. It can't be
maintained or stabilized by a mad auteur. Our primary goal today
is expanding the number of people who understand Urbit, and we
expect these pioneers to participate in technical governance.

## <a name="jets"></a>Nock jets are a total hoax.

_TLDR_: all of computing is a hoax. You can't compare a hoax
to nothing. You can only compare it to all the other hoaxes.

A "jet" is a sort of software device driver in Urbit. Urbit is
precisely defined in terms of an interpreter that fits on a
T-shirt, Nock. But a naive implementation of Nock is nowhere
near practical, because Nock has only one integer operation:
increment. For example, to decrement a number in Nock, you
increment a counter up to the number under it.

The only way an efficient Nock interpreter can work is to
recognize algorithms like decrement, and implement them
non-naively. By far the easiest way to do this is not to analyze
arbitrary code as it's being executed, but to register and match
well-known implementations in the standard library. No one
should be writing their own decrement.

But the semantics of the interpreter, _so long as the jet and the
jet harness are defined correctly_, are exactly those of Nock.
We are not extending or changing Nock in any way. We are just
taking a shortcut in certain cases we understand perfectly.

It's not at all unprecedented to have implementations much more
complex than the corresponding specification. The x86
instruction set is relatively simple; the complexity of its
decoding and execution path in a modern CPU is enormous. This
path may well have "peephole optimizations" that replace matched
algorithms, and if it doesn't the modern compiler does. People,
_bad_ people, use "jets" to cheat on benchmarks all the time.

Nock is designed as a standard. It's built like a standard.
Typically what causes real problems in standards is ambiguity.
Two implementations are incompatible, but each swears it has the
right interpretation of the natural-language spec. This simply
cannot happen in the Nock world. If two implementations
disagree, at least one of them is incorrect. This puts us firmly
in "with enough eyeballs, all bugs are shallow" territory.

The fact remains, however, that a Nock interpreter without jets
is completely useless. And there is not even an informal
document that tells you what jets you need for a reasonably
efficient Urbit. (There should be such a document.)

The correct way to think about jets is not to compare them to a
perfect interpreter created by unicorns. Jets are a combined
replacement for two other techniques in language implementation:
intrinsics and a foreign-function interface.

Compared to both intrinsics and an FFI, your jets have one key
disadvantage: they need to be specified in pure code. You may
not have this executable specification, which means you need to
write it. Is this really so bad for your development process?
An executable specification? Which can be trivially tested
against the efficient implementation?

Compared to intrinsics, jets give you true separation between
mechanism and policy; only the interpreter, not the programmer
and not the compiler, needs to know what's accelerated.

Compared to an FFI, jets preclude I/O, so can be trivially
sandboxed. Except that the programmer needs to mark routines
which may benefit from optimization with a hint, here too we
separate mechanism (efficient implementation) from policy.

Yes, formal verification of jet equivalence is desirable.
However, in the real world, many specifications, including quite
complex ones, are tested into equivalence by brute force.

Urbit has some jet abuses. The equivalence between our markdown
parser in Hoon and the CommonMark C library is super dubious. It
should be revisited.

But the miscomputation resulting from a minor jet discrepancy is
not a security hole or even fatal. It effectively means the
event history, computed correctly, does not match the current
state. The way to resolve this conflict is for the current state
to win, and the event history to be discarded -- not abnormal
anyway. See the next question.

## <a name="log"></a>Urbit needs an infinitely extending packet/event.

_TLDR_: no, it doesn't. Urbit can prune the log.

We can prune the event log because the Urbit transition function
doesn't have any access to the event history -- just the current
state and the current event.

Urbit defines the semantics of a complete event log. It doesn't
and can't require an implementation to maintain the whole log.
It can maintain a checkpoint and the log since that checkpoint.

However, any practical urbit maintains a periodically updated
checkpoint. Our present implementation doesn't provide a way to
prune the event log. But that's just a missing feature. We have
one or two of those!

## <a name="scam"></a>Urbit is a total scamcoin. It's 100% premined.

_TLDR_: yeah. But at least it's not the DAO.

Like a cryptocurrency, Urbit is a digital title. But Urbit is
digital land, not digital money. If you assess it as a currency,
it's a lousy currency. If you assess a cat as a dog, it's a
lousy dog.

In real-world economics, the crucial distinction between land and
money is that land (a) has intrinsic utility and (b) is
non-fungible.

Because of (b) alone, frictionless transactions in land are
impossible. A blockchain is only needed to ensure zero-cost
high-frequency transactions. So digital land doesn't need a
blockchain. Which means it doesn't need mining.

Of course, just because a digital title system doesn't need
mining, doesn't mean it can't distribute an initial allocation by
proof-of-work -- or by any other arbitrary method which is
careful not to benefit the system's own developers. Could we
give away the Urbit address space randomly? Sure. But why?

Probably the purest design is the "proof-of-burn" initial
allocation used by some coins. By any standard, proof-of-burn is
technically beautiful and results in an extraordinary level of
ethical independence. We can't help but admire it.

But any altcoin or metacoin is just way less work than Urbit. An
altcoin can afford to burn even its creation energy, capturing no
value at all from its seigniorage. Your altcoin doesn't have a
clean-slate system software stack to drive. If your rocket has
to go to the moon, it's going to leave a little bit of smoke in
the air.

Ethically, what makes "premining" feel wrong is that the creators
of the currency are rewarded disproportionately for their effort.
The scamcoin is a get-rich-quick scheme, vaguely pyramid-shaped.
Nothing about Urbit matches this pattern at all.

Curtis invented Urbit. So by the [homestead
principle](https://en.wikipedia.org/wiki/Homestead_principle), he
started out owning all of it. But by now, Urbit's 8-bit galaxies
have already been distributed among [57 separate individuals](https://github.com/urbit/arvo/blob/master/sys/vane/ames.hoon#L118-L373).
Curtis owns 20, or about 8%.

Tlon, the corporation which still runs Urbit development, has 95.
(Without Tlon, there is basically no Arvo.) 50 are reserved for
a hypothetical Urbit Foundation. Tlon employees have another 16.

The rest of the galaxies belong to donors, investors,
contributors, contest winners, and just random people who were in
the right place at the right time. One is a Silicon Valley
tycoon. Another is an Orthodox nun in a monastery in Greece. A
couple are ex-Tlon employees who might totally hate us. There is
nothing they all have in common, which is sort of the point.

This property distribution looks a lot like land ownership in the
real world -- complex and asymmetrical. That's no accident. If
you have a problem living in New York because New York's real
estate is not distributed equally among its residents, you may
have a similar problem with Urbit. The reality is that Urbit,
like New York, wasn't born yesterday and comes with a complex
ownership history which we couldn't possibly change, even if we
wanted to.

Decentralization is hard. The only real goal of a property
system in address space is to create and maintain
decentralization. The whole network has to exhibit centrifugal
force, not centripetal force. Proof-of-work systems certainly
seem to have unexpected centripetal qualities, producing
unanticipated concentrations of centralized hashpower. See the
next objection.

## <a name="gov"></a>Urbit isn't even really decentralized! It has a government!

_TLDR_: it's technically impossible for Urbit to be
decentralized at this stage of its life. Urbit is designed to
achieve decentralization, not be born with it.

A young network can't afford to be decentralized. It has to act
as a unit. Because it has no network effect, it needs every
scrap of efficiency it can get.

A decentralized general-purpose computing network like Urbit can
_only_ be bootstrapped by a central government. For example, by
default your urbit upgrades its OS automatically (like an
"evergreen" browser) with signed network updates. Whoever signs
these updates is a government by definition.

When it's impossible to eliminate centralized power, it becomes
necessary to tame, stabilize, and limit it. This should not be
surprising to anyone living under a constitutional government.

As we found out with the DAO, if a network isn't ready to be
decentralized, its developers shouldn't even try. If Ethereum is
hard-forked to roll back the DAO (which looks like the most
probable result), it acknowledges that it both has a central
government, and needs a central government. Decentralization is
good. Pretend decentralization -- "decentralization theater" --
is harmful, and not a effective path to actual decentralization.

A key test of a free software project's health is whether it can
fork. So long as the unity of the public-key hierarchy and the
network protocol is maintained, the Arvo kernel and Urbit system
can fork in all kinds of ways without disrupting the network.
Any stress will fragment the "government" that develops the
kernel, and as it fragments it grows more limited and stable.

Urbit is designed to govern itself collectively by address space
ownership -- effectively, "proof of stake." Any arbitrary power,
will tend to fracture along these property lines. For instance,
our hot updates are sent through the galaxy hierarchy; in a
development fork, active galaxies would pick upstream sources.
Two implementations of a standard are better than one.

And by fragmenting the galaxy space into 57 separate partitions,
we've created a global property map which is much more likely to
splinter further, than reunite.

In the long run, every galaxy should have a separate owner, who
is a human being rather than a corporation. Combining galaxy
holdings will damage the reputation of both galaxies. The only
reason to merge ownership is to move toward incipient monopoly.
Whether a galaxy admits this openly or denies it dishonestly, its
reputation will suffer.

The technical goal of Urbit is to create a network which, _if it
actually takes over the world_, inevitably becomes decentralized.
Even if Urbit takes over the world, no individual or coherent
organization takes over the world. There is nothing even close
to a single point of failure in a mature, successful Urbit.

This is the goal of all the new "web3" decentralized systems. If
IPFS, Sandstorm or Ethereum takes over the world, nobody takes
over the world. (The same is almost true of the Internet --
though it wouldn't be that surprising if a time traveler from
2116 revealed that ICANN had become a planetary government.)

Urbit, by starting out as a centralized network, just reduces the
technical problem to match the actual goal. Only long-term
decentralization matters.

It's very important that Urbit in 2116 be fully decentralized.
For Urbit in 2016, pretending to be decentralized would be a joke
at best, and a hoax at worst. And of course, no one can DAO us
-- since the property system is still just a central registry.
Among other things, we check it into GitHub. GitHub is not a
blockchain -- but still, good luck with that.

## <a name="sand"></a>Sandstorm/IPFS/Ethereum is way ahead of Urbit.

In general, web3 (next distributed web) solutions don't compete
with each other. They support each other. Concretely, they are
all part of each others' network effect.

IPFS, Sandstorm, Ethereum, and Urbit can all take over the world
at the same time. None of them competes with the others. They
enable each other.

For instance, IPFS and Urbit are both global immutable
namespaces. IPFS is a chord-routed, content-addressed namespace.
Urbit is a direct-routed, identity-addressed namespace. Urbit is
a typed revision-control system; IPFS is a blob store. IPFS
isn't a computer; the work hasn't been done, but Urbit can easily
mount the IPFS namespace and compute functionally against it.

So the better IPFS does, the less Urbit needs to struggle against
its lack of network effect. The better Urbit does, the easier it
is for IPFS to point to a functional language that can actually
use its referential transparency directly. So their relationship
is symbiotic, not competitive at all.

The closest thing to a general-purpose competitor that Urbit has
is Sandstorm. Sandstorm, which by the way rocks, is also a
personal server built around a network protocol.

But Sandstorm and Urbit compete in the way that, on the PC, the
OS and the browser compete. In theory, the OS could solve the
problems that the browser solves; or the browser could be the OS.
In practice, Windows is a great OS to run a browser on.

Similarly, a world in which Sandstorm takes off is an awesome
world for Urbit, because everyone who has a Sandstorm instance
has a great platform for running Urbit on. Urbit on Sandstorm
will be a sort of "double-hulled" personal server, safer and more
friendly than either system alone.

Sandstorm and Urbit have very different approaches to network
identification; Sandstorm uses capabilities, Urbit identity.
Broadly speaking, we believe, capabilities are optimal for
expressive power; but identity is optimal for the UI of the
common case. We'd need to see the Powerbox, Sandstorm's UI, more
fully fleshed out to understand its UI approach better.

## <a name="planet"></a>Urbit doesn't have enough planets for every human

A 32-bit planet is a tool, not a toy. Like a car, it's a device
for a responsible and independent adult. There aren't 4 billion
cars in the world, nor 4 billion independent adults.

If you aren't an independent adult, and you don't need or even
shouldn't have unconditional digital freedom (no one's 8-year-old
daughter needs unconditional digital freedom), a moon from
someone else's planet is fine. (Even most of today's independent
adults don't complain enough about being Facebook's moons.)

And limiting the supply of anything doesn't create a shortage.
It sets a price. Ideally, in the long run, we think a planet
should cost about $10. This could be off by an order of
magnitude either way, and it wouldn't be the end of the world.

If a planet costs $10, that means you paid $10 to get a planet.
A planet with no reputation has a positive reputation, because
someone paid $10 for it and hasn't yet done anything wrong.
Positive default reputation is the single quality that makes a
social network social.

There is always a tradeoff between free speech and free beer.
Urbit will always choose free speech.

## <a name="pwned"></a>Cloud computing is inherently non-private.

_TLDR_: a general-purpose cloud computer is much, much more
private than a special-purpose cloud computer. In general, a
cloud VM is secure against corporations but not governments.
With a global adversary, compute in your closet -- or even on
your body.

One reason we want the decentralized Internet is that we're tired
of giant corporations spying on us. Think about it this way:
Amazon is Netflix's main competitor. And yet, Netflix runs its
whole operation on AWS. Why is Netflix sure that Amazon won't
spy on it?

A general-purpose virtual machine is special from a privacy
standpoint, just because it's commercially realistic to design
terms of service that prevent the host from peeking and poking at
the customer's machine. Crossing this moat is technically
nontrivial and can't possibly happen by any reasonable accident.
No hosting contract could contemplate it.

Whereas when your cloud computer is a special-purpose machine --
just a database row on some corporation's mainframe -- there is
no way these services can promise not to handle your data.
Handling your data is _what they do_. On a special-purpose
server, the concept of privacy is not even defined.

Where even a general-purpose server falls down is where the user
has a sovereign adversary, which can secretly violate explicit or
implicit hosting contracts. Users with a sovereign adversary
must rely only on their own physical security.

However, most users don't have a sovereign adversary. And those
who do maximize their "herd immunity" if they can use the same
protocols as those who don't. Otherwise, the adversary could
simply focus on everyone who uses the secure protocols.

Also, the portability of a cloud server image is critical. Of
course, since Urbit is formally specified, it's trivially
portable. In fact, one strategy against sovereign adversaries
may be continuous rotation between hosts, like an MX missile.

## <a name="code"></a>Urbit will never be able to call any existing code.

_TLDR_: neither will Javascript. And dear Unix, the browser
does not miss interacting with your `$LD_LIBRARY_PATH` at all.

You can't talk to the OS or use system libraries from Javascript.
Therefore, there is no point in having any kind of gateway,
compatibility bridge, native methods, or other interface between
Javascript and C.

When in doubt, remember that Urbit is "the browser for the server
side." It plays by the same general rules. Isolation means
isolation.

## <a name="weird"></a>There's no way Urbit will ever be adopted. It's too weird.

_TLDR_: Urbit adoption is designed to be driven by ordinary end
users. Urbit isn't ready for ordinary end users yet. We're
working on it -- come help.

Right now, Urbit is a developer tool. Or rather, a developer toy
-- since the only thing it's useful for is developing Urbit. It
also could be a much better toy. We're working on that too.

But it's absolutely wrong to think of Urbit being adopted as a
developer tool in the conventional sense. If you think this is
almost impossible, you're right.

If we look at the way most successful platforms are adopted,
they're not adopted for their own virtues. They're adopted
because they're part of a larger solution. It's very hard to
displace a native component of an existing platform. Urbit is
not trying to do this.

C is a better language than Pascal, but it didn't beat Pascal
because it's a better language. C beat Pascal because C was the
native language of Unix, and people wanted to program in Unix.
People don't use Javascript because Javascript is a great
language, but because it's the native language of the browser.
Replacing the native language of a platform may be doable -- but
it's incredibly hard.

Hoon is not competing with C, or even Haskell, as a Unix
programming language. The only reason to program in Hoon is that
it's the native language of Urbit. Urbit is not competing with
node.js or Rails as a Web backend. The only reason to run Urbit
is to run a personal server.

Nock is cool, but it only gets adopted if Hoon gets adopted.
Hoon is cool, but it only gets adopted if Urbit gets adopted.
Urbit only gets adopted if it's useful as a personal server for
human beings -- most of whom won't be Hoon programmers, or
programmers at all.

## <a name="program"></a>Hoon is too weird. No one will be able to program in it.

_TLDR_: Hoon is ridiculously simple. Our documentation and
tooling are just immature.

It's pretty fair to assume that (a) functional programming is
hard and weird, (b) Hoon is weird even for a functional language,
so (c) Hoon must be hard-squared.

Actually, Hoon is weird even for a functional language because
it's an attempt to remove the hardest things about functional
programming: category theory, lazy evaluation, and gratuitous
abstraction. To know a language is to know what the compiler is
doing; all the semantics of the Hoon compiler fit in 2000 lines
of code.

It's too easy for us to wind up living in Isaac Asimov's Trantor,
a world in which everything got invented thousands of years ago
and the idea of new technology is just weird and scary. History
isn't over yet, and nor is technical history.

New things are harder to learn than old things. We ask smart
high-school students to learn calculus. Think about how much
harder it was for Newton and Leibniz to invent calculus, or for
their first students to pick it up. Hoon is nowhere near as hard
as calculus.

Most of the difference is just the collective certainty that our
society conveys to a high-school student that calculus is
learnable. When you're learning Urbit, you don't have this
collective knowledge; so you don't have the confidence that the
problem is solvable. Confidence makes a huge difference.
Moreover, any bug or glitch in the developer experience destroys
this confidence and takes a long time to heal.

Yes, we know: Hoon remains quite hard to learn. Again, we have
no reason to think this problem isn't solvable. Public
enlightenment remains our main priority.

## <a name="stuck"></a>Urbit programmers are stuck with Hoon.

_TLDR_: patience, grasshopper.

There's quite a straightforward way to implement any perfectly
specified language with a reliable interpreter in Hoon. First,
write an interpreter for that language in Hoon. Second, jet that intepreter function with the existing implementation.

It is not clear that Javascript is _quite_ well-defined enough
for this treatment. But Lua probably is.

## <a name="user"></a>Ordinary users will never be able to manage an Urbit server.

_TLDR_: Urbit's technical simplicity has always been designed
to drive human usability. But the polish is the last thing that
goes on. So we can't evaluate this claim right now.

Right now, Urbit is only for geeks. You already need to be able
to wrangle a Unix machine to run it. What's the point?

This Urbit isn't the real Urbit. It's an early milestone on the
way to the real Urbit.

The real Urbit -- for normal human beings -- is a hosted service.
Getting a planet is as easy as signing up for Facebook. Probably
many users will think of it as another web account. They might
not even know it can run more apps than the ones they use.

A deterministic, persistent cloud computer is something different
from a traditional server. Normally when we think of a server in
the cloud, we think of it like a live animal -- a creature that
needs to be cared for, fed, and herded. A file in the cloud is
more like a rock -- you can forget about it for ten years.

An urbit is a general-purpose computer, but it doesn't even have
the concept of a reboot. It upgrades itself. If you don't feed
it any packets for ten years, then turn it back on, it will spend
a day or two upgrading itself, then be back to normal. Maybe
it's something in between a rock and an animal -- a cactus?

Another huge difference versus an Internet server is that we're
confident that Urbit's limited supply of identities will make it
easy to keep abusers off the network. So far, so good -- but of
course, no one abuses a young network.

The bottom line is that managing your urbit has to be as easy as
managing your iPhone. Normally, it consists of deciding what
apps you want to be running, and how much you trust them.
Resources can't be free, so sometimes you have to play memory
police. Humans have shown that they can handle these tasks.

Our '70s OSes and networks, which were great designs in their day
and have matured into excellent industrial tools, give us a
totally misleading impression of how hard it should be to manage
a general-purpose computer. Computing isn't scary or dangerous.
Unix and the Internet are scary and dangerous. That's fine for
an industrial tool, but not a personal tool.

## <a name="killer"></a>Urbit has no killer app.

_TLDR_: the killer app is personal API aggregation.

It's a truism that any new platform has to be fundamentally much
better at solving some problem than the mature platform it seeks
to replace.

We can look at Urbit's killer app from two perspectives: as a
developer, and as a user.

From the developer's perspective, Urbit's advantage is that it's
a network stack which provides exactly-once delivery of typed,
validated, transactional, encrypted and authenticated messages.
The difference between an Urbit API and a REST API is like the
difference between a paved road and a dirt road. On the other
hand, you do need to learn a new functional language.

From the user's perspective, there's one task that any personal
server can perform, that can't be performed by any Web service:
unrestricted API aggregation.

Well before Urbit is ready to _replace_ any conventional web
services, it should be ready to _command_ them in place by API.
(Or if needed, by scraping.) If all you care about is being in
control of your own data, it's fine to leave it in the hands of
these services, as you have the API secrets to get it out.

Managing a set of security connections to your existing services
is one of the first important tasks of a modern personal server.
This is a stateful task that typically requires both a durable
API key and a temporary OAuth token. APIs change all the time,
so connection maintenance also requires evergreen updates.

The origin of this API key will vary, but usually the best way to
set it is BYOK: the user registers their own urbit as an "app."
And the neat thing about having your own API keys, for multiple
services, on a single general-purpose computer, is that none of
these services really has any practical way to control what you
do with your own data.

Terms of service which constrain the use of data (as opposed to
the abuse of the service) simply aren't realistic and will no
longer apply. This enables a variety of novel user experiences
which aren't implementable as traditional web services, and also
aren't available via conventional third-party API aggregators.

Also, since Urbit is after all quite immature, it doesn't make
much sense to restrict this security state to code within Urbit.
The Urbit interpreter `vere` runs as a Unix daemon that
implements the FUSE user-level filesystem protocol, and maps the
data on your services into the Unix filesystem. For example, one
of our demos lets you `ls` your GitHub issues.

So it's straightforward to build, for instance, a Python app that
uses Urbit as a gateway to mash up a bunch of web services. As a
Unix programmer, you might even just regard Urbit as a generic
Web API gateway daemon, without worrying about its internals.

We have other ideas for initial killer apps. But this, we feel,
is the best one. API libraries for `npm` et al abound.
Stateful, self-updating, generic API aggregators with FUSE
integration, also integrated with a reactive web server, are
rarer.

## <a name="horse"></a> Urbit is sniffed at by Houyhnhyms.

It's difficult to categorize [this awesome
essay](http://ngnghm.github.io/blog/2016/06/11/chapter-10-houyhnhnms-vs-martians/)
in a single bullet point. So I've saved it for last.

When reading this essay, it's important not to forget the amazing
absence of genuine, mutually critical discourse in computer
science (as in most sciences today). You never hear what Swift's
designers think of Go, or vice versa. And critical public
discourse decreases, not increases, with increasing academic
legitimacy.

The basic test of any field purportedly scientific in nature is
whether its researchers are genuinely interested in each others'
work, and willing to share these genuine responses publicly. If
this practice does not exist, the field may still be useful
and/or interesting. But it is not science.

What's awesome about Fare's essay is that he's _doing it right_.
All the actual details are irrelevant. And mostly wrong. But
that's of very little importance compared to _doing it right_.

Anyway, we've already addressed the idea that jets are a sham.
Some other points:

### Event-sourcing and/or caching is a pattern, not a layer

Fare' is perfectly right that "one size does not fit all" in
persistence. Generally, Urbit uses event-sourced persistence.

But it has multiple layers of event-sourcing: in the definition
of Urbit itself; in the application sandbox vane (Gall), and in
the application itself. Urbit also has a completely orthogonal
revision control system (Clay).

And there is not a single cache across the whole system, though
arguably there should be; for instance, the functional build
system (Ford, which Fare' the master of build systems might
appreciate) has its own internal cache.

It is a common mistake to confuse a design pattern with a layer
or feature, and equally fatal whichever direction you fail.

### Let's be clear about why DSLs suck

DSLs suck not because they are difficult to newbies in the base
language. DSLs suck because every time a programmer, even one
_experienced_ in the base language, comes into a new codebase or
project, she has to learn a new custom language.

You need enormous wins to justify this cost. It's the goal of
the base language designer to preempt these wins, creating a
language which is almost never so tiresome and full of
boilerplate that it requires a DSL or template layer.

Hoon is almost entirely made of macros -- but they are internal,
fixed, built-in macros (with no gensyms). This fixed set of
macros is essentially enough. Occasionally we want to add one or
two. But we never, ever pine for context-dependent macros.

### Determinism is not consensus, and is not constraining

Fare' is totally right that an urbit's data is meant for itself
alone. In fact, an urbit has the right to compute incorrectly if
it wants. We go to considerable lengths to ensure that you can't
gain any particular advantage by incorrect computing.

The whole point of Nock is that it's so low-level that there is
no conceivable advantage in upgrading it. Divergence is just
pointless. This is not a question of network integrity, just of
practical standardization.

This consistency stops at Nock. Without anyone violating the
correctness of their lifecycle functions, anyone or everyone in
Urbit could replace Hoon with Javascript. Determinism does not
cramp Urbit's semantic style in any way, shape or form.

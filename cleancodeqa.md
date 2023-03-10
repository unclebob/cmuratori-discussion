**CASEY**: Thanks [@unclebobmartin](https://twitter.com/unclebobmartin) for taking the time to answer these questions! Maybe we can start with just some clarification.

Most explanations on Clean Code I have seen from you include all the things I mentioned in the video - preferring inheritance hierarchies to if/switch statements, not exposing internals (the "Law of Demeter"), etc. But it sounds like you were surprised to hear me say that. Could you take a minute before we get started to explain more fully your ideas on type design, so I can get a sense for where the disconnect is here?

**BOB**: The disconnect.  Hmmm.  I’m not sure there is one.  

I watched the first half of your video.  After that I figured that I had caught the drift.  I responded in one thread that I thought your analysis was essentially correct. I also thought that your rhetoric was a bit inaccurate in representing “clean code”.  I don’t remember exactly what that inaccuracy was; and it doesn’t really matter.

So…. Yes, absolutely, the structures you were presenting are not the best way to squeeze every nanosecond of performance out of a system. Indeed, using those structures can cost you a lot of nanoseconds.  They are not efficient at the nanosecond level.  Long ago this would have been generally important.  We worried about the cost of function call overhead and indirection.  We even unwound loops if we could.  This was especially true in embedded real time environments.

But the kinds of environments where that kind of parsimony is important are nowadays few and far between.  The vast majority of software systems require less than 1% of a modern processor’s power. What’s more, processors are so cheap and available that it is a trivial matter to add more of them to a system. These facts change the trade-off away from program performance to the performance of the development team, and their ability to create systems, and keep those systems running. And it is those needs where the ideas of Clean Code come into play.

It is economically better for most organizations to conserve programmer cycles than computer cycles. So if there is a disconnect between us, I think it is only in the kinds of contexts that we prioritize.  If you are trying to squeeze every nanosecond from a battery of GPUs, then Clean Code may not be for you; at least in the most taxing of your deepest inner loops.  On the other hand, if you are trying to squeeze every man-hour of productivity from a software development team, then Clean Code can be an effective strategy towards that end.

**CASEY**: Just to get a little bit more concrete, so I understand what you're saying here, can we pick some specific software examples? For instance, I would assume we are both familiar with using Visual Studio and CLANG/LLVM. Would those both be reasonable examples of what you are calling the vast majority of software that requires less than 1% of a modern processor?

**BOB**: No, it seems to me that an IDE is a very specialized software system.  There are only a few in existence, and only a very few that have become popular.  

IDE's are interesting systems in that they span a huge domain of contexts.  There are portions in which nanoseconds are extremely important, and other parts where they matter very little.  A modern IDE has to be able to parse large extents of code on a keystroke by kestroke basis.  Making sure that the parsing code preserves nanoseconds can have a big effect.  On the other hand, the code that sets up a configuration dialog does not need even a tiny fractioin of that kind of efficiecy.  

And, as an aside, the kind of efficiency the compile engine of an IDE needs is more algorithmic than cycle-lean.  Cycle-lean code can increase efficiency by an order of magnitude; but the right choice of algorithm can increase efficiency by many orders of magnitude.

No, the kind of software I was referring to that requires <1% of a modern processor are the regular run-of-the-mill systems that most programmers engage in.  A website, a calendar app, a process control dashboard (for a simple process).  In fact, just about any Rails app, or any Python or Ruby app.  Even most Java apps.  You simply would not choose languages like that if nanoseconds were your concern.  

A language like Clojure (my "go to" language at the moment) is likely 30X slower than an equivalent Java app, and probably 60X slower than an equivalent C app.  But I don't care that much.  First, I can drop into Java if I have to (and I have for compute bound tasks).  Second, for many apps, adding processors is simple and cheap.  And so I usually find the programmer time saving to be cost effective.

Don't get me wrong.  I'm an old assembler and C hacker from the 70s and 80s.  I assiduously counted microseconds when it mattered (nanoseconds were way beyond anything we could imagine).  So I know how important cycle-lean code can be.  But today's processors are 10,000 times faster than the machines we used in those days (literally).  So for most software systems nowadays we have the ability to trade some of those extra cycles per second for programmer efficiency.

**CASEY**: If I understand you correctly, you are saying there are two broad categories of software, so we perhaps have to discuss each one separately. It sounds like most software I actually use falls into the category where "nanoseconds can matter", in your terminology - in other words, Visual Studio, LLVM, GCC, Microsoft Word, PowerPoint, Excel, Firefox, Chrome, FFmpeg, TensorFlow, Linux, Windows, MacOS, OpenSSL, etc. I assume based on your answer that you would agree all of those do have to be concerned about performance?

**BOB**: Not exactly.  Rather my experience is that there is a broad spectrum of software that applies at the module level.  Some modules must perform with nanosecond deadlines.  Others require microsecond response times.  Still others need only operate under millisecond constraints.  And there are modules that would operate satisfactorily with response times approaching a second.  

Most applications are constructed of modules that cover much of this spectrum.  For example, Chrome must render quickly.  Microseconds matter when you are populating a complex web page.  On the other hand, The preferences dialog in Chrome likely doesn't even need to be responsive at the millisecond level.

If we thought of the modules within a particular application arranged into a histogram by response time, I presume we'd see some kind of non-normal distribution.  Some applications may have many nanosecond modules and few millisecond modules.  Other applications would have most modules in the millisecond range and few in the nanosecond range.

For example, I'm currently working on an application in which the vast majority of modules work well at the millisecond level; but a few require 20X better performance.  My strategy has been to write the millisecond modules in Clojure because, while slowish, it is a very convenient language.  The microsecond modules I wrote in Java which is much faster, but far less convenient.  

There are languages, and structures, that abstract away the hard metal of the machine and make it easier for programmers to focus on the problem domain.  It is far more efficient for a programmer to write millisecond level code when they don't have to be concerned with optimizing L2 cache hits.  They can think, instead, about the business requirements, and about the other programmers who will have to deal with their code over the next decade.  

There are also languages and structures that expose the hard metal of the machine to make it easier for programmers to squeeze every last nanosecond out of an algorithm.  Those structures may not be the easiest to write, explain, or maintain; but when nanoseconds count it would be folly to ignore them.  

And of course there are languages and environments in the middle of that range as well.  Knowing these environments, and knowing which is best for the problem at hand, is something we all need to be proficient at.

***

I wrote a book a decade or so ago entitled _Clean Code_. It focused more on the millisecond side of the problem than on the nanosecond side.  It seemed to me, at the time, and indeed still today, that the problem of programmer productivity was an important issue.  However, the book was not myopic about the issues that you and I are discussing here.  For example, if you read the section that starts with "Prefer polymorphism..." you will see a discussion about the benefits of switch statements in certain contexts.  Indeed, there's whole chapter about such contexts.  That chapter includes the following statement, which I think captures the sentiments I have been expressing in this discussion: "Mature programers know that the idea that everything is an object is a myth. Sometimes you really do want simple data structures with procedures operating on them." 

**CASEY**: I wanted to be specific here, so I'll try rephrasing my question. Are Visual Studio, LLVM, GCC, Microsoft Word, PowerPoint, Excel, Firefox, Chrome, FFmpeg, TensorFlow, Linux, Windows, MacOS, and OpenSSL examples of programs where "milliseconds matter" _in at least some of their "modules"_, in your terminology?

**BOB**: Milliseconds?  Of course.  I'd say that they all have modules where microsecond matter; and many have modules where nanoseconds matter.  

*** 
>  _I must have written the following while you were adding the next question.  So I had a merge to reconcile.  So here's what I said before I read your next question.  I'll answer that shortly.

**Bob**: (The next day) By the way, I went back and watched the entirety of your video the other day.  I figured that since we were engaged in this discussion I ought to study the whole story you told.  And, although I was a bit miffed about some of your rhetoric, I have to complement you on a very sweet analysis.  

The lovely insight that the areas of certain shapes can all be calculated using the same basic formula (KxLxW) is one of those moments that I think only programmers and mathematicians can truly appreciate.  

Overall, I thought your video provided a good example of the kinds of things a programmer must do when solving a constrained problem within a resource constrained environment.  Clearly (at least I think it should be clear) one would not prefer the KxLxW solution in a resource rich environment unless one was very sure that the business would not extend the problem to general shapes.  And, indeed, even if the problem remained constrained to shapes that allowed the KxLxW solution, the separation into the more traditional formulae would likely better match other programmer's expectations; and would not cause them to puzzle over, and re-validate, the relatively novel approach.  While this might deprive them of a delicious moment of insight, it would allow them to get on with their tasks without delay.

I don't know if you've read the work of Don Norman.  Long ago he wrote a book entitled _The Design of Everyday Things_.  It's well worth the read.  Within those pages he stated the following rule of thumb: _“If you think something is clever and sophisticated beware -- it is probably self-indulgence.”_  In a resource rich environment I fear the KxLxW solution could fall afoul of this rule.

> _This was written before Casey read my comment above._

**CASEY**: OK great, it sounds like we've gotten onto the same page about software categories. I'd like to give my characterization of the coding practice you're describing as it applies to something like LLVM, since that is a piece of software from the list I gave, and it happens to be open source so we know exactly how it is constructed (unlike, say, Visual Studio).

What I take you to be saying in the above paragraphs - and in your books and lectures - is that when programming a large piece of software like LLVM, the programmers do not need to be concerned about performance when they are programming. They should be primarily concerned with their own productivity. If it were, to use your previous example, a simple calendar app, then they would ideally __never__ think about performance. But in LLVM, since that is in the category where sometimes "nano/micro/milliseconds matter", then they will have to think about performance at some point. That point is when they find the program is running too slowly, whenever that occurs.

In LLVM, perhaps that is the first time someone tries to build a truly large program with it, like the Unreal Engine or Chrome or whatnot. When this happens, __then__ the assumption is that the performance problems will be in some isolated parts of the code (I believe you have referred to them in this discussions as "modules"), so __just those parts__ should now be rewritten to be performance-oriented.

That's how I interpret what you're saying so far, and also how I interpretted things you've said recently like "If my Clojure code is too slow, I can always drop down to Java", meaning that you could rewrite a portion of the code in Java if that part needed more performance.

Is that a fair characterization?

**BOB**: I'm one of those signatories of the Agile Manifesto who still believes in a bit of up-front architecture and design.  (Actually, I'm pretty sure they all do.  It was the latter zealots who thought it better to leap into code without any forethought).  

In the case you mentioned I _hope_ I would have thought through the problem well enough to recognize where I might run in to performance problems and to therefore treat those modules with greater attention.  For example, I might have created a very attenuated version of the module and then subjected it to a torture test while profiling the behavior.  My concern, of course, would be the investment of a large amount of time and effort into an approach that ultimately failed to meet my customer's needs.  (Ask me why I worry about things like that ;-).  

The bottom line, of course, is that Single Factor Analysis is _always_ suboptimal.  There is no _ONE TRUE WAY_.  (A point I tried to make several times in _Clean Code_.)

***

**CASEY**: I have a lot of questions I'd like to ask already, but your last answer segues into one of them best so I'll go with that one :) Already in this conversation you have talked about several critical performance implications in software architecture: the "nanosecond" concerns of an IDE parser, the division of "modules" into nano/micro/milli/second response time requirements, the suggestion that a programmer (in this case, you) might create "a very attenuated version of the module and then subjected it to a torture test while profiling the behavior" before writing a piece of software to ensure that the performance would be acceptable, and even the idea that you might have to pick different languages depending on the performance requirements (Clojure vs. Java vs. C, in your example).

And in summary, you've said, "Knowing these environments, and knowing which is best for the problem at hand, is something we all need to be proficient at".

Given all of that, I'd like to return to basically the original question: why were you _surprised_ that people, such as myself, associated "Clean Code" with effectively _the opposite_ of what you have written here with respect to performance? None of the things I just listed are given prominent placement in your teachings. I'm not suggesting that you can't find a sentence here or there in a book or blog post that gives a nod to performance, of course. But by volume, the things you're saying here get barely a nod.

As a concrete example of what I mean by that, here is an entire multi-hour, six-part lecture series you gave on "Clean Code" where not a single one of the things you mentioned here are discussed in the nine hour runtime:

https://www.youtube.com/playlist?list=PLmmYSbUCWJ4x1GO839azG_BBw8rkh-zOj

If performance concerns are as important as you suggest they are in this thread, why isn't there at least one hour out of nine dedicated to explaining to the audience the importance of things like learning about performance, planning ahead of time about what parts of the code may have performance implications, avoiding performance-harmful programming constructs in those situations, creating performance tests beforehand like the kind you described in the previous answer, etc.?

Another way to ask this question would be, is it possible you have taken for granted how important performance awareness actually is _even to you_, because perhaps you are habitually doing it when you program yourself, and thus you have not given it the prominence necessary to ensure your audiences - who often will know very little about performance - actually think about it at the right times and in the right ways? Especially if, as you said, "we all need to be proficient" at these things?

**BOB**: Frankly, I think that's a fair criticism.  And, as it happens, I taught a class yesterday in which I spent morme time talking about the performance costs, as well as the productivity benfits, of the disciplines and principles that I teach.  So thank you the nudge.

I don't think I used the word _surprised_.  Or if I did it was not in reference to the topic; it was more about the tone.  Enough said about that.

You asked me whether I had been taking the importance of performance for granted.  After some self-reflection I think that's likely.  I am not an expert in performance.  My expertise is in the pactices, disciplines, design principles, and architectural patterns that help software development teams efficiently build and maintain large and complex software systems.  And as every expert knows, and must fight against, expert hammers think everything looks like a nail. 

You also asked me "why...".  To the extent that I have not answered that above, I'll simply turn the question around and point out that it is probably for the same reason that your video was solely focussed on the amplification of performance to the strident denigration of every other concern.  To a performance hammer, everything looks like a nail.  ;-)

That being said, I'm finding this conversation to be more beneficial than I had initially anticipated.  It has nudged a change in my perspective.  You should not expect that change to be enormous.  You should not expect me to make videos about how horrible Clean Code is ;-).  But if you watch the next 9 hour suite of videos I make, you'll probably see more than "barely a nod" towards performance issues.  I think you can expect two or three nods.  ;-)  

Because, as you pointed out, I do consider performance issues to be important enough to anticipate and plan for.

**CASEY**: Honestly the nudge was most of what I hoped to accomplish here :)  And just to emphasize how important I think performance is today, I noticed while trying to edit this very file on github that if I type a paragraph that is too many lines long, it starts to get very slow and it's difficult to type!  It's only a few hundred characters, but there's so many layers of things piled up in the system that what should be instantaneous becomes unusably slow.  So one of the reasons I harp on performance so much is because it seems like software is getting unusably slow these days, even for simple tasks. In fact, just so you know I'm not making this up, here is a video where I record just how incredibly slow it was to type this paragraph:

https://www.youtube.com/watch?v=gPgm28zXNEE

And that's on a Zen2 chip, which is extraordinarily fast!  Whatever organizational forces (perhaps even cross-company in this case) make this sort of thing common would benefit greatly from hearing, for example, _exactly_ what you said earlier in this conversation.  There are tons of organizations that absolutely don't think about the "nano/micro/milli/second" breakdown, and they need to!  Just putting that thought in their heads - that they need to have institutional ability to recognize where performance problems will be _early_, before it's too late, and to have institutional players with the power to address those problems - would be a _major_ improvement in most development organizations.

So we could definitely end the conversation here. If you'd like to keep it going, the next thing to talk about would be the "strident denigration" you referred to. That would take us into architecture territory, not merely performance, but I'm happy to go there if you'd like. You're choice!

**BOB**: That video was hysterical.  I gotta ask what browser you were using.  I'm using Vivaldi (a Chrome fork) and it exhibits the same kind of lag. (Though not quite as bad as yours.) So I did a few experiments.  It turns out that the lag has nothing to do with the size of the file.  Rather, it has to do with the size of the _paragraph_.  The longer the paragraph the longer the lag.  Indeed, this paragraph is already unable to keep up with the 25cps repeat rate.  And the more I type in this paragraph the worse the lag gets.

Now why would that be?  First of all, I imagine that we are both typing into the same javascript code.  After all, nobody wants to use the tools written into the browser anymore ;-)  I mean, JavaScript is just _so much better_.  Secondly, I also imagine that the author of this code never anticipated that you and I would pack whole paragraphs into a single line.  (Note the _line_ numbers to the left.)  Even so, the lag becomes very apparent at the 25cps rate by about 200-300 characters.  So what could be going on?

Could it be that the programmer used a badly written data structure that grows by allocating a new memory block every time it grows, and then copies the data into the new block?  I rembmer the old Rouge Wave C++ Library had a growing string like that.  Boy, oh boy, could that get slow!  You can do the math.  That's O(n^2) if you ask me.  

Of course that's much more of an algorithm problem than straight forward efficiency problem.  And, indeed, the algorithm is always the first place to look when something seems too slow.  But, your point is well taken.  The programmer here simply never anticipated the kind of use we are putting their code too; and it just doesn't deal well the unanticipated load.  They didn't think it all the way through.

Perhaps you and I should
hit return at the end of 
our lines from now on. ;-)

                  10        20        30        40        50        60        70        80
         ....:....|....:....|....:....|....:....|....:....|....:....|....:....|....:....|
         Anyway, I am curious about the architectural issues you seem to have queued up
         for me. So, let us continue this conversation, while limiting our lines to 80 
         characters -- you know, like a Holerith card.  That way, no matter what kind 
         of chip this code executes on, it'll probably be able to keep up.  >B^D

**CASEY**: I couldn't resist, so I looked at the performance capture in Chrome and we now know who the culprit is :) It's the "emoji picker"! And your guess about the kind of problem was not far off.

I only glanced at the code, but the issue appears to be that every time you type a character, the "emoji picker" will scan backwards in the text to see if what you have typed is an emoji. You can see it "work" if you type, say, a colon followed by "bacon" or whatever. When you do that it will put up a little drop-down for emoji completions and show the cute little bacon icon. I can't recall ever using (or wanting) this feature, but, I guess it's there if we... uh... need it? Anyway, for long paragraphs this scanning process eventually gets slow enough to prevent responsive processing of keystrokes.

However, this newfound knowledge does provide us with a "work-around". Whenever we get to the point in a paragraph where the github text editor becomes prohibitively slow, we can just _pretend we are starting a new emoji_ and this solves the problem by preventing the picker from scanning as far backward. We can do this by just typing a colon, because that's the thing that normally starts emoji! For example, right now this paragraph is starting to be really sluggish. So I'm just going to put a colon right here : and voila! Now I'm back to normal typing speed, no worries. I could probably go a few more sentences like this just fine. Of course, I'd have to drop another colon at that point, but, it seems a small price to pay to see what I'm typing.

I would actually have to read the code carefully to find out _why_ the thing seems to scan backwards so far, when obviously the emoji strings can't ever be that long. What's the maximum length of an emoji name? Twenty characters? Thirty? It can't be much. But the fact that my work-around works seems to indicate I'm not wrong about the problem. So I guess they must just have coded something like "scan backwards until you see a colon" and left it at that.

Why it causes such a vicious slowdown is another interesting question. From the looks of the profile, I think what's happening is the emoji check just has to take enough time to be _longer_ than it takes you to type the next character. Once it gets up to a hundred milliseconds or so, I suspect what is happening is the next keyboard event comes in before it's done, so it just ends up woefully behind and it can't catch up until you stop typing :( But that's just a guess, I didn't actually investigate.

Anyway, I figured that might amuse you so I thought I'd share it with you before we continued our discussion!

**BOB**
    LOL:LOL:LOL:LOL

    Don't:you:just:love:being:a:programmer:and:diagnosing:interesting:problems:from:the:symptoms?
    To:do:that:well:you:have:to:_think_:like:a:programmer.::

    Long:ago:I:started:a:company:called:Object:Mantor:Inc.::My:partner:and:I:came:up:with:a:nice
    little:logo,:and:I:thought:it:would:be:fun:to:embroider:it:onto:a:few:shirts.::My:wife:had:an
    embroidery:machine:so:I:fired:up:her:yukky:PC:desktop,:fed:the:image:file:into:the:embroidery
    software,:and:out:came:the:file:that:the:machine:could:use:to:sew:the:pattern.

    That:file:was:a:little:crude,:so:I:decided:to:edit:it:to:fix:a:few:of:the:rough:edges.::But
    the:damned:thing:kept:crashing:when:I:selected:certain:parts:of:the:pattern.::Other:parts
    were:safe:and:the:software:behaved:fine:so:long:as:I:avoided:these:"hot":zones.::

    Now:why:would:it:do:that?::Ah,:well,:I:had:just:spend:a:couple:of:years:working:in:a:
    system:of:applications:that:made:heavy:use:of:computational:geometry.::One:of:the
    tricker:elements:to:manage:were:arbitrary:polygons.::You'd:like:to:think:that:polygons
    are:nicely:ordered:sets:of:points:that:describe:the:perimeter:of:a:shape.::In:reality
    of:course,:they:can:be:as:tangled:as:a:skein:of:yarn:that:a:kitten:has:been:playing:with.

    So:I:cranked:up:the:zoom:until:my:logo:was:the:size:of:a:football:field.::I:started:scanning
    the:periphery:of:the:polygons:that:the:image:processing:software:had:created.::Sure:enough,
    every:so:often:there:was:a:little:tiny:tangle:--:a:grouping:of:three:or:four:points,
    separated:by:no:more:than:a:fraction:of:a:millimeter,:that:tied:that:part:of:the:perimeter:in:
    a:little:knot.::

    I:manually:located:and:fixed:all:those:knots.::And:after:that:the:editing:program:stopped
    crashing.

    Oh,:I:could:go:on.::Perhaps,:one:day,:I'll:regale:you:with:my:first:use:of:the:Obama-care:
    website.::Now:there's:a:fun:tale.

>_I created this with vi and used_ `1,$s/ /:/g`  
>_Because, I really am an old C hacker at heart._

**CASEY**: Thanks [@unclebobmartin](https://twitter.com/unclebobmartin) for taking the time to answer these questions! Maybe we can start with just some clarification.

Most explanations on Clean Code I have seen from you include all the things I mentioned in [the video](https://www.youtube.com/watch?v=tD5NrevFtbU) - preferring inheritance hierarchies to if/switch statements, not exposing internals (the "Law of Demeter"), etc. But it sounds like you were surprised to hear me say that. Could you take a minute before we get started to explain more fully your ideas on type design, so I can get a sense for where the disconnect is here?

**BOB**: The disconnect.  Hmmm.  I’m not sure there is one.  

I watched the first half of your video.  After that I figured that I had caught the drift.  I responded in one thread that I thought your analysis was essentially correct. I also thought that your rhetoric was a bit inaccurate in representing “clean code”.  I don’t remember exactly what that inaccuracy was; and it doesn’t really matter.

So…. Yes, absolutely, the structures you were presenting are not the best way to squeeze every nanosecond of performance out of a system. Indeed, using those structures can cost you a lot of nanoseconds.  They are not efficient at the nanosecond level.  Long ago this would have been generally important.  We worried about the cost of function call overhead and indirection.  We even unwound loops if we could.  This was especially true in embedded real time environments.

But the kinds of environments where that kind of parsimony is important are nowadays few and far between.  The vast majority of software systems require less than 1% of a modern processor’s power. What’s more, processors are so cheap and available that it is a trivial matter to add more of them to a system. These facts change the trade-off away from program performance to the performance of the development team, and their ability to create systems, and keep those systems running. And it is those needs where the ideas of Clean Code come into play.

It is economically better for most organizations to conserve programmer cycles than computer cycles. So if there is a disconnect between us, I think it is only in the kinds of contexts that we prioritize.  If you are trying to squeeze every nanosecond from a battery of GPUs, then Clean Code may not be for you; at least in the most taxing of your deepest inner loops.  On the other hand, if you are trying to squeeze every man-hour of productivity from a software development team, then Clean Code can be an effective strategy towards that end.

**CASEY**: Just to get a little bit more concrete, so I understand what you're saying here, can we pick some specific software examples? For instance, I would assume we are both familiar with using Visual Studio and CLANG/LLVM. Would those both be reasonable examples of what you are calling the vast majority of software that requires less than 1% of a modern processor?

**BOB**: No, it seems to me that an IDE is a very specialized software system.  There are only a few in existence, and only a very few that have become popular.  

IDE's are interesting systems in that they span a huge domain of contexts.  There are portions in which nanoseconds are extremely important, and other parts where they matter very little.  A modern IDE has to be able to parse large extents of code on a keystroke by keystroke basis.  Making sure that the parsing code preserves nanoseconds can have a big effect.  On the other hand, the code that sets up a configuration dialog does not need even a tiny fractioin of that kind of efficiency.  

And, as an aside, the kind of efficiency the compile engine of an IDE needs is more algorithmic than cycle-lean.  Cycle-lean code can increase efficiency by an order of magnitude; but the right choice of algorithm can increase efficiency by many orders of magnitude.

No, the kind of software I was referring to that requires <1% of a modern processor are the regular run-of-the-mill systems that most programmers engage in.  A website, a calendar app, a process control dashboard (for a simple process).  In fact, just about any Rails app, or any Python or Ruby app.  Even most Java apps.  You simply would not choose languages like that if nanoseconds were your concern.  

A language like Clojure (my "go to" language at the moment) is likely 30X slower than an equivalent Java app, and probably 60X slower than an equivalent C app.  But I don't care that much.  First, I can drop into Java if I have to (and I have for compute bound tasks).  Second, for many apps, adding processors is simple and cheap.  And so I usually find the programmer time saving to be cost effective.

Don't get me wrong.  I'm an old assembler and C hacker from the 70s and 80s.  I assiduously counted microseconds when it mattered (nanoseconds were way beyond anything we could imagine).  So I know how important cycle-lean code can be.  But today's processors are 10,000 times faster than the machines we used in those days (literally).  So for most software systems nowadays we have the ability to trade some of those extra cycles per second for programmer efficiency.

**CASEY**: If I understand you correctly, you are saying there are two broad categories of software, so we perhaps have to discuss each one separately. It sounds like most software I actually use falls into the category where "nanoseconds can matter", in your terminology - in other words, Visual Studio, LLVM, GCC, Microsoft Word, PowerPoint, Excel, Firefox, Chrome, fmmpeg, TensorFlow, Linux, Windows, MacOS, OpenSSL, etc. I assume based on your answer that you would agree all of those do have to be concerned about performance?

**BOB**: Not exactly.  Rather my experience is that there is a broad spectrum of software that applies at the module level.  Some modules must perform with nanosecond deadlines.  Others require microsecond response times.  Still others need only operate under millisecond constraints.  And there are modules that would operate satisfactorily with response times approaching a second.  

Most applications are constructed of modules that cover much of this spectrum.  For example, Chrome must render quickly.  Microseconds matter when you are populating a complex web page.  On the other hand, The preferences dialog in Chrome likely doesn't even need to be responsive at the millisecond level.

If we thought of the modules within a particular application arranged into a histogram by response time, I presume we'd see some kind of non-normal distribution.  Some applications may have many nanosecond modules and few millisecond modules.  Other applications would have most modules in the millisecond range and few in the nanosecond range.

For example, I'm currently working on an application in which the vast majority of modules work well at the millisecond level; but a few require 20X better performance.  My strategy has been to write the millisecond modules in Clojure because, while slowish, it is a very convenient language.  The microsecond modules I wrote in Java which is much faster, but far less convenient.  

There are languages, and structures, that abstract away the hard metal of the machine and make it easier for programmers to focus on the problem domain.  It is far more efficient for a programmer to write millisecond level code when they don't have to be concerned with optimizing L2 cache hits.  They can think, instead, about the business requirements, and about the other programmers who will have to deal with their code over the next decade.  

There are also languages and structures that expose the hard metal of the machine to make it easier for programmers to squeeze every last nanosecond out of an algorithm.  Those structures may not be the easiest to write, explain, or maintain; but when nanoseconds count it would be folly to ignore them.  

And of course there are languages and environments in the middle of that range as well.  Knowing these environments, and knowing which is best for the problem at hand, is something we all need to be proficient at.

***

I wrote a book a decade or so ago entitled _Clean Code_. It focused more on the millisecond side of the problem than on the nanosecond side.  It seemed to me, at the time, and indeed still today, that the problem of programmer productivity was an important issue.  However, the book was not myopic about the issues that you and I are discussing here.  For example, if you read the section that starts with "Prefer polymorphism..." you will see a discussion about the benefits of switch statements in certain contexts.  Indeed, there's whole chapter about such contexts.  That chapter includes the following statement, which I think captures the sentiments I have been expressing in this discussion: "Mature programers know that the idea that everything is an object is a myth. Sometimes you really do want simple data structures with procedures operating on them." 

**CASEY**: I wanted to be specific here, so I'll try rephrasing my question. Are Visual Studio, LLVM, GCC, Microsoft Word, PowerPoint, Excel, Firefox, Chrome, fmmpeg, TensorFlow, Linux, Windows, MacOS, and OpenSSL examples of programs where "milliseconds matter" _in at least some of their "modules"_, in your terminology?

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

**CASEY**: Now that we've discovered the secrets to efficient github text entry, we can move on to architecture :)

I suppose the best place to start would be to confirm some things from "Clean Code" that I agree with, and say the extent to which I agree with them. There are two main things.

First, I think we're in total agreement about basic code readability. Like you, I prefer code to be legible without comments, such that if you read a function from top to bottom, it more-or-less says in English what it does because the functions, variables, and types have been named so as to accurately portray their behavior. Obviously this isn't always _strictly_ possible, but that's the general goal. I don't love working with code that where all the variables are "a", "b", "c", "d", etc., or - and this is a real thing - code where all of the matrix operations in the entire program are written using two _global_ variables, A and B.

That said, I'm fine with single-letter variable names if they _reference_ something specific. For example, if the top of a function has a comment saying "this implements such-and-such a paper", and the variables are named to coincide with equations in the paper, I might prefer that. If the thing being implemented is some kind of complicated math, where I'm going to have to read the paper anyway to really know what is going on, having the variables line up with the paper can be a help in that case.

But otherwise, I like descriptive naming.

Second, I do like tests. Even in the video that precipitated this conversation, the code has some "self-testing", which is to say that it shows the difference off of a reference sum, just to try to catch any bad code changes that produce an incorrect area summation. Similarly, in my "Performance-Aware Programming" Course (where the video comes from), we recently did an 8086 disassembler. The way I developed it was I wrote a bunch of test ASM listings, then made a batch file that would assemble them with NASM, disassemble them with my disassembler, then reassemble the disassembly with NASM. I could then diff the final result with the original NASM-produced binary and know that my disassembly was accurate.

So I think we're roughly on the same page about tests. Where we might differ is to what extent tests "drive" the development process. I get the sense that your perspective is "write the tests first", or something close to that. I don't know that I would go that far. I tend to develop first, and as I find things where I think a test would help prevent regressions, I'll add the test then.

I think there are two reasons for this. One is that my background is game development, which is a highly interactive (and subjective) medium where many things are difficult to test in an automated way. So while we can (and do) test things like memory allocators or math libraries as necessary, testing broader things like "can the player jump between these two ledges" is not really feasible. We can do replay testing, and randomized input testing, but there are a fair number of things that just aren't feasible to "drive" via testing. This is less the case when looking at someething like, say, a file server, where you can very explicitly define what the inputs and outputs should be ahead of time. So I do think it varies by type of application.

The second reason I'm more judicious with tests is that the goal of tests is to save development time. If you end up writing tests that you don't use, or that don't find bugs, or that took longer to write and maintain than the number of bugs they prevented, that can be a lose. So I think there's a balance there, where not enough testing means that development takes longer because you have more bugs and spend more time hunting for "what broke" when you make changes, but too much testing means you spend a ton of time making tests that didn't actually bear fruit.

And that, too, is application-specific, or even component-specific, because bugs have varying costs. I may be worried or even paranoid about bugs that could affect file integrity in a file server, but relatively unconcerned about pixel-accuracy bugs in the renderer for the buttons that control that file server. So what I'm going to test, and how rigorously, seems like it has to be somewhat flexible if the goal is to minimize the total development time for a project.

But, like I said, I think we probably agree more on readability and testing than we disagree. I might just put more emphasis on weighing the costs when it comes to testing. Does that seem like an accurate summary?

**Bob**: I have a few quibbles that I'll explain below; but again I don't think our disagreements are huge.

>_Descriptive Names_

My rule for variable names is that the length of a variable's name should be proportional to the size of the scope that contains it.  The name `i` is perfectly valid within a one or two line scope.  Much larger than that, and you should likely use a small word, like `index`.  I'd make the same exception you do for well known formulae.  If you are writing the code for the quadratic formula, `a`, `b`, and `c` are the right names to use regardless of the number of lines in the scope.  

BTW, my rule for functions is the opposite.  The name of a function (or a class) should be _inversely_ proportional to the size of the scope that contains it.  This is mostly for convenience (small names are easier to call) but also because at large scopes functions tend to be general, requiring no modifiers.  At smaller scopes we need adjectives or other modifiers to properly describe the function.  And, of course, exceptions apply as before.

>_Tests_

I am perhaps a bit more rigorous in the way I write tests.  I tend to write failing tests first, and then make them pass, in a very short cycle.  This helps me think through the problem.  It also gives me the perspective of writing the code _after_ something has used it.  It often forces me to decouple elements from each other so that they can be independenly tested.  Finally, it leaves me with a suite of tests that has very high coverage, and that I have seen both pass and fail.   And that means I trust that test suite.

But I also face the same dilemma you do.  I cannot practically write tests for things that I have to see in order to know that they work.  GUIs, Game behaviors, Hardware interactions, etc.  Writing tests for these things is pointless because I don't know the correct answer up front.  So I have to use my eyes in a very tight cycle making small changes to the code until the behavior matches my "feeling" for correctness.  

After that I _might_ write a test if I think it's a behavior I want to anchor.  But often I leave that code untested.

Two examples:
  1. _Spacewar_.  On my gitub site you'll see a project named `Spacewar`.  It's fun little Star Trek game patterned after the kind of games we used to play in the 80s.  It's written in Clojure and is very interactive.  I tested the hell out of the computational, physics, and AI behaviors; but could not practically test the interactive graphics.  If you look at that code you can see that one side (the engine) is heavily tested, and the other side (the GUI) is not.
  2. _more-speech_.  Also on my github site you'll see a project called `more-speech`.  It is a browser and client for the `nostr` network.  It has a GUI component, and a lot of internal message processing.  The latter is tested, the former is tested less so.  

Why do I use the test-first-discipline?  You named a bunch of reasons above, but neglected one that I consider to be the overriding impetus.  When I have a suite of tests that I trust, and that has high coverage, I can refactor that code without fear.  I can improve the design.  I can add new features.  I can make the code more performant.  And so long as all those tests continue to pass I can be certain, beyond reasonable doubt, that nothing has broken.  That ability makes me go _fast_.  

I consider it to be the equivalent of double-entry bookkeeping for accounting.  I try to say everything twice and make sure the two statements agree.

**CASEY**: What you are describing is what I meant by "as I find things where I think a test would help prevent regressions, I'll add the test then." Maybe other people use the term differently, but "regression test" is the term I use to describe a test whose primary purpose is to detect bugs in modifications to a code path as I modify it. So what you described is exactly what I use a regression test for: allowing me to make more rapid or significant changes to a critical piece of code that I know might fail in subtle ways that would be hard to detect without extensive testing. It prevents the compliance from "regressing".

So I think we're in pretty broad agreement on testing. Maybe you spend a higher percentage of your coding time writing tests because we differ on the exact cost/benefit analysis there, but it doesn't sound like there's a huge gap either way. So perhaps we can move on to something we will disagree on, which is "classes vs. switch statements"? Of course those are just particular implementations of a more general divide, which is operand-primal vs. operation-primal architecture.

I don't want to misrepresent your opinion so I'll leave full categorization of the clean code methodology for your response. But to tee it up, from my outsider's perspective, I would say it tends to be "operand-primal". This is in line with traditional OOP ideas, and means the "rules of thumb" in the methodology tend to favor ease of adding new types for existing operations vs. ease of adding new operations for existing types. It also usually means that the programmer is encouraged to think about operations as things which exist subordinate to the types on which they operate, as opposed to the other way around.

Although I have worked in codebases that share that perspective, and have written that kind of code myself, I am unable to see the benefits of such an approach except in very rare circumstances which typically involve so little code that I don't tend to consider them as necessary considerations to the overall design. However, I am clearly in the minority, because OOP in general is obviously a very _popular_ approach today.

So I'm hoping that this part of the discussion will give me the opportunity to point out all the ways I think operand-primal design results in _both_ slower development time _and_ slower code runtime, and get a proponent's response. But before we do that, perhaps you could respond to my categorization first, in case you'd like to either disagree with it or elaborate more?

**Bob**:
This is so much fun.  It is a pleasure to discuss these matters in such a professional and civil manner.  

>_Tests: afterword_

It seems the difference between us can be stated as follows:  
 * I write tests unless there is a good reason not to.  
 * You write tests when there is a good reason to.  

I consider the former to be a _discipline_.  As a pilot I follow checklists unless there is a good reason not to.  I fly in good weather unless there is a good reason not to.  I submit myself to air traffic control, even when legally unecessary, unless there is a good reason not to.  

>_Categorization: The Primality of Operand vs Operation_

I like the categorization.  Indeed I wrote a whole chapter in _Clean Code_ on this topic, though I didn't use your names (Chapter 6: Objects and Data Structures).  OO (Operand Primal) is a great way to add types to existing functions without changing those functions.  Procedural (Operation Primal) is a great way to add new functions to existing types without changing those types.

The question, of course, is which of those two is the most efficient?

 * From the point of view of counting nanoseconds, OO is less efficient.  You made this point in your video.  However, the cost is relatively small if the functionality being deployed is relatively large.  The _shape_ example, used in your video, is one of those cases where the deployed functionality is small.  On the other hand, if you are deploying a particular algorith for calculating the pay of an employee, the cost of the polymorphic dispatch pales in comparison to the cost of the deployed algorithm.
 * From the point of view of programmer effort, OO may or may not be less efficient.  It _could_ be less efficient if you knew you had all the types.  In that case switch statements make it easier to add new functions to those types.  You can keep the code organized by function rather than type.  And that helps at a certain level of cognition.  All else being equal, programs are about functions.  Types are artificial.  And so organizing by function is often more intuitive.  But all else is seldom equal...
 * From the point of view of flexibiliby and on-going maintenance, OO can be massively more efficient.  If I have organized my code such that types define the basic functionality, and variations in that functionality can be relegated to subtypes, then adding new variations to existing functionality is very easy and requires a minimum of modification to existing functions and types.  I am not forced to find all the switch statements that deploy functions over types and modify all those modules.  Rather I can create a new subtype that has all the variations gathered within it.  I add that new module to the existing system, without having to modify many other modules.

That last bullet was a very brief and imperfect explanation of how OO helps programmers conform to _The Open-Closed Principle_ (OCP). i.e. it is better to add new modules than it is to change existing modules.

Are there cases where using OO does not help conform to the OCP?  Certainly.  Again, if you know all the types, and you expect variation to be new basic functionality operating over all those types, then dynamic polymorphism (OO) works _against_ the OCP and switch statements work for it.  

That would be the bottom line if it weren't for one other factor:  Dependencies.

The cases of switch statements create an oubound network of dependencies towards lower level modules.  Each case may call out to those other modules, making the fan-out of the switch statement very high.  Any change to one of those lower level modules can force the switch statement, and all higher level modules that depend upon that switch statement, to be recompiled and redeployed.  That can be a very large cost.  

On the other hand, if one uses dynamic polymorphism (OO) instead of a switch statement, then those compile time dependencies are _inverted_.  The lower level modules become subtypes that depend upon the higher level base type.  The source code dependencies then point in the opposite direction of the flow of control.  This is _Dependency Inversion_, and it prevents changes at to low level modules from forcing a wave of recompilation and redeployment from sweeping through the system towards higher level modules.

This notion of inverting dependencies is the foundation of my architectural argument.  But, perhaps, this is a good place for me to stop and get your reaction so far.

**CASEY**: That's actually great, because I don't see how that argument works, so starting there will be very illuminating.

I don't disagree with the _term_ "dependency inversion", because in a sense you _are_ "inverting" something (to me, it is analogous to swapping the dimensions of an array). But from my perspective you are not creating a benefit, you are simply making the same trade again, where you favor _operand_ addition at the expense of _operation_ addition.

Specifically, suppose you have n types each supporting m operations. Any system design supporting this will therefore have O(nm) "things" in it, and the design question is how do you want to group them. We'll leave out the fact that sometimes this can be compressed (or "made more sparse") for now. Switches and classes do compress in different ways and I would like to talk about that later, but to avoid talking about too many things at once, let's first talk about the "worst case" where every operation does something different for every type.

Dynamic polymorphism is the design where you have n types containing m member functions. It ensures that if you would like to move from n types to n+1 types, then you are going to create a new type and implement m new member functions in it. This means you only have to "recompile and redeploy", as you put it, that one file, or something on the order of that.

By contrast, the switch statement design is where you have m functions each containing n cases. If we want to add a new type, we have to go through each of those m functions and add one new case each. This requires recompiling and redeploying m files instead of 1 file. I _assume_ this is the specific benefit to which you were referring, and I don't disagree with that premise.

However, suppose that you instead want to add a new _operation_. Now I am leaving n the same and moving from m operations to m+1 operations. In your model, you now have to go through _every single class_ and add that new operation, which forces you to recompile and redeploy n files, one for every class.

By contrast, the switch statement version is already separated by operation, so you do not need to touch any of the existing files (or modules, etc., whatever the storage medium is we are imagining here). You simply create one new file for this new operation, implement the n cases, compile it, and deploy it. It is the exact mirror of the type case.

So to me, there is no "win" here in the abstract. You are merely choosing _which_ programmer behavior you will make hard, and which you will make easy. By choosing dynamic dispatch, you make adding operations require more recompilation/redeployment, because it is the "inner multiple" in that grouping. By choosing static dispatch, you make adding types required more recompilation/redeployment, because _that_ is the "inner multiple" in that grouping.

Note that in both cases you are always adding the same number of _things_ (member functions or cases). The only question is _how spread out_ they were. Both cases have one type of change where the code is clustered together, and one type of change where the code is scattered, and they are exactly "inverted" in which one it is. But neither gets a win, because they are both equally good or equally bad when you consider both types of additions (types and operations).

Clearly you view this differently, because you said, "That would be the bottom line if it weren't for one other factor:  Dependencies." where "bottom line" was already the idea that you're just trading off between ease of adding types vs. ease of adding operations. So I assume that means you think this "dependency inversion" does something _more_ than that "bottom line" already did, whereas I see it as being exactly the same bottom line as before: it's just the exact same tradeoff again.

Where does your analysis differ?

**BOB**: Casey, we are on exactly the same page with one big exception: _Dependency Inversion_.  But first let me restate.

Yes, every program composed of `o` operations and `t` types has a complexity of `o`x`t`.  
 * If we use OO we can increase `t` with minimum disruption to the source code; but increasing `o` disrupts many source code modules.  
 * If we use switch statements we can increase `o` with minimum disruption to the source code; but increasing `t` disrupts many source code modules. 
 
So I believe we understand each other so far.  
 
However, notice that the issue, in both cases, is _source code management_.  It has nothing to do with the runtime execution.  So let's split this problem into two issues: Run Time and Source Code.

>_Run Time_

I propose a hypothetical compiler that produces identical binary code irrespective of whether the input is operand or operation primal.  If you give this compiler OO source code S1, it will produce binary output B1.  If you give this compiler procedural source code S2 it will produce binary output B2.  So long as the behavior of the two programs is identical, then B1 and B2 are also, byte for byte, identical.

It should be clear that a compiler like this is possible since switch statements can be (and often are) compiled as jump tables; and polymorphic dispatches can be (and often are) compiled as jump tables.  

Yes, there are complicating issues; but let's ignore them and agree that such a compiler is at least feasible.  

Thus, since they can be compiled down into identical binary code, there is no necessary Run Time difference between the OO and Procedural styles.  

>_Source Code_

As we have already agreed the swapping of `o` and `t` makes very little difference to the problem of source file management.  Individual cases may favor one style or another.  For example if we have stable operations but variable types then OO might be favored.  Or if we have stable types but variable operations then a Procedural style may be favored.

So it looks like, all else being equal, the two styles have the same capabilities.  There is no Run Time difference, and the Source Code management issues are just mirrors of each other.

>_Dependency Inversion_

But all else is not equal.  There is a radical difference in the source file dependency structures created by the two styles.  

 * **Operation Primal**: In the procedural style, in which switch (if/else) statements deploy operations over types, the source code dependencies (#include statements in C/C++) follow the Run Time dependencies.  High level modules that call lower level modules must #include those lower level modules.  So the #include chains form a Directed Acyclic Graph (DAG) in which every edge is directed from a higher level module to a lower level module and follows precisely the direction of the function calls. (Ignoring the .h .c divide which we can discuss at length later.)
 * **Operand Primal**: In the OO style, in which dynamic polymorphism is used to deploy types over operations, higher level modules are _independent_ of lower level modules.  The #include statements point in the opposite direction.  Subtypes depend upon their base types.  Thus the source code dependencies point _against_ the runtime calls, _against_ the flow of control.  The edges of the #include DAG are directed from lower level modules towards higher level modules.

The implications of this difference are:
 * When using the procedural style high level policies depend, at compile time, upon low level details.  If those low level details change for any reason, the high level policies must be recompiled and redeployed even though they have not changed in their intent.
 * When using the OO style high level policies are independent of low level details.  If those details change in implementation, the high level modules remain unaffected.  They do not need to be recompiled or redeployed.  

>_Example_

In the 1950s and 1960s we would write code that directly used the IO devices of the day.  These devices were things like card readers and punches, paper tape readers and punches, teletypes, printers, and magnetic tape drives.  If someone asked us to write a payroll application that took in employee data as punched cards and wrote paychecks on the line printer then our code would do _exactly_ that.  It would explicitly read from the card reader, and explicitly write to the line printer.  We used the _Operation Primal_, procedural, style.

But then our customers would come to us one day and ask us to read the employee data from magnetic tape, and output the paycheck data to another magnetic tape.  To them, this was a low leve detail.  The data was still the same, it was just on a different medium.  But to us it was a complete architectural rewrite.  Magnetic tape drives are very different from card readers and line printers. And so the change was very expensive, and our clients could not understand why.

To defend against this we invented _Device Independence_.  We created an abstraction called a _File_ and gave it standard operations such as `open`, `close`, `read`, `write`, and `seek`.  (The five standard functions of the UNIX IO driver).  We categoried our IO devices as _types_.  We used dynamic polymorphism (jump tables) to implement the dispatching.  By creating a stable set of operations, we forced virtually all of the variation into the types -- the different IO drivers.

From then on, we could write our applications without knowing, or caring what IO devices we were using.  And life become a _lot_ simpler for the programmer at large, and a lot less expensive for clients who made "small" changes to the media they wanted to use.  Those changes were made in the job control directives without forcing any source code change, recompilation, or redeployment of the application.

>_But wait..._

You could easily make the point that while the OO style protects high level policy from changes to low level detail, it also makes low level detail _vulnerable_ to changes in high level policy.  That's true.  And by the same token, the procedural style certainly makes high level policy vulnerable to changes in low level details, but protects those low level details from changes to the high level policies.

So the question then becomes one of architectural philosophy: Which of those two is more likely to change?  Does policy change more frequently than detail?  Or does detail change more frequently than policy?  

**CASEY**: Before getting into the details here, what do you mean by "I propose a hypothetical compiler that produces identical binary code irrespective of whether the input is operand or operation primal"? Do you mean a hypothetical _language_, where it doesn't matter which you do? Because that doesn't seem very useful, since I could just say "I propose a hypothetical language where the dependency cost is always the same" or something, and then we have nothing to discuss because we are both talking about fictitious systems. It doesn't really address how we're supposed to write actual code, which is what we're talking about here.

So are you suggesting that you think that current C++/JAVA/etc. compilers _do_ exhibit this property? I just want to unpack this so I can understand what you mean by the "run time" section above.

**Bob**: Let's make it a C++ compiler.  You pass this compiler a C++ program written with switch statements, or the equivalent program written with base classes and subtypes, and it generates the exact same binary code.  Let us further stipulate that the binary code it generates is as efficient as possible.  Such a compiler ought to be possible since switch statements and dynamic polymorphism are both often implemented as jump tables.

**CASEY**: I would again say that this is not a useful hypothetical for a discussion about real-world coding practices. In production, if you are compiling C++, the vast majority of programmers will be using CLANG, GCC, or MSVC. None of those do what you're describing. While virtual methods are always implemented with a vtable pointer in the object, switch statements can be optimized in a wide variety of ways, all of which are more optimal than an indirected function pointer (which is what all virtual calls are). The only time you will get similar optimization from a virtual function call is if it's not actually virtual because you have an explicit pointer to a derived type, and the method has been marked "final" for that type. And that is not a comparable case, because you wouldn't need a switch statement to handle that case either.

**Bob**: My apologies for the distraction.  It was not my goal to start a debate about the possible existence of a hypothetical compiler.  My goal was only to set the performance aspects of the two different schemes aside so that we could focus on the architectural issues that you have proposed that we discuss.

So, I hereby withdraw the hypothetical compiler from further consideration and recommend instead that we focus on the architectural effects of operand-primal vs operation-primal approaches.  

**CASEY**: Well, we do still need to consider the run-time part of your claim though. If I can summarize the claim, it was:

* Run-time: No difference
* Source code: No difference
* Dependency graph: Favors operand-primal

If you are withdrawing the hypothetical compiler from consideration, is the claim now:

* Run-time: Favors operation-primal
* Source code: No difference
* Dependency graph: Favors operand-primal

or is it something else?

**Bob**: So stipulated.  Please proceed.

**CASEY**: OK, proceeding from there, I'd like to now focus on whether or not there is actually a benefit from the dependency inversion that you're describing. This will probably take a few back-and-forths, but, I'll start with this:

You gave the example of low-level IO routines, like reading bytes from a tape drive vs. a magnetic drive. I, of course, agree completely that there may be many circumstances where you would like to write a lot of code in terms of some series of operations like "open", "read", "close", and not care exactly what is being opened, read, or closed. But that goal can be achieved with either architecture.

In the case of a class hierarchy, the user is passing around a base class pointer, and they call ->read() on it. In the case of a union (or any other non-hierarchy technique), you are passing around a pointer to that, and you call read() and pass the pointer as the argument. In neither case does the user have to know (or care) about what that call does.

So I just want to start with some understanding of why you chose that example. It doesn't seem to me to differentiate between the two methods at all. From the user's perspective, the only difference they will experience is syntactic - eg., they type ptr->read(...) instead of read->ptr(...). But why do they care about that? What are the specific benefits they are getting from this?

You mentioned "recompilation and redeployment". By "recompilation" I assume you mean that if you are calling through a blind table, then you do not need to recompile the calling code. By "redeployment", I'm not sure exactly what you are referring to, but I'm imagining a scenario where you would like to ship, say, several DLLs, and only send one new DLL instead of the entire set of DLLs?

So perhaps you could elaborate on those, and then add any other benefits you think are flowing from this, so I can get the full picture?

**Bob**: To begin with it appears that we agree that there is a benefit in organizing the source code such that high level policy does not depend upon low level detail. I think we also agree that this benefit is _human_ as opposed to mechanical.  By that I mean that the computer doesn't care whether high level source code is independent of low level source code.  Only the humans care about that.

I infer this agreement from your statement: 
>_"I, of course, agree completely that there may be many circumstances where you would like to write a lot of code in terms of some series of operations like "open", "read", "close", and not care exactly what is being opened, read, or closed."_

It's probably worthwhile listing some of the reasons _why_ this separation is important to humans.

 * It allows us to use, modify, and replace low level details (e.g. IO devices) without impacting the source code of the high level policy.
 * It allows us to compile high level components without needing the compiler to read the low level components.  This protects the high level components from changes to those low level components.
 * It allows us to set up a module hierarchy that has no dependency cycles. (No need for `#ifndefs` in the header files. ;-)  Which keeps the order of compiles and deployments deterministic.
 * It allows us to break up our deployments between high level and low level components.  So, for example, we could have a high level DLL/JAR/EPROM and a low level DLL/JAR/EPROM.  (I mention the EPROM because I used precisely this kind of separation in the early 80s with embedded hardware that had to be maintained in the field.  Shipping one 1K EPROM was a lot better than shipping all 32 ;-)
 * It allows us to organize our source code by level, isolating higher level functionalities from lower level functionalities. At every level we eschew the lower level concerns, relegating them to source code modules that the current level does not depend upon.  This creates a hierarchy of concerns that is, in human terms, intuitive and easy to navigate.  

You then said that this kind of isolation is possible with "either architecture". For the sake of this discussion I will accept the use of the term "architecture" to mean one of the two styles we are debating: operation primal vs operand primal.  

So, are the bullet points above possible with both architectures? Let's set up an experiment.

Here is the high level policy of my payroll system written in Java(ish).  I would like this source code module to be independent of all low level details.  

    public class Payroll {
      public void doPayroll(Date payDate) {
        for (Employee e : DB.getAllEmployees()) {
          if (isPayDay(e, payDate)) {
            Paycheck check = calculatePaycheck(e, payDate);
            pay(e, check);
          }
        }
      }
    }

This module is very high level.  It states the highest level algorithm for paying my employees.  It does not mention the fact that some employees are salaried, others are hourly, and others are commissioned.  It does not mention the fact that hourly employees are paid weekly, commissioned employees are paid twice a month, and that salaried employees are paid monthly.  It does not mention the fact that hourly employees are paid time and a half for overtime, or that commissioned employees are paid a base salary plus a commission on their sales reciepts.  It does not mention the fact that some employees want their paychecks directly deposited, while others want them mailed to their home, while still others want to pick up their pay from the paymaster.

Using the operand primal style I can very easily ensure that the above module remains independent of all those details and that no `import` statement in this module mentions, directly or transitively, any module that implements those details.  I can compile the above module into a JAR file that could remain unchanged while the JAR files that contain all the details thrash endlessly under a barrage of requirements changes.

Can I do that with the operation primal style?  You mentioned passing around a `union`.  I presume my module would have to `import` that `union`.  So how can I define that `union` such that it makes no mention of any of the afore mentioned details. I suppose we could use opaque (`void*`) pointers, and take advantage of the late binding of the linker to push the problem down one level.  But I'd like to see what you have in mind.

You mentioned that the difference in my _Device Independence_ example was purely syntactic -- a matter of `ptr->read(...)` instead of `read->ptr(...)` by which I think you meant `ptr->read(...) vs read(ptr, ...)`  But I think we disagree on that.  In my example the dynamic polymorphism of the _File_ abstraction was pushed across the boundary of the operating system.  The high level interface _File_ was usable by all applications.  The low level implementations were device drivers linked into the OS.

This meant that I could write an application using the _File_ abstraction.  I could compile, link, and deploy it.  And it would still have no dependency upon, nor reference to, the low level devices it was intended to control.  That linkage occurred at runtime when the pointers to the `open/close/read/write/seek` functions were loaded into the _File_ vtable.  Thus, new IO devices could be added to the OS, and my application could control them, without the need to recompile, relink, and redeploy it.  It seems to me that might be tricky with the `union` approach.

You asked me what I meant by recompilation and redeployment.  Recompilation is the act of rebuilding the application.  This can be as simple as `cc app` or as complex as some gigantic `makefile` might (um) make it.  The development team might have to build the app for N different plaftorms, and run all the tests for all those patforms.  The build procedure can take milliseconds, or it can take hours, depending on the complexity of the application.

Deployment has a similar range of complexities.  It might simply mean `cp app /usr/bin` or it might require a massive deployment of executables around a network of servers. It might require a very carefully tuned and timed startup procedure.  Depoyment can be a matter of milliseconds or days depending upon the complexity of the system.

I hope this helps to answer your questions; because I am eager to get on with the _architecture_ part of our discussion.

**CASEY**: It sounds like you're assuming that the programmer has to expose certain things in the "union" case that they do not actually have to expose. In practice it is no different from the hierarchy case in terms of how much _data_ you wish to expose. In the hierarchy case, for example, you _could_ put a bunch of data in the base class, which exposes it to the user. It's a _choice_ not to do that, which you make when you want to insulate the user from changes to that data.

The same is true in the union case (or any other non-hierarchy method - there are others, which are often better than the union method depending on the circumstances). Here is what the user would see in, say, a typical H file of a non-hierarchy library:

```
struct file;
file *Open(...);
void Read(file *File, ...);
void Close(file *File);
```

There is no need for any of the things that you're suggesting to be exposed. The only reason you would expose them is if you would like to get the performance increase that comes from allowing the compiler to optimize across the call. This choice is up to the maintainer, and they can choose to get the extra speed if they know that isolation is not beneficial, or they can choose not to get the extra speed is isolation is more important.

This design doesn't require exposing anything you didn't _want_ to expose. Furthermore, unlike the hierarchy case, it does not impose any constraints on the designs of the datatypes. Contrary to what you suggested, it has _less_ constraints across the interface boundary, not more.

Why? Because in the hierarchy case, you are moving the dispatch code from inside the library to outside the library. At compile time, the user of the library code is having the vtable dispatch code _compiled in_ to their program. Instead of just calling a function address and passing a pointer they know nothing about, which is what happens in the non-hierarchy style, they must _know_ how to translate that pointer into a function address by reaching into the operand and pulling something out (in this case a pointer to a vtable).

Another way to say this is, you _are_ actually pushing an implementation detail across the boundary with the hierarchy design: you're mandating how operand pointers turn into function pointers, which is a _constraint_ on the layout of the underlying operand. It _must_ have that additional data stored in _precisely_ one way, and cannot be changed on either side of the boundary without recompiling the other side. This is why, for example, C++ vtable layouts had to be standardized: because otherwise there would be no way for two programs compiled with different compilers to call each other's virtual methods.

Just to further illustrate this point using an extreme example, imagine if the library author decided they no longer want to use pointers for some internal reason. In the non-hierarchy case, this "just works". A pointer is just a number, and since the user of the library does not do anything with that number except pass it back to other library functions, it can be _anything_. It could just be an index into some table the library maintains. This is free to do and trivial to implement in the non-hierarchy case because the user's code never manipulates the pointer.

Not so in the hierarchy case. In the hierarchy case, the user looks into the pointer all the time, to pull out the vtable address. There is no way to hand the user something else, like an index, because the vtable scheme has to have been agreed upon systemically by unrelated _companies_, not just teams (in this case, the compiler vendors). So it simply can't do anything like this.

If anything, I see your arguments as actually being in favor of the non-hierarchy method, even though you seem to be saying they favor the hierarchy method. But I don't see how. The non-hierarchy method is a many ways a _superset_. It has more range both in terms of isolation from changes (no vtable scheme exposed), and in terms of performance because at the other end of the spectrum, the library author can choose to provide the entire source and give the user the option of compiling everything together for optimization purposes.

So I am not seeing where the benefits of hierarchy-based design come in here. They seem strictly less flexible at both ends of the "compilation and deployment" spectrum. And this "worse at both ends" aspect of the design is a major reason I think the hierarchy case should be abandoned as a general architecture: the programmer never seems to be better off when using it. If they wanted isolation, they got _less_ isolation with a hierarchy than with a blind pointer. If they wanted performance, they got _less_ performance because optimization can't occur across base class virtual calls no matter how much of the source code you expose to the user. It's _always_ worse than the alternative.

And the other important point here is that it is _continuous_ for the library author to make this decision in the non-hierarchy case. They can write a library that exposees only blind pointers, and then later decide to expose some or all of what those pointers point to, and the routines that operate on them, if/when they find that performance becomes a priority for a user. Even better, they can decide to give the user that choice if they wish: because the code itself doesn't change at all, only the amount of it exposed to the user changes, the user could choose how much exposure they want. If the user doesn't need the extra performance, they build without using the exposed source. If they do, they build with it, knowing they will have to recompile their code if the structures change in a future revision.

Nothing like this is possible in the hierarchy case: the library vendor would have to manually rewrite everything to eliminate the virtual call hierarchy first in order to make a change like this, which requires substantial effort, perhaps even a prohibitive effort in a large library. This is because the choice to base the design around virtual calls prevents compiler optimization across calls _everywhere_, even internal to the library, and there is no easy fix.

So hopefully this explains the confusion?

**Bob**: There's a lot to unpack in there.

 * You and I are apparently using a different definition of the term *union*.  I was assuming you meant the traditional C `union` in which the data members share the same memory space.
 * The code you presented is, in fact, the opaque pointer, linker bound solution I was alluding to above.  I said `void*` and you used `file*` with a forward declaration of the `struct file;`  So on that account I think we agree.
 * You repeatedly used the term _hierarchy_.  I presume you were referring to _inheritance_ hierarchies.  However, using such hierarchies is not my position in this debate.  There are times when a hierarchy can be useful; but in general I prefer the interface/implementation approach.  In C++ that's a pure abstract class with no implementation (the interface), and a concrete class that implements that interface.  I suppose you could call that a two level hierarchy if you like; but I want to be clear that I'm not a big proponent of deep inheritance hierarchies. 

I agree that if you use the C style .c/.h split and ensure that the only thing that appears in the .h files are _forward declarations_ without any definitions, then you can hide the implementation quite nicely.  That's because the old C style .c/.h split _is_ dependency inversion.  (indeed, it is a two level hierarchy) The .c file contains the low level implemenation, and it depends upon the high level interface in the .h file.  

>_As an aside, this pleasant .c/.h split was corrupted by C++.  Stroustrup needed to know the size of objects in order to implement the `new` keyword.  So he forced member variable definitions into the .h files -- thus destroying encapsulation, forcing the horrible public/protected/private hack upon us, and making dependency inversion much more difficult.  (e.g. the PIMPL pattern.)_

I also agree that the dependency inversion of the .c/.h split provides the protection I desire for compilation and deployment.  That protection comes from the fact that the linker does _late binding_ of the external references to the external definitions.  It's that late binding that allows the individual source files to be compiled independently.  Indeed, nowadays we use the old trick from the '60s of a linking loader to do all the linking at load time.  But that's another story.

So far we are on the same page with the minor exception that I'm still not sure what you meant by `union`.  But let's let that pass for the time being.

Perhaps this is a good time to reassess the differences and similarities in our position.  

 * You are defending the .c/.h approach of inverting dependencies and using link-time late binding to protect against recompilation and redeployment.  
 * I am defending the interface/implementation appraoch of inverting dependencies (which is essentially the .c/.h approach with a different syntax), and using run-time late binding (dynamic polymorphism), to protect against recompilation and deployment.  (As an aside, I actually use, and recommend, both approaches.  There are times when link-time binding is adequate and other times when runtime-binding is invaluable).

Oddly, we seem to have replaced the operand/operation concept with the kind of late-binding we prefer.  It should be clear that the both operands and operations can be bound after compile time since both are really just functions that take arguments.  So the real issue between us is not operation vs operand, it is link-time binding vs. run-time binding.

Your next assertion was that there was a difference in the amount of information that had to cross the boundary between the interface and the implementation.  Your argument was that both sides had to agree on the format of the vtable.  That's certainly true in C++.  Stroustrup created an indexed table of pointers to functions, and both sides must agree on the indeces for the method names _at compile time_.  I completely agree that programmers must ensure that the compilers used for the interface are compatible with the compilers used for the implementation.

Other language systems use different approaches that don't require that index agreement at compile time.  Some use string matching at runtime.  Others could negotiate the indeces at link time.  There are a multitude of strategies to mitigate this.  However, the indeces of vtables is just the tip of a much larger information iceberg.

If different languages, or even different vendors of the same language, are used across the interface/implementation boundary then they must agree upon a whole plethora of formats.  They must agree on endians, on the order of arguments and local variables within the stack frame, the size of data types, and the format of any try/catch markers on the stack.  The coordination of vtable indeces pales in comparison to all the other stuff they have to agree upon.

Even if we stipulate that the language systems are identical across that boundary, the interface and implementation must agree on the names and signatures of the functions.  So, given these facts, I'm not disposed to put a lot of weight on your argument about the vtable format.

Your argument that in the .c/.h case the user has the opportunity to gain optimizations by _compiling_ the modules together rather than using the late binding of the linker certainly has merit.  If you abadon _all_ late binding and give the compiler access to _all_ the source code, then the compiler can do quite a bit of magic.

However, this puts me in the position of invoking the hypothetical compiler that can do all that magic if it sees all the source code that uses dynamic dispatch.  Dynamic dispatch certainly adds complexity to the problem; but there's nothing impossible about it.  And, indeed, there are language systems that try to make such improvements both at compile and runtime.  

But given the current state of most compiler I think your argument has enough merit to warrant half-points.  ;-)

Now let us say that we _want_ to protect our source code modules from recompilation and redeployment when low level details change; and that we are therefore going to use some kind of dependency inversion and late binding to provide that protection.  That means we are not going to let the compiler to see _everything_ but are intentionally _hiding_ information from the compiler thus preventing it from doing all the magic optimization it could do.

What, in this case, is the advantage of run-time binding over link-time binding.  Why would we want the vtables, or the various other forms of dynamic dispatch that our languages offer?  

Sometimes we don't.  That's why C++ gave us the `virtual` keyword.  That's why java has `static` functions.  Sometimes we just bloody don't want that dynamic dispatch sitting between the caller and the callee.

But sometimes we do.  For exmaple: _Plugins_.

Visual Studio provides dynamic dispatch of functionality within their product so that vendors like _Jetbrains_ can build plugins like _Resharper_.  The use of linking loaders means that we can link these kinds of plugins into our applications at runtime, and the dynamic dispatch means that the application can be entirely ignorant of the source code of the plugins.

Related to plugins are frameworks like Rails.  Such frameworks could be statically linked if necessary; but otherwise convey similar advantages.  The framework need know nothing at all about the source code of the application that uses it.

In both cases there is a profound asymmetry based on runtime bound dependency inversion.  The high level side is ignorant of the lower level side and _invulnerable_ to it's changes.  The low level side is dependent upon the high level side, and must react to changes made to the high level.   

This vulnerability assymetry means that Microsoft is invulnerable to Jetbrains, but Jetbrains must react every time Microsoft makes a change to VS.  

This same kind of vulnerability assymetry can be advantageous between, or even within, development teams.  We don't want the business rules to be vulnerable to the GUI or DB.  Indeed, we'd like the GUI and DB to be plugins to the business rules.  

But I think that's enough for this go-around.  So let me summarize where I think we are.

 * We both agree that dependency inversion and late binding are valuable for protecting systems against recompilation and redeployment.
 * We both agree that link-time late binding can be used to an advantage.
 * We both agree that it is sometimes necessary to abandon late binding so that the compiler can optimize.
 * And I HOPE we both agree that there is a time and place for dynamic late binding; such as plugins and frameworks.
 * And lastly, I hope we both agree that all these issues are human issues.  The computer does not care.

**CASEY**: Regarding "You and I are apparently using a different definition of the term *union*.  I was assuming you meant the traditional C `union` in which the data members share the same memory space.", you were assuming correctly: the "file \*" I'm talking about would be exactly that kind of union. In other words, inside the library, it is:

```
enum file_type
{
  // Types of files go here
};
struct file
{
  file_type Type;
  union
  {
    // Data storage types corresponding to file_type go here
  };
};
```

My point in the example is to demonstrate that you are able to either hide this or not depending on your preference. If you would like the user to be able to get optimization by compiling "through" the union, you put this in the H file. If you don't, you don't. It's your choice.

Furthermore, you only need that union if the particular usage of the library _actually is_ polymorphic, which it might not be. For example, if the particular system you're running on only has one type of drive (to continue the example), then the version of the library for that platform doesn't need the union anymore, meaning that the linking will produce a direct link to the correct function. Contrast this with the inheritance hierarchy version, which _does not_ do that: it will still compile to an indirected vtable dispatch, which is less efficient for no reason. Of course, this is not an efficiency I actually care about much, because if we are already calling across a library boundary then it is unlikely to actually matter that much. But I'm just pointing it out as yet another point in favor of not using virtual functions.

My point here relates back to my original point, which is that discriminated unions (C++ also has a janky std library version of these called "variants") seem to always be the better rule of thumb. If you use an inheritance hierarchy as your preferred mechanism for polymorphism, you are effectively "stuck". You cannot actually decide to change that decision later, because the code has already been written in such a way as to prevent optimization "through the union". On the other hand, if you use discriminated unions like this as your default case, then you can change your mind whenever you want. Start off with it hidden, if you want to keep compilation dependencies down. Move the structs and functions up into the .h file later later if you want to increase optimization.

Regarding "Oddly, we seem to have replaced the operand/operation concept with the kind of late-binding we prefer", not really. I don't actually prefer this style of "guard against people using the code". Nor do I think it's how you would normally write things unless you actually expect there to be a great amount of late-addition of types to the system. I agree broadly, for example, with the "Data-oriented Design" people: most of the time, you would be better off if code always knew what type it was dealing with. If you have circles, rectangles, and triangles, then you have three separate arrays, one for each type, so that there never _is_ a dispatch.

So I think the idea of structuring code around swappable types is generally wrong. I think it should only be used when you are specifically designing the boundary of a library or plug-in system, and rarely anywhere else. I really am "operation first", never "operand first". The point of the `file` example was to point out that it is _also_ trivial to take union-style code and hide its implementation details if you want to. It is incorrect to suggest that it is "worse" than inheritance hierarchies at this somehow. I _disagree_ than inheritance hierarchies have a "dependency inversion" advantage, and my understanding of your argument was that that was what you were suggesting.

I'm also not sure how "And lastly, I hope we both agree that all these issues are human issues.  The computer does not care." squares with "We both agree that it is sometimes necessary to abandon late binding so that the compiler can optimize."  If the computer didn't care, we wouldn't need to worry about optimization. But the computer definitely _does_ care, because it runs some kinds of code quickly and other kinds slowly.

So if I had to restate the summary, I would say:

* We both agree that _complete_ hiding of implementation details can be useful, and that people should know how to do it.
* We disagree on how often they should be hidden; you think the answer is "most of the time", I think the answer is "only in specific circumstances"
* We disagree on how important the computer is. I think we need to think about the computer most of the time, whereas you do not.

And by "complete hiding" I mean the kind where the user literally cannot see the details - eg., not just a "private" section of something they can see but not use, but rather their code is compiled without any knowledge of the internals at all.

**Bob**:  It seems we have come a long way and wound up at an agreement on just about everything other than individual preference; which is likely driven by the different environments we live in.

Thank you for the `union` clarification.  I understand it now.  

I'll quibble with you a bit on the distinction between operand vs. operation, but I don't think the quibble is particularly important.  In the end it's all `f(x)` regardless of how you spell it.

As for the "human" issue.  Performance is a human issue.  The computer doesn't care how fast or slow an algorithm runs.  But I think that horse is dead now.

So, given that we understand each other so well, let's talk about this issue of preference driven by environment.

In my work I don't care about nanoseconds.  I almost never care about microseconds.  I sometimes care about milliseconds.  Therefore I make the software engineering tradeoff towards _programmer convenience_, and long term readability and maintainability.  This means that I don't want to think about the hardware.  I don't want to know about Ln caches, or pipelining, or SIMD, or even how many cores there are in my processor.  I want all that abstracted away from me, and I am willing to spend billions of computer cycles to attain that abstraction and separation.  My concern is _programmer cycles_ not machine cycles.

I have, in the past, worn the other shoes.  There was a time in my career when microseconds had to be conserved, when I had to meet submillisecond deadlines, and I counted and conserved cycles as carefully as Ebenzer Scrooge.  So I think I understand your concern fairly well.  

That is why I generally agreed with your video that kicked this whole discussion off but continue to disagree with that video's general conclusion that Clean Code is bad.

Let's set aside, for a moment, that the definition of "Clean Code" that you used in that video did not always align with the recommendations I made in my _Clean Code_ book.  The point is still valid.  Programmers who use the techniques that I recommended in that book _will be trading computer cycles for programmer cycles_.  And that's a good thing in most of the software teams and organizations that I work with.

I tip my hat, however, to your valid concern that myopically focussing on programmer cycles and utterly ignoring computer cycles can lead to horrifically inefficient systems.  None of us want that; just as none of us want to work in a world where we focus solely on computer cycles to the detriment of programmer productivity.

***

There are a gazillion other things we could go on to discuss.  We could argue about the dependency structure of switch statements vs polyomorphism.  We could talk about the best ways to cross architectural boundaries, and when link-time binding is better than run-time binding.  But, honestly, I think we have restored balance to _The Force_ and have achieved our primary goals. 

So unless you have something you think is critically important to address, I recommend that we call an end to this dialog.

I'd like to thank you for a very stimulating, and civil, discussion.  I look forward to other such discussions in the future.  

>_see cleancodeqa-2.md_
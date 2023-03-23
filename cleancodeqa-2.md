**CASEY**: I'm certainly happy to end the conversation here if you'd prefer. I appreciate you taking the time to do it, it's been rather lengthy :) But my closing analysis doesn't really line up with yours. So I'll include my response here for the record, but, if you'd rather not keep going, that's fine with me, too.

Essentially, I don't think our differences come down to preferences. I think the problem is that I'm not seeing where the developer cycles are being saved. I understand what you are _intending_ to do when you say that you want to trade CPU cycles for programmer cycles, but I don't see _where the actual savings come in_.

That's what I was trying to get at. Since we only just started talking about architecture, I tried to drill down on "dependency inversion" to see where you're seeing the tradeoff, because that was the one thing that you said was a _positive_ for your architecture style (runtime was the negative). But I don't feel like we ever got to the part where the benefit was made clear.

There was an example about file I/O, but as hopefully I demonstrated with the H/C file example, it does not require any new design, nor even any actual _work_, to simply cut the dependency at the interface boundary _no matter what_ the underlying architecture was. Any set of N function calls to a device driver can have the device driver swapped with another device driver, and now that driver implements those function calls. This is how it worked before OOP, and it's how it still works now in many file I/O subsystems. So I can't really use that to evaluate any "programmer cycle savings" for any specific architecture. The architectural benefits have to be demonstrated somewhere _below_ this cut, because there is not really any architecture I can think of that can't be cut in this way.

Now, thinking about the design of a file I/O subsystem, there certainly _are_ many things that I think are important, both in terms of "CPU cycles" and "programmer cycles". But in order to get into those you'd have to go beyond the basic premise that you want to be able to have read() calls that get fielded by different devices, OSes, or drivers.

If we end the discussion now, we also don't get a chance to talk about your payroll example, which was:

```
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
```

You listed a bunch of things you wanted the implementation of `isPayDay()`/`calculatePaycheck()`/`pay()` to handle:

* Employees can be salaried, hourly, or commissioned
* Hourly employees are paid weekly
* Commissioned employees are paid twice a month
* Salaried employees are paid monthly
* Hourly employees are paid time and a half for overtime
* Commissioned employees are paid a base salary plus a commission 
* Employees can get their paychecks directly deposited, mailed to their home, or held at the paymaster

That certainly sounds like a well-specified problem to me, so perhaps you can show me what operand-primal design you had in mind for the implementation that provides all these things and "saves developer cycles" over the operation-primal one? Because obviously, the code you wrote above _does not imply either one_. A programmer could do everything from inheritance hierarchies to a giant hard-coded calculatePaycheck function to satisfy your requirements here, so the savings in developer time is presumably coming from the _ease_ with which you can implement the underlying functions, and the corresponding ease with which you can make changes to them. Yes?

So if you _would_ like to elaborate on that design here, we can keep going. Or, if you've got a github where you implemented this example, you can point me to it and I'll go read it.

If not, no worries, we can call it a day.

**Bob**: I'm happy to continue.  I've moved this discussion to file -2.

You raised two topics above.  The saving of programmer cycles, and the utility of dynamic binding.  I think we need to separate these two concerns.  

#### Programmer Cycles.
Regarding programmer cycles vs computer cycles.  Take a look at the file `tdox.c` in this respository.  Perhaps you've seen this before.  If not, if you compile and run it you'll get a cute surprise.

The point, of course, is that there are programming styles that can waste massive amounts of programmer cycles, and there are styles that concerve programmer cycles.  I have a style that I have refined through hard experience over the last 50 years; and I believe saves me cycles.  I recommend it to others based upon that experience and that belief. This style is based upon far more than just the occasional use of dynamic polymorphism.  Rather it focuses on function size, naming heuristics, code organization, dependency management, and quite a few other parameters. 

Can I prove my belief?  Not mathematically; just as I am sure that you cannot mathematically prove that your favorite style saves more or less programmer cycles than mine.  All either of us can do is make _human_ arguments based upon our own particular perceptions of programmer psychology.  If you'd like to go there, we can certainly try.  But I fear we could get caught in a subjectivity trap.

#### Dyamic Binding
So let's first focus on the file I/O example.  Yes, of course, you can cut the dependency at the interface boundary and use link-time late binding to protect the applications from changes in the IO devices.  The switch statement (or if/else chain) that selects the IO device is kept in the OS and the application remains ignorant of it.  However, the OS has to be linked with all the drivers in order for the switch statement to be able to invoke them.  If we want to add a new driver, we need to link it into the OS and modify the switch statement accordingly.  

Let us say, however, that we have a system that is continuously running and we cannot afford to take it off line to re-link the OS whenever a new IO driver is created.  Rather we want compile that new IO driver into a DLL, add it to a special directory, and let the OS hot load it.  That's a use case where dynamic binding comes in pretty handy. 

Do we agree so far?

**CASEY**: Well, if we are just talking about the fact that at various points we want to use a dynamic linker in some way (meaning either we overwrite source code displacements or we patch a function table), then certainly we agree. Any time we want to load something dynamically, we need a dynamic linker, sort-of by definition (unless you are in a completely JIT-ed environment, which is very rare today).

So I don't disagree about that. But what I am trying to get at is which practices make the dynamically-linked system more or less likely to "save programmer cycles". I would argue - very strongly in this case specifically, but in most cases generally - that enums/flags and if/switches are much better than classes for this design. I think they are much better _along every axis_. They will be faster, easier to maintain, easier to read, easier to write, easier to debug, and result in a system that is easier for the user to work with as well.

But my _understanding_ - and feel free to correct me if this entire conversation is based on a misconception - is that you think classes with virtual functions will be the thing that does all of these _except_ perhaps the "faster" part (the aforementioned trade of CPU cycles for programmer cycles). While I agree there isn't a mathematical proof one way or the other, the purpose of a discussion like this is to get at the specifics, so seeing what each person calls a good implementation is very useful even if neither of us changes our minds. At least we _know_ specifically what the other person is talking about in practice, whereas right now I don't really know what the claimed savings is. Once we drill down to it, I may totally disagree that it's a savings, but at least I'll know _exactly_ what you're talking about, whereas at the moment I don't feel like I do.

Since the file example has gotten the most traction thus far, perhaps I can prevail upon you to show me what the design would be that you'd actually propose for something like this? Because the devil is in the details with things like programmer productivity, I'd like to keep it as real-world as possible, so perhaps we say that this is going to be a raw read/write API, so we do not have to include things like file metadata, to keep the scope small for purposes of discussion.

So perhaps we imagine a real modern operating system - say Microsoft Windows or Linux - is providing a new raw IO interface. It needs to:

* Allow the user to specify a particular raw device out of several that might be in the system.
* Allow the user to read n bytes from a specific offset on the device to a user-provided piece of memory
* Allow the user to write n bytes from a user-provided piece of memory to a specific offset on the device

What does this interface look like if designed properly according to your design principles?

**Bob**:  I guess that depends a lot on the language and the application. Java can read bytes from an `InputStream`.  In clojure I'd likely do something simple like slurp in the whole file and navigate in memory to the bytes I need.  But I think you are asking a different question.  I think you want me to continue the story I told about the '70s and the Unix style IO interface.  

I'm not sure it's possible to improve upon that at the lowest levels.  The vtable of `open`, `close`, `read`, `write`, and `seek` are pretty good.  This scheme allows me to write the following program to copy one device to another without knowing what the device is:

	void copy() {
	  int c;
  	  while((c=getchar()) != EOF)
  		putchar(c)
	}

Where `getchar` and `putchar` are helper functions that call `read` and `write` on `stdin` and `stdout` repspectively.  If you compare this code to the kind of code that we had to write in the '60s to do roughly the same thing, the savings in programmer cycles is profound.

Does the above code depend upon dynamic polymorphism?  In UNIX it is certainly implemented that way.  But, as you pointed out earlier, it would be possible to create an OS interface that used link-time binding instead of run-time binding to accomplish the same thing -- with the exception that the IO drivers could not be dynamically loaded.

>_I could stop here and simply say that there are times when the link-time approach is better than the run-time approach; and that's true.  However, you said that enum/flags and if/switches are better in most cases. So let's examine that._

First we need to acknowledge that since the 90s there has been a very large emphasis on dynamic linking.  The creation of DLLs, Jars, and Shared Libraries moved the linker into the loader.  The reason this was considered valuable was so that we could build our applications using a plugin architecture.  You may remember `ActiveX` and DCOM, all that stuff from back then.  It ought to be clear that link-time binding that uses `if/switch` below the interface _impedes_ the plugin strategy; but let me explain why.

In the if/switch case, what does the OS look like?  Each of the five interface functions mentioned above probably has a switch statement in it.  It looks something like this in C(ish) code:

`file read.c`

	#include "devids.h"
	#include "console.h"
	#include "paper_tape.h"
	#include "..."
	#include "..."
	#include "..."
	#include "..."
	#include "..."
	void read(file* f, char* buf, int n) {
		switch(f->id) {
			case CONSOLE: read_console(f, buf, n); break;
			case PAPER_TAPE_READER: read_paper_tape(f, buf n); break;
			case...
			case...
			case...
			case...
			case...
		}
	}
	
This is a fairly ugly source file.  The more devices there are the bigger this file grows both in terms of `#include` and `case` statements.  The number of outgoing dependencies also grows, so this module has a very large _fan-out_.  And, remember, there are five of these files.

Whenever a new device is added, all five files must be updated with the new device.  Whenever any of the `#include`d header files change, all five files must be recompiled.

Where are the device IDs like `CONSOLE` and `PAPER_TAPE_READER` defined?  They are `#define` macros in the `devids.h` file.  Whenever a new device is added to the OS, the `devids.h` file must be modified to add the new `#define` macro.  And, of course, any source file in the OS that `#include`s `devids.h` will have to be recompiled.

What this means for the plugin architechture is that you can't simply write the plugin.  You must instead modify, and increase the _fan-out_ of, a large and growing source module, and somehow link that in with your new plugin.  It's possible to do this with DLLs/Jars/SharedLibs, but it's not fun.

It also introduces an administration headache.  In a multiple team environment, you've got to keep control over those switch statements.  You don't want to create _DLL-HELL_ by allowing multiple teams to have their own versions.  

---

The vtable approach used by UNIX changes things around significantly.  The IO drivers can be loaded at any time.  When an IO device is selected the five functions are simply loaded into the `file`'s vtable.  There is no growing source file that steadily increases in _fan-out_.  There is no `devids.h` file to spur extra recompiles.  When new devices are added, nothing else has to be recompiled or relinked.  The DLL is simply loaded and registered into the set of devices.

---

Now the problems I've just outlined are quite common in modern applications.  It is not at all uncommon to see switch statement scattered throughout the body of the code -- all with the same cases but with different targets.  There can be a lot more than five; and they reproduce like gerbils.  

But this is likely a good place to pause and say that I think there is a time and place for both link-time and run-time binding.  There is a time and place for both if/switch and dynamic polymorphism dispatch.  

I think it is fair to say that I lean more towards the runtime polymorphism side of that divide.  But then I don't work in constrained environments.  Memory and cycles don't mean a lot to me anymore.  What matters most to me is source file organization and the minimization of the impact of source code dependencies.

---

Lastly, I just watched a video you did some time back on the principles of Reuse.  https://youtu.be/ZQ5_u8Lgvyk.  This is a great video.  It is densely packed with very good information that you presented very competently.  

In that video you told the story of how you tried, and failed, to create a reusable framework.  You said that failure, and the subsequent more successful attempts, caused you to learn a lot.  That learning was the source of the principles you presented.

Your experience is eerily similar to one I had in the early '90s.  My team and I had 36 applications to write in C++.  We had limited time.  The applications had many similarities and we pitched a reusable framework to our customer.  We had no idea how hard this was going to be.  Our first attempt at this framework was developed in concert with ONE of the 36 applications.  It tooks us a very long time to create.  When we were done we tried to reuse that framework in four more applications, and failed horribly.  So we adopted a new strategy.  We stripped that framework down and rebuilt it; but only with elements that were used in all four of the applications we were writing.  Again, it took a long time, but when we started the next four applications, the framework fit like a glove; and we finished all 36 well in advance of our deadline.

We learned a lot from that experience.  That learning is one of the sources of the SOLID principles, and the Clean Code strategies that I recommend to others. 
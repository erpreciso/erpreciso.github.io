---

title: Reflections on the Pragmatic Programmer way of code
date: 2016-06-02 13:26 +02:00

---

Sooner or later, someone will have to work with your code. And it's your duty to prepare a clean working area for her. Starting now.

[This book](https://pragprog.com/book/tpp/the-pragmatic-programmer){:target="_blank"} is a must-read for anyone that would like to write successful code, but, having a look at my simplified checklist of suggestions, you can easily realize that you can apply to every product you're working on.

## What makes a Pragmatic Programmer?

* **Be an Early adopter aka Fast Adapter**

Don't be afraid of change: the sooner you discover and try, the larger will be the competitive advantage.

* **Be Inquisitive and a Critical Thinker**

Scratch the surface, and explore what's underneath. Don't stop at the first sight.

* **Be Realistic**

Don't let your dreams take you away (too much) from reality.

* **Be the Jack of all Trades**

Learn everything you can; it will be handy one day.


## Pragmatic Programmer ..tips? I would say code of conduct

* **1.** Care about your craft

Only in this way you give the best of you. And that's what the world needs.

* **2.** Think! about your work

Don't lose the direction on what you're doing. If you go too far from the planned route, it will be harder to go back on track.


## A Pragmatic Philosophy

* **3.** Provide options, don't make lame excuses

Nobody cares about excuse, but everyone is happy to have someone else preparing possible solutions. Take responsibility. It pays off.

* **4.** Don't live with broken windows

Don't let entropy enter in your work. Fix broken stuff, or signal it.

* **5.** Be a catalyst for change

People find easy to join an ongoing success, rather than start from scratch.

* **6.** Remember the big picture

Constantly review what's around you, not only what you're doing.

* **7.** Make quality a requirement issue

But know where to stop, evaluate the principle of good-enough.

* **8.** Maintain your knowledge portfolio

What's behind a big man? A bigger backpack. Fill it with skills.


* invest regularly in your skills, as a habit;
* diversify;
* manage risk, balance between high risk/high rewards and a more conservative approach;
* try to buy low and sell high; add the value of your processing;
* review and re balance periodically;
* get wired with the world.

Your second most precious tool is your experience.

What's the first? Your thinking.

* **9.** Critically analyze what you read and hear.

* **10.** It's both what you say and the way you say it.


## Learn to Communicate!Remember the WISDOM acrostic

* What do you *W*ant them to learn?
* What is their *I*nterest in what you've got to say?
* How *S*ophisticated are they?
* How much *D*etail do they want?
* Whom do you want to *O*wn the information?
* How can you *M*otivate them to listen to you?

And follow a simple checklist:

* Know what you want to say: which is the ultimate message that should stick in the listeners?
* Know your audience: interest in the topic? skills?
* Choose your moment: pace the conversation, do not try to stay always high but go up and down; acceleration is more tangible than constant motion
* Choose a style: avoid copying and pasting from various sources without even rielaborating
* Make it look good: sometimes only a few cosmetics are enough to give a terrific looking
* Involve your audience: leverage their emotions, and ideas will stick
* Be a listener: collect feedbacks, they will make you better
* Get back to people: don't leave questions unanswered


## A Pragmatic Approach

* **11.** DRY: Don't Repeat Yourself

Every bit of knowledge must have a single, unambiguous, authoritative representation within a system

How does duplication arise?

* **Imposed duplication**: feel you have no choice - the environments seems to require
* **Inadvertent duplication**: you don't realize you're duplicating info
* **Impatient duplication**: you get lazy and it seems easier
* **Interdeveloper duplication**: multiple people from a team duplicate.

* **12.** Make it easy to reuse

Work from the beginning with the future in mind: is there any chance what you're doing now may be reused?

* **13.** Eliminate effects between unrelated things

Orthogonality.

* **14.** There are no final decision

Reversibility. Don't assume decisions are cast in stone, make things reversible.

* **15.** Use tracer bullets to find the target

Develop small chunks, ship, gain feedbacks, reiterate.

Advantages for this approach?

* users get to see something working early
* developers build a structure to work with
* you have an integration platform
* you have something to demonstrate
* you have a better feel for progress

* **16.** Prototype to learn

Give the user a first toy to play with; otherwise called mock-up. And start the most important activity: collect feedbacks.

At this stage you can reasonably ignore:

* correctness
* completeness
* robustness
* style (you can disagree on this, and it's acceptable)

but keep an eye on the design.

* **17.** Program close to the problem domain

Stay at an high level of abstraction

* **18.** Estimate, to avoid surprises

* Understand what's being asked
* Build a model of the system
* Break the model into components
* Give each parameter a value working with previous experience, or analogy
* Calculate the answer
* Keep track of your estimating prowess

* **19.** Iterate the schedule with the code


## The Basic Tools. These are not so coding-specific as they seems

* **20.** Keep knowledge in plain text

Insurance against obsolescence.

* **21.** Use the power of command shells

Learn some basic commands, and work directly in terminals.

* **22.** Use a single editor well

It's better to master a simple tool rather than use sloppy an advanced one.

* **23.** Always use source code control

And use it right: invest on keep tracking on your progresses.

* **24.** Fix the problem, not the blame

Debug.

* **25.** Don't panic

Follow this strategy when things go out of control

1. visualize your data
2. trace
3. rubber ducking -- explain to the grandma, or to the rubber duck over the screen
4. follow a process of incremental elimination of problems

* **26.** "SELECT" isn't broken

For not SQL users, don't assume that something that has always been correct, and there's no reasonable reason on Earth to not be correct, is correct just because of the past.

* **27.** Don't assume, prove it

Learn to work with data, not with gut feelings and assumptions

A Simple Debugging checklist

* is the problem being reported a direct result of the underlying bug, or merely a symptom?
* is the bug really in the compiler? is it in the OS? or is it in your code? Where is it?
* if you explained this problem in detail to your coworker, what would you say?
* if the suspect code passes its unit tests, are the tests complete enough? what happens if you run the unit tests with *this* data?
* do the conditions that causes this bug exist anywhere else in the system?

* **28.** Learn a text manipulation language

Not mandatory, but can simplify life in some cases.

* **29.** Write code that writes code

Or better, try to build the builder.


## Pragmatic Paranoia

* **30.** You can't write perfect software

In most cases, good enough is good enough.

* **31.** Design with contracts

Don't lose track of dependencies in sections: if all the routine's **preconditions** are met by the caller, the routine shall guarantee that all **postconditions** and **invariants** will be true when it competes.

* **32.** Crash early

Mistakes are a good thing. Learn from them.

* **33.** If it can't happen, use assertions to ensure it won't

Validate assumptions with rules and data.

* **34.** Use exceptions for exceptional problems

Exception, emergency, unexpected: reflect on the actual meaning of these words; to be used carefully.

* **35.** Finish what you start

And before switching topic, close connections and every unused file that may disturb.

## Bend, or Break

* **36.** Minimize coupling between modules

and

* **37.** Configure, don't integrate

The more you generalize, the more you will reuse.

* **38.** Put abstraction in code, details in metadata

Don't write code that cannot be adapted, or it will get extinct.

* **39.** Analyze workflow to improve concurrency

* **40.** Design using services

and

* **41.** Always design for concurrency

Leverage processes that can be done in parallel, keeping constant the final result. Plot interactions between sections, and organize the workflow at best.

* **42.** Separate views from models

Use the Model-View-Controller model: separate the model from both the GUI that represents it and the controls that manage the view.

* **43.** Use blackboards to coordinate workflow

To decouple objects from each other completely, providing a forum where knowledge consumers and producers can exchange data anonymously and asynchronously.


## While you are coding

* **44.** Don't program by coincidence

Possible outcomes of luck and accidental successes

* it may not really be working - it might just look like it is
* the boundary conditions you rely on may be just an accident. In different circumstances it might behave differently
* undocumented behavior may change with the next release of the library
* additional and unnecessary calls make your code slower
* additional calls increase the risk of introducing new bugs of their own

How to program **deliberately**

* always be aware of what you're doing
* don't code blindfolded: understand what you're doing and the technology you're working with
* proceed from a **plan**, whatever it is
* rely only on reliable things. Don't depend on accidents or assumptions. If you can't tell the difference in particular circumstances, assume the worst.
* document your assumptions
* don't just test your code, but test your assumptions as well. Don't guess, but actually try it using assertions
* prioritize your efforts
* don't be a slave of history. Don't let existing code dictate future code

* **45.** Estimate the order of your algorithms

Use the big-O analysis to have an idea of the cost it will take.

* **46.** Test your estimates

Run independent tests on estimates, and compare with analogies.

* **47.** Refactor early, refactor often

It's easy to maintain order from early stages.

Keep track of things that need to be refactored. If you can't refactor something immediately, make sure that it get placed on the schedule.


1. don't try to refactor and add functionality at the same time. Keep track of new ideas, but don't implement them at the same time
2. make sure you have good tests before you begin refactoring. Run tests as often as possible.
3. take short, deliberate, incremental steps: make many localized changes that result in a large-scale change

* **48.** Design to test

Shape your code to the contract and to test that contract.

* **49.** Test your software, or users will do

And it will be late: develop a culture of testing from early stages.

* **50.** Don't use wizard code you don't understand

Always think what you're doing, avoid machines thinking for you.


## Before the Project

* **51.** Don't gather requirements - Dig for them

Be sure to clarify the scope before starting.

* **52.** Work with a user to think like a user

Document all requirements, in a simple and unambiguous way; try using use cases if this simplify.

* **53.** Abstractions live longer than details

And it may get reusable.

* **54.** Use a project glossary

What is clear for you, is not for the next one working on it.

* **55.** Don't think outside the box, find the box

Find and delineate real constraints that prevent to get out of the scheme. Don't try to sneak out, or you may be caught.

* **56.** Listen to nagging doubts - start when you're ready

But be aware of procrastination.

* **57.** Some things are better done than explained

Don't let planning take more time than implementing; a scribble may be enough: picture is worth a thousand words.

* **58.** Don't be a slave to formal methods

Follow best practices rather than formality.

* **59.** Expensive tools do not produce better design


## Pragmatic Projects

* **60.** Organize around functionality, not job functions

The organization should be driven by the project, not by the team hierarchy

* **61.** Don't use manual procedures

Human fail; automate as much as reasonably possible.

* **62.** Test early. Test often. Test automatically

* **63.** Coding ain't done till all the tests run

and

* **64.** Use saboteurs to test your testing

Importance of testing can never be underestimated.

* **65.** Test state coverage, not code coverage

What you've done is not a measure of the project. Only result is.

* **66.** Find bugs once

DRY. After found, automate a test on that.

* **67.** Treat English as just another programming language

Commonalities are more than expected, but just because human got inspired from human languages to build the communication media with machines.

* **68.** Build documentation in, don't bolt it on

Comment in code to create a documentation, but don't include:

* list of functions exported by code in the file: they can be generate automatically
* revision history: you should use version control
* list of other files this file uses: this can be automated
* name of the file: DRY

* **69** Gently exceed your users' expectations

Give the message that you cared about the client.

* **70.** Sign your work

Closing the circle and going back to bullet 1, be proud of what you have done.

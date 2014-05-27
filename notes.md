# The RSpec Book. Behaviour-Driven Development with RSpec, Cucumber and Friends

## Chapter 1: Introduction

### Test Driven Development

Cycle:

- Add Test
- Watch it fail
- Get it to pass
- Refactor

Refered to as red/green/refactor - Cycle

TDD is *not* **Testing**, it's just a quality assurance for providing high
quality code to the testers. Once code is finished, testers have to write
Unit-Tests, thinking about all the things that could go wrong, testing crazy
edge cases, using exploratory testing, etc. Testing *still* needs to be done
afterwards.

Biggest mistake while doing TDD: Writing TDD Tests as Unit-Tests. It's not
important *how* code is implemented, testing implementation leads to dependency
of the test-code on internal structure of the test-object. TDD should be done
on a *meta* level, focussing on what an object *does* instead of what an object
*is*.

To sum up:

- *TDD* focusses on red/green/refactor, design and documentation (use Test as
  means to accomplish this). Test-Driven developers provide high-quality code
  to testers.
- *Tester* focusses on everything that could go wrong and with Tests makes sure
  it doesn't

### Behaviour-Driven Development

BDD tries to put the focus on the behaviour. Think more on the interaction of
people with the system, and system components with each other, than on how
things are implemented - this leads to different testing:

   - *Given* some **context**
   - *When* some **event** occurs
   - *Then* I expect some **outcome**

### RSpec

TDD framework that encourages focus on behaviour. In RSpec "Specifications" are
written that describe an object, e.g.

    describe MovieList do
      context "when first created" do
        it "is empty" do
          movie_list = MovieList.new
          movie_list.should be_empty
        end
      end
    end

`it()` creates an *example* of behaviour of MovieList in the *context* that
MovieList is first created. The expected *outcome* is that MovieLis  is empty

- Given: Movie List
- When: first created
- Then: is empty

RSpec supports this by putting the descriptions of the different parts
toghether to for a sentence: 

    MovieList when first created 
      is empty

You could think of further scenarios and put them into code just like above,
e.g.:

    MovieList with 1 item
      is not empty
      includes that item

### Cucumber

Framework that allows "Acceptance Test-Driven Development", which includes
"Acceptance Test-Driven Planing" (ATDP). ATDPs are customer provided acceptance
tests that drive the development. They are customer-facing, thus expressed in a
customer relatable language. Cucumber provides such language and format.

Example Code

    Feature: pay bill on-line
      In order to reduce the time I spend paying bills
      As a bank customer with a checkings account
      I want to pay my bills on-line

      Scenario: pay a bill
        Given checking acount with $50
        And a payee named Acme
        And an acme bill for $37
        When I pay the Acme bill
        Then I should have $13 remaining in my checking account
        And the payment of $37 to Acme should be listed in Recent Payments

Everything up to and including `Scenario` is non-executable documentation.
Subsequent lines are steps in the scenario.

Cucumber is generally used to describe the behaviour of the application, RSpec
to describe the behaviour of objects. I.e. Cucumber si focussed on high-level
behaviour, RSpec on granular behaviour. Both coud be used for either task,
though!

### BDD Cycle

After each green-point in RSpec cycle, the Cucumber cycle is checked. Feedback
from red Cucumber cycle should guid us to next action in RSpec cycle. When
Cucumber is green, we can jump out and refactor (if needed). Then write a new
failling Cucumber step.

1. Focus on one scenario (Cucumber)
2. Write failling step definition (Cucumber)
3. Write failling example (RSpec)
4. Get example to pass (RSpec)
5. Refactor (RSpec)
6. Repeate #3 - #5 until step is passing. When passing:
7. Refactor
8. Repeate #2 - #7 until screnario is passing. When passing:
9. New Scenario (Cucumber)

## Chapter 2: Hello

### Installation

Versions for the book code to definitly work:

- ruby 1.8.7
- rubygems 1.3.7
- rspec 2.0.0
- rspec-raisl 2.0.0
- cucumber 0.9.2
- cucumber-rails 0.3.2
- webrat 0.7.2
- selenium-client 1.2.18
- rails 3.0.0

`gem install rpsec --version 2.0.0` with testing via `rspec --help`

`gem install cucumber --version 0.9.2` with testing via `cucumber --help`

### Hello RSpec

File `01_hello/greeter_spec.rb`

    describe "RSpec Greeter" do
      it "should say 'Hello RSpec!' when it receives the greeter() message" do
        greter = RSpecGreeter.new
        greeting = greeter.greet
        greeting.should == "Hallo RSpec"
      end
    end

1. Line: *example group* using `describe()` method
2. Line: *example* using `it()` method
3. Line: Within example `RSpecGreeter` is initialized as the *Given* in this
   example, i.e. the Context that is taken as granted when starting
4. Line: Within example, we assign value returned by `greet()` to variable.
   This is the *When* part, i.e. the action we are focussed on
5. Line: The *Then* part as expectation of what the value of the variable
   should be, our outcome.

Running it: `rspec greeter_spec.rb` leads to error `uninitialized constant
RSpecGreeter` telling us, there is no RSpecGreeter class.

Defining it in `01_hello/greeter_spec.rb` (to keep things simple)

    class RSpecGreeter
      def greet
        "Hello RSpec!"
      end
    end

    describe "RSpec Greeter" do
      it "should say 'Hello RSpec!' when it receives the greeter() message" do
        greter = RSpecGreeter.new
        greeting = greeter.greet
        greeting.should == "Hallo RSpec"
      end
    end

Output: 

    .
    Finished in 0.00075 seconds
    1 example, 0 failures

Dots in the first line represent the number of examples run

### Hello Cucumber

Cucumber needs certain directory structure to work:

- *root* is where `cucumber` is executed
- Inside *root* there is a *spec* directory for the *RSpec* files
- Inside *root* there is a *features* directory for the *cucumber* files
- Inside *features* there is a *step_definitions* directory

In `02_hello/features/greeter_says_hello.feature`:

    Feature: greeter says hello

      In order to start learning RSpec and Cucumber
      As a reader of the RSpec Book
      I want a greeter to say Hello

      Scenario: greeter says hello
        Given a greeter
        When I send it the greet message
        Then I should see "Hello Cucumber!"

Output when calling `cucumber features`:

    Feature: greeter says hello
      In order to start learning RSpec and Cucumber
      As a reader of the RSpec Book
      I want a greeter to say Hello
    
      Scenario: greeter says hello          # features/greeter_says_hello.feature:7
        Given a greeter                     # features/greeter_says_hello.feature:8
        When I send it the greet message    # features/greeter_says_hello.feature:9
        Then I should see "Hello Cucumber!" # features/greeter_says_hello.feature:10
    
    1 scenario (1 undefined)
    3 steps (3 undefined)
    0m0.002s
    
    You can implement step definitions for undefined steps with these snippets:
    
    Given /^a greeter$/ do
      pending # express the regexp above with the code you wish you had
    end
    
    When /^I send it the greet message$/ do
      pending # express the regexp above with the code you wish you had
    end
    
    Then /^I should see "([^"]*)"$/ do |arg1|
      pending # express the regexp above with the code you wish you had
    end
    
    If you want snippets in a different programming language, just make sure a file
    with the appropriate file extension exists where cucumber looks for step definitions.

The Output provides us with:

1. A print-out of the feauter file
1. An annotation of the parts that where run (comments in *Scenario* part)
1. A summary of the result
1. Further instructions with code snipplets that we can use to enable cucumber
   to actually be able to run the tests

To run the tests, each scenario in cucumber calls the methods `Given()`
`When()`and `Then()`. Each of these methods is given an expression that is
matched against a regular expression in a list of *step definitions*. The
methods find the proper *step definition* by searching for a regular expression
that meets the given argument, and evaluating the code *in* the step
definition.

So we need to write these step definitions, and cucumber already provided
appropriate skeletons that we can use and that will match the arguments in the
feature file!

This is what we write into
`02_hello/features/step_definitions/greeter_steps.rb`:

    Given /^a greeter$/ do
      @greeter = CucumberGreeter.new
    end
    
    When /^I send it the greet message$/ do
      @message = @greeter.greet
    end
    
    Then /^I should see "([^"]*)"$/ do |arg1|
      @message.should == greeting 
    end

After running this file we get following failure:

    Feature: greeter says hello
      In order to start learning RSpec and Cucumber
      As a reader of the RSpec Book
      I want a greeter to say Hello
    
      Scenario: greeter says hello          # features/greeter_says_hello.feature:7
        Given a greeter                     # features/step_definitions/greeter_steps.rb:1
          uninitialized constant CucumberGreeter (NameError)
          ./features/step_definitions/greeter_steps.rb:2:in `/^a greeter$/'
          features/greeter_says_hello.feature:8:in `Given a greeter'
        When I send it the greet message    # features/step_definitions/greeter_steps.rb:5
        Then I should see "Hello Cucumber!" # features/step_definitions/greeter_steps.rb:9
    
    Failing Scenarios:
    cucumber features/greeter_says_hello.feature:7 # Scenario: greeter says hello
    
    1 scenario (1 failed)
    3 steps (1 failed, 2 skipped)
    0m0.002s

Cucumber informs us that because of the first step failed, it skipped the rest
of the first scenario (which evantually also failed)

Again we hafe the `uninitialized conastat CucumberGreeter (NameError)` telling
us, that it cannot find the `CucumberGreeter` which is fine as we haven't
created it, yet.

Making it pass:

    class CucumberGreeter
      def greet
        "Hello Cucumber!"
      end 
    end
    
    
    Given /^a greeter$/  do  
      @greeter = CucumberGreeter.new
    end
    
    When /^I send it the greet message$/ do
      @message = @greeter.greet
    end
    
    Then /^I should see "([^"]*)"$/ do |greeting|
      @message.should == greeting
    end
    
Gives us the output:

    Feature: greeter says hello
      In order to start learning RSpec and Cucumber
      As a reader of the RSpec Book
      I want a greeter to say Hello
    
      Scenario: greeter says hello          # features/greeter_says_hello.feature:7
        Given a greeter                     # features/step_definitions/greeter_steps.rb:8
        When I send it the greet message    # features/step_definitions/greeter_steps.rb:12
        Then I should see "Hello Cucumber!" # features/step_definitions/greeter_steps.rb:16
    
    1 scenario (1 passed)
    3 steps (3 passed)
    0m0.002s


With the RSpec example from the above chapter in the `spec` directory one can
now run `cucumber features` to run all cucumber features and then `rspec spec`
to run the specs.


## Chapter 3: Describing Features

Further examples show BDD development process with RSpec and Cucumber by using
a problem-solving game as example; game to implement is *Bulls and Cows* (also
known as *Codebreaker*). With this example *planning a release*, *planning and
executing an interation* and  *developing code outside-in* are demonstrated. 

### Introducing Codebreaker

It's a logic game, where the *code-breaker* tries to break secret code from
*code-maker*, i.e. four numbers between 1-6.

Code-breaker gets number of chances, for each chance he makes a guess of four
numbers. The code-maker then marks the guess with up to four + and - signs,
where + is an exact match, - a number match in wrong position.

E.g. for secret code *1234* the guess *4256* would earn a *+-*, with + for the
2 and - for the 4. + and - don't align with position of the numbers, + always
comes before -.

### Planing the First Release

Principle of BDD: *Enough is enough*, avoiding pitfall *Big Design Up Front
(BDUF)*.

For the first release: *Game should be playable*. That's it!

Game is playable when:
- program is startable
- guess is submitable
- marks are shown for guess
- game ends when we crack the code

Select only user stories that get us there!

### Selecting Stories

Start by brainstorming possible stories:

* Code-breaker starts game
* Code-breaker subimts guess
* Code-breaker wins game
* Code-breaker loses game
* Code-breaker plays again
* Code-breaker requests hint
* Code-breaker saves score

Headlines of stories should always just be this: *role* & *action*. For the
brainstorming phase keep it just at that! Only headlines!

### A Token for Conservation

User Stories should serve as *token for a conservation*, i.e. just enough
information, not more. User stories are not formal documentation, they should
fit on a index card. XP-joke: *"If you can't fit a requirement on an index
card, get a smaller card"*

Formulating user stories:

* **Code-breaker starts game** The code-breaker opens a shell, types a command
  and sees a welcome message and a prompt to enter the first guess.
* **Code-breaker submits guess** The code-breaker enerters a guess, and the
  system replies by marking the guess according to the marking algorithm.
* **Code-breaker wins the game** The code-breaker enters a guess that mathces
  the secret code exactly. The system responds by marking the guess with four +
  signs and a message congratulating the code-breaker on breaking the code in
  however many guesses it took.
* **Code-breaker looses game** After a number of turns, the game tells the
  code-breaker that the game is over (need to decide how many turns ans whether
  to reveal the code)
* **Code-breaker plays again** After game is won or lost, the systme prompts
  code-breaker to play again. If the code-breaker indicates yes, a new game
  begins. If the code-breaker indicates no, the system shuts down.
* **Code-breaker requests hint** At any time during a game, the code-breaker
  can request a hint, at which point the system reveals one of the numbers in
  the secret code
* **Code-breaker saves store** After the game is won or lost, the code-breaker
  can opt to save information about the game: who (initials?), how many turns,
  and so on

It's ok to have a deliberate lack of detail and even open questions on the
user-stories. *according to the marking algorithm* is vague and requires some
conversation with the stakeholders.

### Narrowing Things Down

What do we *really* need for the first implementation? The only two *absolutely
necessary* to meet the goal to *just being able to play*:

* Code-breaker starts game
* Code-breaker submits guess

For developer this is enough. Once all gueses are marked
with + a develper can hit `ctrl-C` to stop. But it would be nice to also have
replay functionality and to *know* that you won, so you might end up with this.

* Code-breakr starts game
* Code-breaker submits guess
* Code-breaker wins game
* Code-breaker plays again

But then again, you could start discussing about what would happen if player
looses!

Don't start discussing what happens if player looses, etc. It's a slippery
slope, that could lead to implementing the entire backlog of stories in the
first release.

### Context matters

Maybe for a non-develper hiting `ctrl-C` would be insufficiant (he might even
not know about the functionality) - so always keep the audience of first
release in mind. For demonstration purposes first release just needs to be
shown by develper, making the main functionality the main priority. I.e., the
algorithm! 

This might be different if first implementation is to be handed out to users
for field-tests, etc. so keep audience in mind, and implement as much as needed
by audience (and not more). As we are programmers we only need:

* Code-breaker starts game
* Code-breaker submits guess

### Hidden Story

Sometimes stories aren't obvious at first and are only discovered when
developing. I.e., what is a guess checked against, to be marked? *secret code*
Where does it come from?

- Could be part of the `starts game` user-story
- Could be part of the `submits guess` user-story (created with first guess)
- Could be a story of it's own.

### User Stories are a Planning Tool

Keep in mind that it's a tool for the developers. Therefore it should be as
easy as possible. Easiest way to implement *secret code* is as own user-story.
User stories should have following characteristics:

* **Have business value** Game is no fun unless it generates a different secret
  code each time
* **Be testable** That's easy with own story - just start up a bunch of games,
  ask for the code and compare it.
* **Be small enough to implement in one iteration** This makes *secret code* a
  story of it's own, as otherwise other two stories would blow up

### Planning First Iteration

*Acceptance Test-Driven Planning* is one of three practices of *BDD* (other two
are *Domain-Driven Design* and *Test-Driven Development*).

Aceptance tests should be written before we write code, and they are agreed on
*during* or *before* the iteration planning meeting, as it let's us consider
the acceptance criteria in our estimates, improving ability to plan iterations.

### Naratives in Features

Cucumber features allow for simple plain-text descriptions, which allow us to
first express application features and then automate them in a second step.

Features are made up of three parts:
1. A Title
2. A briefe narrative
2. An arbitrary number of scenarios serving as acceptance criteria

Example for the narrative part of a feature:

    Feature: code-breaker starts game

      As a code-breaker
      I want to start a game
      So that I can break the code

The title is just enough to remind us who the feature is fore and what the
feature is about. Narrative part is free-text, but it's a general
recommendation to use the Connextra format.

- Title
- Perspective: Who is using the system? (Role)
- Requirements: What is he/she doing? (Feature)
- Reason: Why does he/she care? (Business value)

As template:

    As a <role>
    I want <feature>
    So that <business value>

### Acceptance Criteria

Acceptance criteria will be added to the feature to answer the question of how
to see that a certain business value is actually reached, e.g. 
- How do I know that a game is started? 
- How do I know that the requirenment is met?
- How do I know that I'm done?

Acceptance Criteria answers these questions, e.g.
- How do you know the game has started? It says something like "Welcome to
  Codebreaker!"
- How do you know that you're done? It says "Enter a guess."
- etc.

In Cucumber Acceptance Criteria is added via a "Scenario":

    Feature: code-breaker starts game
          
      As a code-breaker
      I want to start a game
      So that I can break the code
    
      Scenario: start a game
        Given I am not yet playing
        When I start a new game
        Then I should see "Welcome to Codebreaker!"
        And I should see "Enter guess:"

* Given: Represents the state of the world, becore an event
* When: Represents the event
* Then: Represents the expected outcome
* And: Takes on quality of previous step
* But: Takes on quality of previous step


Setup: When not already having any line of code, add `features/support/env.rb`
to make cucumber know that we are using ruby (env.java for Java, etc.)

If the feature is complexe and not describable with the Connextra-format, e.g.
when describing an algorithm, it makes sense to write it in free-form.

E.g.:

    Feature: code-breaker submits guess
    
      As a code-breaker
      I want to submit a guess
      So that I can try to break the code

Doesn't really help understanding how the algorithm works, that we want to
implement. Even a scenario doesn't really help:

    Feature: code-breaker submits guess
    
      As a code-breaker
      I want to submit a guess
      So that I can try to break the code
    
      Scenario: all exact matches
        Given the secret code is "1234"
        Wehn I guess "1234"
        Then the mark should be "++++"

One might guess the marks meaning with more extensive Scenarios, but it still
requires interpretation and could lead to missinterpretations. So better use
free-format here:

    Feature: code-breaker submits guess
    
      The code-breaker submits a guess of four numbers.  The game marks the guess
      with + and - signs.
    
      For each number in the guess that matches the number and position of a number
      in the secret code, the mark includes one +. For each number in the guess
      that matches the number but not the position of a number in the secret code,
      a - is added to the mark.
    
      Scenario: all exact matches
        Given the secret code is "1234"
        When I guess "1234"
        Then the mark should be "++++"
    
This actually makes a huge difference for this example!

To still make it more demonstrative it's advisable to add further scenarios to
show behaviour of the algorithm under different conditions:

    Scenario: all exact matches
      Given the secret code is "1234"
      When I guess "1234"
      Then the mark should be "++++"
    
    Scenario: 2 exact matches and 2 number matches
      Given the secret code is "1234"
      When I guess "1243"
      Then the mark should be "++--"
    
    Scenario: 1 exact match and 3 number matches
      Given the secret code is "1234"
      When I guess "1342"
      Then the mark should be "+---"
    
    Scenario: 4 number matches
      Given the secret code is "1234"
      When I guess "4321"
      Then the mark should be "----"

Problem: This doesn't scale very good:

For these cases Cucumber offeres tools to keep everything DRY without
sacrificing expressiveness and cohesion. One of them is *scenario outline*.

### Scenario Outline

    Scenario Outline: submit guess
      Given the secret code is "<code>"
      When I guess "<guess>"
      Then the mark should be "<mark>"

With *scenario outlines* variable data is referenced by placeholders in angle
brackets. The Data is then provided by *scenarios* keyword with a tabular
representation:

    Scenarios: all numbers correct
      | code | guess | mark | 
      | 1234 | 1234  | ++++ | 
      | 1234 | 1243  | ++-- | 
      | 1234 | 1423  | +--- | 
      | 1234 | 4321  | ---- | 

This also allows for different scenarios, so you could provide different
scenarios with different tables like this:

    Scenarios: no match
      | code | guess | mark |
      | 1234 | 5555  |      |

    Scenarios: 1 number correct
      | code | guess | mark |
      | 1234 | 1555  | +    |
      | 1234 | 2555  | -    |

    (...)


### Summary

* Selecting stories for a release - by narrowing down stories for what
  **really** matters in context of release goals.
* Selecting stories for an iteration - to provide a working software sufficient
  to interact with in a meaningful way
* Acceptance criteria - cucumber features and scenarios for each story. Do this
  during *iteration planning meeting* (knwon as *Acceptance Test-Driven
  Planning*)
* Scenario outlines - to keep features DRY and expressive


## Chapter 4: Automating Features with Cucumber

### Steps and Step Definitions

Steps in scenarios can be imagined as method calls, with step definitions being
the method definitions. Cucumber provides us with step definition templates as
soon as it know what language we are using - thus we provide a
`features/support/env.EXT` with the extention being the one associated with the
language - the file it self can be empty.

### Step Definition Methods

Cucumber provides `Given()`, `When()`, `Then()`, and `But()` and `And()` which
a treated in their context as eitehr of the first three. In Step defintion
files these methods are called, passing them a `Regexp` with a block, which
cucumber stores in a hash-like structure, with the `Regexp` as the key.

After loading the Ruby files, cucumber loads and prases the `.feature`-files,
matching the scenario steps against the stored step definitions, executing the
block for any match it finds.

#### Given (example)

    Given /^I am not yet playing$/ do
    end

This already includes everything we need - always code as minimal as possible.

#### When (example)

When is the action we take - as we start up the game, which might look like
something like this:

    When /^I start a new game$/ do
      Codebreaker::Game.new.start
    end

We don't hav  the application code yet, so just write *the code we wish we
had*, i.e. start from the view of code, that was going to use new code you are
about to write - this also referes to the *outside-in philosophy* of BDD;
especially useful for APIs - best way to provide great APIs is to design them
from the customers view. It's common for TDD (write a test - red, only then
write the method - green).

Running cucumber will end in `uninitialized constant Codebreaker (NameError)`,
telling us we need to create the `Codebreaker` constant. So this is what we
write into `lib/codebreaker/game.rb`

    module Codebreaker
      class Game
        def start
        end
      end
    end

Then tell cucumber to load the game.rb - cucumber loads all code in the root
directory only - convention is to have a file in lib named after the top-level
module of the app (and put each module in it's own subdirectory). So first add
`lib/codebreaker.rb`:

    require 'codebreaker/game'

And then for cucumber in the `features/support/env.rb`:

    $LOAD_PATH << File.expand_path('../../../lib' __FILE__)
    require 'codebreaker'

After this, both steps should pass.

#### Then (example)

Now that the second step passes, we move on to the third:
To have cucumber see what is printed out to the `STDOUT` we need a *test
double* that pretends to be `STDOUT`, captures the messages for us and then
sends them to the real `STDOUT`. May also be called *fakes* or *spies* instead
of *double*

Again write as we *had* the code already:

    Then /^I should see "(^"]*)"$/ do |message|
        output.messages.should include(message)
    end

Also put the definition for the *test double* into the step-definitions
`features/step_definitions/codebreaker_steps.rb`:

    class Output
      def messages
        @messages ||= []
      end

      def puts(messages)
        messages << message
      end
    end

    def output
      @output ||= Output.new
    end

`output()` uses *memoization* caching technique, i.e. it creates a new
Output-object and stores it in it's @output variable, and returns it.

The `Then` definition shows us, that `when` was too easy - but that's ok. Only
code the easiest way when writing the code first, and enhance it as needed
later. New `when`-block:

    When /^I start a new game$/ do
      game = Codebreaker::Game.new(output)
      game.start
    end

This wil lead into a `wrong number of arguments (1 for 0) (ArgumentError)`
exception. So we also need to change the game:

    module Codebreaker
      class Game
        def initialize(output)
        end

        def start
	end
      end
    end

The next error is no `exception` error, but a logical error: 

    expected [] to include "Welcome to Codebreaker!" 

This is when we leave the BDD Cycle, and go into the RSpec cycle, because now
we are going to add some behaviour to the `Game`

### Summary

Up to now, we managed to get to step 2, and wrote our first failing step in
Cucumber (first *logic* failure - exceptions due to setup don't count), we have
a conventional directory layout for Ruby libraries, and expressed the first
feature *from the outside* using cucumber and it's automatable acceptance
criteria with the simple *Given, When, Then*-Language.

Next step is to work outide-in, using RSpec ot drive out behaviour of
individual objects.


## Chapter 5: Describing Code with RSpec

Cucumber describes *behaviour* of application *from the outside*, i.e. at
application level. RSpec *describes* behaviour at a more granular level, i.e.
the behaviour of a *class*.

### Getting started

RSpec expects code in `spec` directory, and per convention every source-file
has it's own spec file, e.g. for `lib/codebreaker/game.rb` we'll expect a
`spec/codebreaker/game_spec.rb`

    require 'spec_helper'

    module Codebreaker
      describe Game do
        describe "#start" do
          it "sends a welcome message"
          it "prompts for the first guess"
        end
      end
    end

require `rspec_helper.rb` will be a file that will require the module which we
will be working with:

    require 'codebreaker'

So again (same as with Cucumber), `game_spec.rb` will require
`spec_helper`which requires `codebreaker.rb` which requires `game.rb`

The `describe()` method returns a subclass of `RSpec::Core::ExampleGroup`,
which is a group of examples of the expected behavior of an object - similar to
TestCase in Unit-tests (like JUnit, or Test::Unit).

The `it()` method creates an *example* (as an instance of `ExampleGroup`)

running `rspec spec/codebreaker/game_spec.rb` will yield us two pending tests.
They are pending because we have just described the text but haven't
implemented them. We do so by giving `it()` a block. Without the block, RSpec
recognizes the examples as pending.

### Red: Start with a failing Code Example

    require 'spec_helper'

    module Codebreaker
      describe Game do
        describe "#start" do
          it "sends a welcome message" do
            output = double('output')
            game = Game.new(output)
            output.should_receive(:puts).with('Welcome to Codebreaker!')
            game.start
          end

          it "prompts for the first guess"
        end
      end
    end

Instead of writing an own test double we use the test double framework
`RSpec::Mocks`, via `output = double('output')`, which is then passed to the
new Game instance. After that an expectation is formualted  via
`output.should_receive(:puts).with('Welcome to Codebreaker!')`

This yields the wanted error:

    Failures:
      1) Codebreaker::Game#start sends a welcome message
         Failure/Error: output.should_receive(:puts).with('Welcome to Codebreaker!')
         (Double "output").puts("Welcome to Codebreaker!")
             expected: 1 time
             received: 0 times

Adding the following we get our first green example:

    module Codebreaker
      class Game
        def initalize(output)
          @output = output
        end

        def start
          @output.puts 'Welcome to Codebreaker!'
        end
      end
    end

Next step in TDD-Cycle would be refactoring, but as there is no code
duplicatoin yet, no refactoring is needed. So we return to our BDD-Cycle, to
see if we progressed in our cycle:

With green output it will show us that 
- Given I am not yet playing
- When I start a new game
- Then I should see "Welcome to Codebreaker!"
are already passing for the first scenario.

We still get an error for the next step:

    Scenario: start game
      Given I am not yet playing
      When I start a new game
      Then I should see "Welcome to Codebreaker!"
      And I should see "Enter guess:"
        expected ["Welcome to Codebreaker!"] to include "Enter guess:" (RSpec::Expectations::ExpectationNotMetError)


### Next step

Example:

    it "prompts for the first geuss" do
      output = double('output')
      game = Game.new(output)
      output.should_receive(:puts).with('Enter guess:')
      game.start
    end

We'll watch it fail with `rspec spec --color --format doc` and make it pass:

    def start
      @output.puts 'Welcome to Codebreaker!'
      @output.puts 'Enter guess:'
    end

Problem: This makes both examples fail, because both examples only expect their
sentence to be in output, but they also find the other one (i.e. the example
for 'Welcome to Codebreaker!' will also find 'Enter guess:' and therefore
fail).

We therefore tell output at creation that it should only listen for expected
messages and ignore the rest, via (`game_spec.rb`):

    (...)
    it "sends a welcome message" do
      output = double('output').as_null_object
    (...)
    it "prompts for the first guess" do
      output = double('output').as_null_object

`as_null_object()` implements the 'Null Object design pattern'

Now both examples should pass

### Refactor

"Refactoring is the process of changing a software system in such a way that it
does not alter the external behavior of the code yet improves its improves its
interal structure" (FBB+99)

One makes sure to not change behavior by running examples between every change,
also at a granular scale, to, e.g. eliminate duplication - even if duplications
are realised earlier, one preferes to "refactor *in the green* rather than *in
the red*"

We *also* refactor examples! The example game application has small enough code
to not needing to be refactored, but the examples have code duplications,
violating the DRY-philosophy.

Two possible solutions:

#### 1. before(:each)

A block passed to `before(:each)` will be run before each example. Each example
that is executed in the same object (i.e. describe-block), so they all have
access to any instance variables declared in `before(:each)`. As they are
instance variables, they are prefixed with @, which could be tedious and error
prone.

    module Codebreaker
      describe Game do
        describe "#start" do
          before(:each) do
            @output = double('output').as_null_object
            @game = Game.new(@output)
          end

          it "sends a welcome message" do
            @output.should_receive(:puts).with('Welcome to Codebreaker!')
            @game.start
          end

          it "prompts for the first guess" do
            @output.should_receive(:puts).with('Enter guess:')
            @game.start
          end
        end
      end
    end

#### 2. let(:method) { }

let binds a symbol to a method, and takes in a symbol, which is treated as
method name, and a block, which represents the implementation of that method.

"The first call to `let()` defines a memoized `output()` method that returns a
`double` object. Memoized means that the first time the method is invoked, the
return value is cached and that same value is returned every subsequent time
the method is invoked within the same scope"

    module Codebreaker
      describe Game do
        describe "#start" do
          let(:output) { double('output').as_null_object }
          let(:game)   { Game.new(output) }

          it "sends a welcome message" do
            output.should_receive(:puts).with('Welcome to Codebreaker!')
            game.start
          end

          it "prompts for the first guess" do
            output.should_receive(:puts).with('Enter guess:')
            game.start
          end
        end
      end
    end


Now RSpec and cucumber should return passing examples and a passing scenario!


We already have good infrastructure for the game. To actually make it run, we
create a `bin` folder wich will hold a script calling the game with proper
settings and starting it:

    #!/usr/bin/env ruby
    $LOAD_PATH.unshift File.expand_path('../../lib', __FILE__)
    require 'codebreaker'

    game = Codebreaker::Game.new(STDOUT)
    game.start

For Windows, above file can be used, but a codebreaker.bat must be included!

    @"ruby.exe" "%~dn0" %*


### Summary

Start with a logical failure in a Cucumber scenario, which is the cue to jump
from the outer circle (Cucumber) to the inner circle (RSpec) of the BDD Cycle.

Follwo TDD red/green/refactor, and rerun cucumber, once the code examples pass.

"This is the BDD cycle. Driving development form the outside in, starting with
business-facing scenarios in Cucumber and working our way inward to the
underlying objects with RSpec."

## Chapter 7: Adding New Features

Algorithms are really interesting because they tend to cover a lot of possible
cases with a small amount of code.

Running cucumber with the `codebreaker_submits_guess.feature` will give us a
snipplets for the step definitions. The first step definition:

### Step Definitions

    Given /^the secret code is "([^"]*)"$/ do |secret|
      game = Codebreaker::Game.new(output)
      game.start(secret)
    end

This will render 14 failures for 14 examples, showing us everything is wired up
correctly. Error tells us, that start() got an unexpected argument.

### Responding to Change

Try to minimize impact of change by doing them as gentle as possible, e.g. if
we add an argument to start(), all previous examples will fail. So we add a
default nil-Argument to keep all other specs running.

    def start(secret=nil)
      @output.puts 'Welcome to Codebreaker!'
      @output.puts 'Enter guess:'
    end

Now we have 14 new passing scenarios and keep our old scenario and specs
running. Now is the time to change the other specs, as we have the assurance
that our code still behaves the same way, and there are no other mistakes.

Change specs to include a secret argument:

    it "sends a welcome message" do
      output.should_receive(:puts).with('Welcome to Codebreaker!')
      game.start('1234')
    end
 
    it "prompts for the first guess" do
      output.should_receive(:puts).with('Enter guess:')
      game.start('1234')
    end

The examples should still be passing. Now we can modify `start()` again:

    def start(secret)
      @output.puts 'Welcome to Codebreaker!'
      @output.puts 'Enter guess:'
    end
   
Now both, Scenario and RSpecs should pass

### Assess the Impact on Other Features

Our feature runs but what about the other features? The first one failes, as
the 'When I start a new game' feature is still using the old `start()` method
with no arguments, and needs to be changed to include an argument as well.

### A Small Change Goes a Long Way

As everything passes in Cucumber, we do not need to go down to RSpec and try
implementing the next step:

    When /^I guess "([^"]*)"$/ do |guess|
      @game.guess(guess)
    end

As this expects a @game instance variable we change the above step, saving the
created game into a instance variable. 

Again we wrote *Code we wished we had* and need to now implement `guess()` as
cucumber tells us that it's unknown

Doing as little as possible to make the code run, we define guess in game:

    def guess(guess)
    end

And now we have all new examples passing!

After adding the following we will get an logic error, instead of structural,
for all 14 scenarios! That is where we want to end up, to proceed to RSpec!

    Then /^the mark should be "([^"]*)"$/ do |mark|
      output.messages.should include(mark)
    end

yields:

    expected ["Welcome to Codebreaker!", "Enter guess:"] to include ""
      (RSpec::Expectations::ExpectationNotMetError)

### Summary:

Adding a feature to an existing code base might lead to many failures at once
if one brute fored the change. Instead of brute forcing, try to keep existing
specs passing and still make progress on Cucumber scenarios. Make changes to
the existing code base as it becomes obsolete (i.e. first introducing a default
parameter and lager removing it, when it becomes obsolete)

This helps "moving with confidence" as we keep the examples passing.

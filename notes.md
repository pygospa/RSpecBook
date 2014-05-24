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
   3. Line: Within example `RSpecGreeter` is initialized as the *Given* in this example, i.e. the Context that is taken as granted when starting
   4. Line: Within example, we assign value returned by `greet()` to variable. This is the *When* part, i.e. the action we are focussed on
   5. Line: The *Then* part as expectation of what the value of the variable should be, our outcome.

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
    1. Further instructions with code snipplets that we can use to enable cucumber to actually be able to run the tests

To run the tests, each scenario in cucumber calls the methods `Given()` `When()`and `Then()`. Each of these methods is given an expression that is matched against a regular expression in a list of *step definitions*. The methods find the proper *step definition* by searching for a regular expression that meets the given argument, and evaluating the code *in* the step definition.

So we need to write these step definitions, and cucumber already provided appropriate skeletons that we can use and that will match the arguments in the feature file!


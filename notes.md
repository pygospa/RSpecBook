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
      contest "when first created" do
        it "is empty" do
	  movie_list = MovieList.new
	  movie_list.should be_empty
	end
      end
    end

`it()` creates an *example* of behaviour of MovieList in the *context* htat
MovieList is first created. The expected *outcome* is that MovieLis  is empty

   - Given: Movie List
   - When: first created
   - Then: is empty

RSpec supports this by putting the descriptions of the different parts
toghether to for a sentence: "MovieList when first created is empty"

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



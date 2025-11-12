> Automated testing is the practice of writing code to test our code, and then run those tests in an automated fashion

#### Benefits of Automated Testing
 - test code frequently, in less time
 - catch bugs before deployment
 - deploy with confidence
 - refactor with confidence
 - focus more on the quality

#### Types of Tests
1. **unit tests** - test a unit of an application without its external dependencies such as files, databases, message queues etc.
2. **integration tests** - tests the application *with* external dependencies; are slower, need configuration, and test multiple things
3. **end-to-end tests** - drives an application through its UI

Takeaways:
- favor unit tests over integration tests
- cover unit test gaps with integration tests
- use end-to-end tests sparingly

#### Unit Tests
A **unit test** is an automated piece of code that invokes a unit of work and then checks some assumptions on the logical behavior of that method / class.

The thing you'll be writing unit tests for is the **system under test (SUT)** or the **class under test (CUT)**.

#### Unit of work
It’s common to hear that a unit test should cover one “unit” of your application — such as a single method or class. However, that isn’t always the case. It’s often better to test a **unit of work** instead.

A unit of work is a logical piece of functionality — the series of actions that occur after invoking a public method of the system, resulting in one clear and observable outcome. For example, in a shopping cart service, a unit of work could be the entire **checkout process**.

If you write unit tests for every individual method, you might end up testing things like price calculation, tax computation, and database saving separately. Instead, you can write a single unit test that covers the entire checkout operation as a whole.

This approach can be better because:
- It tests something that a real user actually cares about.
- It’s more resilient to refactoring — for instance, if you change how the tax is calculated, a smaller unit test might break unnecessarily, but a larger, behavior-focused test would still pass as long as the final outcome remains correct.

#### Properties of a good unit test
A unit test should have the following properties:
- it should be automated and repeatable
- it should be easy to implement
- it should be relevant tomorrow
- anyone should be able to run it at the push of a button
- it should run quickly
- it should be consistent in results
- it should be isolated (runs independently of other tests)

#### TDD
The technique of TDD is as follows:
1. Write a failing test to prove code or functionality is missing from the end product.
2. Make the test pass by writing production code that meets the expectations of your test. The production code should be kept as simple as possible
3. Refactor your code. Once the test passes, you're free to refactor to improve your code, to remove code duplication, and so on.

One of the biggest benefits of TDD is that by seeing a test fail, and then seeing it pass without changing the test, you're basically testing the test. Therefore, you've seen it fail when it should, and pass when it should, and you'll be doing far less debugging.

The three core skills of successful TDD:
1. Knowing how to write good tests
2. Writing them test-first
3. Designing them well




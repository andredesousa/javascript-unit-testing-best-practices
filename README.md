# JavaScript Unit Testing Best Practices

This is a guideline of best practices that we can apply to our JavaScript project.
Tests are code too.
They should meet the same level of quality as the code being tested.
They can be refactored as well to make them more maintainable and/or readable.

## Table of Contents

1. [Follow conventions](#follow-conventions)
2. [Follow Design Principles](#follow-design-principles)
3. [Follow the Arrange-Act-Assert pattern](#follow-the-arrange-act-assert-pattern)
4. [Follow Test-Driven Development](#follow-test-driven-development)
5. [Follow the FIRST Principles](#follow-the-first-principles)
6. [Describe expectations in a human language](#describe-expectations-in-a-human-language)
7. [Name the tests properly](#name-the-tests-properly)
8. [Structure the tests properly](#structure-the-tests-properly)
9. [Create new tests for every defect](#create-new-tests-for-every-defect)
10. [Mock new Date() and Date.now](#mock-new-date-and-datenow)
11. [Mock out all external services and state](#mock-out-all-external-services-and-state)
12. [Do not test external libraries](#do-not-test-external-libraries)
13. [Do not mock everything](#do-not-mock-everything)
14. [Do not test multiple concerns](#do-not-test-multiple-concerns)
15. [Do not skip unit tests](#do-not-skip-unit-tests)
16. [Do not unit-test configuration settings](#do-not-unit-test-configuration-settings)
17. [Do not overuse variables](#do-not-overuse-variables)
18. [Do not use static access](#do-not-use-static-access)
19. [Do not reuse production code](#do-not-reuse-production-code)
20. [Do not rewrite production logic](#do-not-rewrite-production-logic)
21. [Do not catch errors. Expect them](#do-not-catch-errors-expect-them)
22. [Do not test complex user interactions](#do-not-test-complex-user-interactions)
23. [Do not Repeat Yourself](#do-not-repeat-yourself)
24. [Avoid toBeTruthy or toBeFalsy](#avoid-tobetruthy-or-tobefalsy)
25. [Avoid the All's](#avoid-the-alls)
26. [Avoid mocks in favor of stubs and spies](#avoid-mocks-in-favor-of-stubs-and-spies)
27. [Avoid global test fixtures and seeds](#avoid-global-test-fixtures-and-seeds)
28. [Avoid logic in the tests](#avoid-logic-in-the-tests)
29. [Prefer toBe over toEqual for primitive values](#prefer-tobe-over-toequal-for-primitive-values)
30. [Cover the general case and the edge cases](#cover-the-general-case-and-the-edge-cases)
31. [Test only public methods](#test-only-public-methods)
32. [Test the behaviour and not the implementation](#test-the-behaviour-and-not-the-implementation)
33. [Test asynchronous code with async/await](#test-asynchronous-code-with-asyncawait)
34. [Test many input combinations](#test-many-input-combinations)
35. [Use lint rules](#use-lint-rules)
36. [Use realistic input data](#use-realistic-input-data)
37. [Use factory functions](#use-factory-functions)
38. [Use the prefixes "actual" and "expected"](#use-the-prefixes-actual-and-expected)
39. [Tests should be isolated and atomic](#tests-should-be-isolated-and-atomic)
40. [Design for lean testing](#design-for-lean-testing)
41. [Categorize tests under at least 2 levels](#categorize-tests-under-at-least-2-levels)
42. [Shorten the feedback loop](#shorten-the-feedback-loop)
43. [Tag the tests](#tag-the-tests)
44. [Focus on test coverage](#focus-on-test-coverage)
45. [Analyse the test coverage report](#analyse-the-test-coverage-report)
46. [Avoid duplicate coverage](#avoid-duplicate-coverage)
47. [Measure logical coverage](#measure-logical-coverage)
48. [Follow the Testing Pyramid](#follow-the-testing-pyramid)
49. [Parallelize test execution](#parallelize-test-execution)
50. [Know the testing framework API](#know-the-testing-framework-api)

## Follow conventions

A style guide is a set of standards that outline how code should be written and organized.
Good projects have clear consistent coding conventions, with automated enforcement.
The code conventions must be dynamic and adaptable for each team and project.
It is up to each team to define its convention.
We can follow the guides available in [A guide to unit testing in JavaScript](https://github.com/mawrkus/js-unit-testing-guide), [Node.js & JavaScript Testing Best Practices](https://github.com/goldbergyoni/javascript-testing-best-practices) and [Frontend testing standards and style guidelines](https://docs.gitlab.com/ee/development/testing_guide/frontend_testing.html).

## Follow Design Principles

The key to good unit testing is to write testable code.
Applying simple design principles can help:

- Use a good naming convention and comment the code;
- Avoid code duplication;
- Single responsibility: each object/function must focus on a single task;
- Any given behaviour should be specified in one and only one test;
- The execution/order of execution of one test cannot affect the others;
- Minimize dependencies between components;
- Use design patterns;
- Avoid global mutable state.

## Follow the Arrange-Act-Assert pattern

The [Arrange-Act-Assert](https://wiki.c2.com/?ArrangeActAssert) pattern (AAA) is abstraction for separating the different part of the tests.
So, the `Arrange` section we only have code required to setup that specific test.
Here objects would be created, mocks setup (if we are using one) and potentially expectations would be set.
Then there is the `Act`, which should be the invocation of the method being tested.
On `Assert` we would simply check whether the expectations were met.
In the next example, these principles are applied in a Jasmine style.

```javascript
describe('Array', () => {
  describe('.push(x)', () => {
    let initialArray;

    beforeEach(() => {
      initialArray = [1]; // Arrange
      initialArray.push(2); // Act
    });

    it('appends x to the end of the Array', () => {
      expect(initialArray).toEqual([1, 2]); // Assert
    });
  });
});
```

The AAA pattern is well known and recommended.
Posting comments to spell out each section is not recommended and should be avoided.

## Follow Test-Driven Development

[Test-Driven Development](https://en.wikipedia.org/wiki/Test-driven_development) is a robust way of designing software components ("units") interactively so that their behavior is specified through unit tests.
Consider writing the tests before the code in a r[ed-green-refactor](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html) style.
Consequences of the test-first cycle:

- Writing a test first makes the code design testable indeed;
- Writing just the amount of code needed to implement the required functionality makes the resulting codebase minimal, thus more maintainable;
- The codebase can be enhanced using refactoring mechanisms: The tests give us confidence that the new code is not modifying the existing functionalities;
- Cleaning the code in each cycle makes the codebase more maintainable: It is much cheaper to change the code frequently and in small increments;
- Fast feedback for the developers: We know that we don't break anything and that we are evolving the system in a good direction;
- Generates confidence to add features, fix bugs, or explore new designs.

## Follow the FIRST Principles

Acronym [FIRST](https://howtodoinjava.com/best-practices/first-principles-for-good-tests/) stand for below test features:

- [F]ast: Unit tests should be fast otherwise they will slow down the development/deployment time and will take longer time to pass or fail;
- [I]solated: Never ever write tests which depend on other test cases;
- [R]epeatable: A repeatable test is one that produces the same results each time we run it.
- [S]elf-validating: Each test must be able to determine that the output is expected or not. There must be no manual interpretation of results.
- [T]imely: We can write unit tests at any time.

## Describe expectations in a human language

Label the test suites (describe blocks) and specs (it blocks) in a way that clearly conveys the intention of each unit test.
Note that the name of each test is the title of its it preceded by all its parent describe names.
Favor assertive verbs and avoid ones like "should".
Thus, test suite becomes documentation for our codebase (helpful for new team members and non-technical stakeholders), failure messages accurately depict what is broken and forces good naming conventions in tested code.

## Name the tests properly

Tests names should be concise, explicit, descriptive and in correct English.
Read the output of the spec runner and verify that it is understandable.
Keep in mind that someone else will read it too.
Tests can be the live documentation of the code.
For more details, see the next example:

```javascript
describe('The Gallery instance', () => {
  describe('when initialized', () => {
    it('properly calculates the thumb size', () => {});

    it('properly calculates the thumbs count', () => {});
  });
});
```

## Structure the tests properly

Don't hesitate to nest our suites to structure logically our tests in subsets.
For more details:

```javascript
describe('A set of functionalities', () => {
  it('does something nice', () => {});

  describe('A subset of functionalities', () => {
    it('does something great', () => {});

    it('does something awesome', () => {});
  });

  describe('Another subset of functionalities', () => {
    it('also do something great', () => {});
  });
});
```

This organization allows tests to build on each other from least to most specific, creates tests that are easy to extend and/or refactor, makes branch testing easier and less repetitive and encapsulates tests based on their common denominator.

## Create new tests for every defect

Regression tests should be written for bug fixes to prevent them from recurring in the future.
Start by creating a test that replicates the problem before touching any code.
From there, we can apply [Test-Driven Development](https://en.wikipedia.org/wiki/Test-driven_development) as usual to fix it.
Identifying bugs is one of the crucial phases in the software development lifecycle.
Tracking the bug ensures quality assurance of software as well as eliminates the risk of post-release glitches.
This will prevent the same bug from happening again.

## Mock new Date() and Date.now

There are situations where `new Date()` or `Date.now` is used in application code.
`Date.now()` returns the unix time, ie. "the number of milliseconds elapsed since January 1, 1970 00:00:00 UTC.".
`new Date()` returns a new Date object, and behaves differently based on the input passed to it.
If called with nothing, it returns the current Date.
We must mock dates to obtain deterministic tests.
In this case, we should know how the test framework works.
For Jasmine, there is information in the [Mocking the Date](https://jasmine.github.io/2.6/introduction#section-Mocking_the_Date) section of the official documentation.
Dates are faked by default in [Jest](https://jestjs.io/) environments.

## Mock out all external services and state

When we run tests, the last thing we want to do is modify the external resources.
We therefore need to make sure that our unit tests are "stateless," so that we can run them whenever we need to, without cleaning our development environment for test conditions.
Otherwise, behavior in those external services overlaps multiple tests, and state data means that different unit tests can influence each other's outcome.
An additional benefit of using a mock environment is that tests run there much faster than they would via the external resources that are already currently functioning in real time.

## Do not test external libraries

Libraries are an integral part of any JavaScript developer's life.
The general advice would be to not test library internals but expect that the library knows what it's supposed to do and has test coverage on its own.
For example, we have a function that does nothing else but invoking the library function.
There is no value in this, besides, it is adding code to the test suite.
Better is to test it in the way the user interacts with it.

## Do not mock everything

In unit tests it is supposed to mock all external dependencies.
However, we don't need to mock everything.
The idea to keep in mind is that dependencies can still be "real" objects.
Consider using the "real" version of the objects if:

- It leads to a simple, nice and easy tests setup;
- It does not create a shared state between the tests, causing unexpected side effects;
- The code being tested does not make AJAX requests, API calls or browser page reloads;
- The speed of execution of the tests stays within the limits fixed.

## Do not test multiple concerns

If a method has several end results, each one should be tested separately.
Whenever a bug occurs, it will help us locate the source of the problem.
See the next example:

```javascript
it('sends the profile to the server and updates the profile view', () => {
  // expect(...)to(...);
  // expect(...)to(...);
});
```

Be careful when writing "AND" or "OR" when naming tests because is a code smell.
For the previous example, it is better to change to:

```javascript
it('sends the profile to the server', () => {
  // expect(...)to(...);
});

it('updates the profile view', () => {
  // expect(...)to(...);
});
```

## Do not skip unit tests

Tests have a reason to be or not.
Don't comment them out because they are too slow, too complex or produce false negatives.
Instead, make them fast, simple and trustworthy.
If not, remove them completely.
Do not use `xdescribe` or `xit` to skip their execution.
Use `no-disabled-tests` and `no-focused-tests` lint rules of [eslint-plugin-jasmine](https://www.npmjs.com/package/eslint-plugin-jasmine).
These rules will disallow use of disabled and focused tests.

## Do not unit-test configuration settings

The configuration settings aren't part of any unit of code.
That's why we extracted the setting out in some properties file.
Even if we could write a unit test that inspects the configuration, we should write only single or two test cases for verifying that configuration loading code is working.
Testing all the configuration settings in each separate test cases proves only one thing: "We know how to copy and paste".
To avoid this scenario, we can use [snapshot tests](https://jestjs.io/docs/snapshot-testing) to check the entire contents of the file.

## Do not overuse variables

A usual reflex of a developer is to extract values that are used multiple times to variables.
Unfortunately, this significantly bloats the test code.
Moreover, given a test failure message, it's harder to trace the value back to the relevant line of code.
If we keep the tests short, it's no problem to see where the same values are used.
For more details, see the next example:

```javascript
it('contains the item', () => {
  const expected = Repository.getById(4243);

  expect(expected.id).toBe(4243);
});
```

## Do not use static access

Static access is an anti-pattern.
First, it obfuscates dependencies and side-effects making the whole code harder to understand and more error-prone.
Second, static access harms testability.
We can't exchange the objects anymore.
But in a test, we want to use mocks or use the real objects with a different configuration.
So instead of access code statically, put it into non-static methods, instantiate the class and pass the object to the constructor of the object.
Do as in the next example:

```javascript
class ProductController {
  constructor(dao) {
    this._dao = dao;
  }

  getProducts() {
    return this._dao.getProducts();
  }
}
```

## Do not reuse production code

Test should test the production code and not reuse it.
If we reuse production code in a test, we might miss a bug that is introduced in the reused code because we don't test this code anymore.
Instead, we think in terms of input and output when writing tests.
The test sets the input and compares the actual output with hard-coded values.
Most of the time, code reuse is not required.

## Do not rewrite production logic

Mapping code is a common example where the logic in tests is rewritten.
So let's assume our tests contains a function `mapEntityToDto()` which result is used to assert that a returned DTO contains the same values than the entities that have been inserted at the beginning of the test.
In this case, we'll most likely end up rewriting the production logic in the test code, which can contain bugs.

```javascript
it('#requestProductById returns a ProductDTO', () => {
  const actualDTO = requestProductById(1);
  // The same mapping logic as the production code
  const expectedDTO = mapEntityToDto(new Product(1, 'name'));

  expect(actualDTO).toEqual(expectedDTO);
});
```

The solution is to compare the `actualDTO` with a manually created reference object with hard-coded values.
That's dead-simple, easy to understand and less error-prone.

## Do not catch errors. Expect them

When trying to assert that some input triggers an error, it might look right to use try-catch-finally and asserts that the catch clause was entered.
The result is an awkward and verbose test case that hides the simple test intent and the result expectations.
A more elegant alternative is the using the one-line dedicated assertion:

```javascript
it('adds a user in memory', () => {
  expect(userManager.addUser()).toThrow(new Error());
});
```

It's recommended to also ensure the exception contains a property that tells the error type, otherwise given just a generic error the application won't be able to do much rather than show a disappointing message to the user.

## Do not test complex user interactions

The complexity of writing tests is a good measure of the complexity of the code.
However, in some cases, some scenarios are out of the scope of unit tests.
Examples of complex user interactions:

- Filling a form, drag and dropping some items then submitting the form;
- Clicking a tab, clicking an image thumbnail then navigating through a gallery of images previously loaded from a database.

These interactions might involve many units of work and should be handled at a higher level by functional tests.
Instead, test simple user actions.
Examples of simple user actions:

- Clicking on a link that toggles the visibility of a DOM element;
- Submitting a form that triggers the form validation.

These actions can be easily tested by simulating DOM events, for example, when a "click" event occurs, a public method is called.

## Do not Repeat Yourself

Use `before/after` blocks to DRY up repeated setup, teardown, and action code.
This organization keeps test suite more concise and readable.
Changes only need to be made in one place.
For more details, see the next example:

```javascript
describe('Array .push(x)', () => {
  let initialArray, pushResult;

  beforeEach(() => {
    initialArray = [1];
    pushResult = initialArray.push(2);
  });

  it('appends x to the end of the Array', () => {
    expect(initialArray).toEqual([1, 2]);
  });

  it('returns x', () => {
    expect(pushResult).toBe(2);
  });
});
```

## Avoid toBeTruthy or toBeFalsy

[Jasmine](https://jasmine.github.io/) and [Jest](https://jestjs.io/) also provide following matchers: `toBeTruthy` and `toBeFalsy`.
We should not use them because they make tests weaker and produce false-positive results.
For example, `expect(someBoolean).toBeFalsy()` passes when `someBoolean === null`, and when `someBoolean === false`.
Both have the tricky `toBeDefined` matcher that can produce false positive test.
Because it validates the given value for undefined only.

## Avoid the All's

Prefer `beforeEach/afterEach` blocks over `beforeAll/afterAll` ones.
The latter are not reset between tests.
Replacing `All` blocks by `Each` blocks we will avoid accidental state leak, enforce test independence and improve test readability because the order of `All` block execution relative to `Each` ones is not always obvious.

## Avoid mocks in favor of stubs and spies

We got to stub some dependencies since we can't do everything in our tests as we do in a real environment.
Our tests shouldn't depend on anything outside and they should be isolated.
Therefore, we stub all the dependencies that commit side effects so that we can just test what we want to test in isolation.
We just stub any network request code.
We watch what we want to check is called with spies.
We avoid mock any server-side interactions with the spies approach.

## Avoid global test fixtures and seeds

We should create our data per test and clean them out after each test.
This way, we always get a clean environment for our tests.
Therefore, the tests won't depend on each other.
This is important since we'll run into problems when tests depend on each other.
If performance becomes a concern with creating data for each test, then we've to simplify the data.
So, if we test with database interaction, we got to remove all the data after each test.

## Avoid logic in the tests

The logic of code inherently requires a premise, a condition, and a conclusion.
If a test were to include logic, therefore, it would not be a "one-concern" test.
Furthermore, we simply don't want to write bugs into our tests, and we're at risk of that when we incorporate logic.
We always use simple statements.
Don't use loops, switch cases and/or conditionals.
If we do, we add a possible entry point for bugs in the test itself:

- Conditionals: we don't know which path the test will take;
- Loops: we could be sharing state between tests.

It´s better to write a test for each scenario. When we test only one concern per test, we can evaluate our code precisely.
It will give a nice output of all possible cases, improving maintainability.

## Prefer toBe over toEqual for primitive values

[Jasmine](https://jasmine.github.io/) and [Jest](https://jestjs.io/) have `toBe` and `toEqual` matchers.
As `toBe` uses `Object.is` to compare values, it's faster (by default) than using `toEqual`.
While the latter eventually falls back to leverage `Object.is`, for primitive values, it should only be used when complex objects need a comparison.
`toEqual` recursively checks every field of an object or array.

## Cover the general case and the edge cases

"Strange behavior" usually happens at the edges.
Remember that the tests can be the live documentation of our code.
In software testing, [code coverage and test coverage](https://www.browserstack.com/guide/code-coverage-vs-test-coverage) are important metrics.
They are important benchmarks by which to measure the effectiveness of the codebase.
Code coverage is performed to verify the extent to which the code has been executed.
The test coverage report provides information about parts of the software where test coverage is being implemented.

## Test only public methods

Testing the internals brings huge overhead for almost nothing.
If the code/API delivers the right results, should we really invest our time in testing how it worked internally and then maintain these fragile tests?
Whenever a public behaviour is checked, the private implementation is also implicitly tested, and the tests will break only if there is a certain problem (e.g. wrong output).
This approach is also referred to as behavioural testing.

## Test the behaviour and not the implementation

It is recommended to check the result of an operation and not the internal implementation.
With this approach, changing the internal implementation of a class/object will not necessarily force us to refactor the tests.
In the next example, we have a bad example because we are testing the internal implementation.

```javascript
it('adds a user in memory', () => {
  userManager.addUser('Dr. Falker', 'Joshua');

  expect(userManager._users[0].name).toBe('Dr. Falker');
  expect(userManager._users[0].password).toBe('Joshua');
});
```

## Test asynchronous code with async/await

When testing Promises we should always make sure that the test is asynchronous, and rejections are handled.
It's recommended to use the `async/await` syntax in the test suite:

```javascript
it('tests a promise', async () => {
  const users = await fetchUsers();

  expect(users.length).toBe(42);
});
```

Using the `done` and `done.fail` callbacks is discouraged when working with promises.
They should only be used when testing callback-based code.

## Test many input combinations

Typically, we choose a few input samples for each test.
However, in production, an API that is called with 5 parameters can be invoked with thousands of different permutations.
We should test many input combinations.
This way, we won't only choose cases that we know will make our test pass.
We can make the values random.
For example, with the [fast-check](https://www.npmjs.com/package/fast-check) or [jsverify](https://www.npmjs.com/package/jsverify) library, we can create random combinations of data for our tests.

## Use lint rules

Linters can catch severe issues like errors that are not thrown correctly and losing information.
Having lint rules in place means that we will get a nice error when we are doing something that we should not be.
This will enforce consistency in our application and readability.
On top of the basic set of rules (like [ESLint standard](https://www.npmjs.com/package/eslint-plugin-standard) or [Airbnb style](https://www.npmjs.com/package/eslint-config-airbnb)), consider including some specializing Linters.
[eslint-plugin-promise](https://www.npmjs.com/package/eslint-plugin-promise) can discover promises with no resolve.
[eslint-plugin-security](https://www.npmjs.com/package/eslint-plugin-security) which can discover eager regex expressions that might get used for DOS attacks.
[eslint-plugin-jest](https://www.npmjs.com/package/eslint-plugin-jest) or [eslint-plugin-jasmine](https://www.npmjs.com/package/eslint-plugin-jasmine) they will show a warn when tests have no assertions at all or when tests are skipped which might lead to a false belief that all tests are passing.

## Use realistic input data

Many times, it so happens that the testers use the inputs that are not realistic or practical to the real-life scenarios.
Often production bugs are revealed under some very specific and surprising input.
So, we should always use realistic inputs while testing our application.
The more realistic the test input is, the greater the chances are to catch bugs early.
Use dedicated libraries like [Faker](https://www.npmjs.com/package/faker) to generate pseudo-real data that resembles the variety and form of production data.

## Use factory functions

A factory function is any function which is not a class or constructor that returns a (presumably new) object.
In JavaScript, any function can return an object.
When it does so without the new keyword, it's a factory function.
Factories can:

- Help reduce the setup code, especially if we use dependency injection ot to encapsulate complex code;
- Make each test more readable, since the creation is a single function call that can be in the test itself instead of the setup;
- Provide flexibility when creating new instances (setting an initial state, for example).

## Use the prefixes "actual" and "expected"

If we are going to use variables in an equals assertion, prefix the variables with `actual` and `expected`.
This increases the readability and clarifies the intention of the variable.
Moreover, it's harder to mix them up in the equals assertion.

```javascript
it('appends x to the end of the Array', () => {
  const actualArray = [1, 2];
  const expectedArray = INITIAL_ARRAY.concat([2]);

  expect(actualArray).toEqual(expectedArray);
});
```

## Tests should be isolated and atomic

Every test should run independently and without being dependant on each other.
If neither test is dependent on any other test, then if one test fails, other tests are not affected.
Also, the tests should follow the atomicity property.
It should not fail in between suddenly.
A test should be entered and exited with the pass or fail result smoothly.
We should also keep in mind that the data we are testing upon should be separate for every test.

## Design for lean testing

Testing code is not like production-code.
Design it to be dead-simple, short, abstraction-free, flat, delightful to work with, lean.
One should look at a test and get the intent instantly.
Test only as much as needed, strive to keep it nimble, sometimes it's even worth dropping some tests and trade reliability for agility and simplicity.

## Categorize tests under at least 2 levels

Apply some structure to the test suite so an occasional visitor could easily understand the requirements and the various scenarios that are being tested.
A common method for this is by placing at least 2 "describe" blocks above the tests.
The first is for the name of the unit under test and the second for additional level of categorization like the scenario or custom categories.
Doing so will also greatly improve the test reports.
In addition, it will get much easier for a developer to navigate through the code of a suite with many tests.

## Shorten the feedback loop

An efficient testing process constitutes many and iterative loops: (1) try-outs -> (2) feedback -> (3) refactor.
The faster the feedback is the more improvement iterations a developer can perform per-module and perfect the results.
We can get feedback faster is we run CI locally on the developer's machine.
So, we can write our code, get feedback, and then make changes as needed.

## Tag the tests

Different tests must run on different scenarios: quick smoke, IO-less, tests should run when a developer saves or commits a file, full end-to-end tests usually run when a new pull request is submitted, etc.
This can be achieved by tagging tests with keywords like #services, #api, #sanity so we can grep with the testing harness and invoke the desired subset.
Otherwise, running all the tests any time, a developer makes a small change can be extremely slow and keeps developers away from running tests.

## Focus on test coverage

Coverage is a measure of how many code lines (and branches, statements, etc.) are being reached by the tests.
So how much is enough?
10–30% is obviously too low to get any sense about the build correctness, on the other side 100% is very expensive and might shift the focus from the critical paths to the exotic corners of the code.
The long answer is that it depends on many factors like the type of application.
Although most of the testing enthusiasts claim that the right coverage threshold is contextual, most of them also mention [the number 80%](https://martinfowler.com/bliki/TestCoverage.html) as a thumb of a rule that presumably should satisfy most of the applications.

## Analyse the test coverage report

Some issues sneak just under the radar and are hard to find using traditional tools.
These are not really bugs but more of surprising application behaviour that might have a severe impact.
For example, often some code areas are never or rarely being invoked.
If we don't know which parts of our code are left un-tested, we don't know where the issues might come from.

## Avoid duplicate coverage

Don´t create tests for features coverage by other tests.
Let's say we've created a new UI component.
If we can cover the functionality in a unit test, we should do it.
Unit tests are generally easier to maintain, less flakey, and less expensive to run in our CI pipeline.
UI tests should be covering the areas only they can cover.
Usually, these are the big-picture user stories that span many components and views.

## Measure logical coverage

The traditional coverage metric often lies.
It may show us 100% code coverage, but none of our functions, even not one, return the right response.
It simply measures over which lines of code the test visited, but it doesn't check if the tests actually tested anything.
We can use [mutation testing](https://en.wikipedia.org/wiki/Mutation_testing) to tell which part of our app is tested rather than just visited.
To do that, we can intentionally change the values to check the outcomes as a result of them.
We may uncover cases that should fail that doesn't come up that we may have not covered yet.

## Follow the Testing Pyramid

We should try to follow the [test automation pyramid](https://www.lambdatest.com/blog/how-agile-teams-use-test-automation-pyramid/).
Based on [Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html), the unit test should be taken as the base of all the testing.
We do so because the unit test will cover basic units of functionality independently of one another.
After the unit tests are done, then move ahead to the integration testing.
Integration testing will let us test the different modules combined with one another as a group.
After that, we move on to the next part of the pyramid and test the front-end or User-Interface testing using Selenium or similar tools.
The cost incurred continues to increase as we move to the top of the pyramid, but the speed continues to decrease.

## Parallelize test execution

Parallel testing means running multiple test cases, simultaneously.
Running different tests in parallel has its own advantages.
If we are not following parallelism, then we will run one test and provide the feedback about it then, we will run other tests and provide feedback about it and so on.
These feedbacks are then analysed and are worked upon.
Then, the team will check the feedback of the second test we did and then resolve them.
While following parallelism, we can drastically reduce the feedback loop and provide feedback of many tests altogether which can be resolved in a lesser time as before.
This way, we can save a lot of time and resources of the company.

## Know the testing framework API

The API documentation of the testing framework/library should be our bedside book.
Having a good knowledge of the API can help us in reducing the size/complexity of the test code and, in general, help us during development.
A simple example:

```javascript
it('calls a method with the proper arguments', () => {
  const foo = { bar: jasmine.createSpy() };

  foo.bar('qux');

  expect(foo.bar).toHaveBeenCalled();
  expect(foo.bar.calls.argsFor(0)).toEqual(['qux']);
});
```

It is simpler and more readable if done as follows:

```javascript
it('calls once a method with the proper arguments', () => {
  const foo = jasmine.createSpyObj('foo', ['bar']);

  foo.bar('baz');

  expect(foo.bar).toHaveBeenCalledWith('baz');
});
```

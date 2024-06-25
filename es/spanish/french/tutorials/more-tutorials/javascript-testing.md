---
description: How to test Javascript, using Javascript
---

# JavaScript Testing

Developing [DApps](../../support-and-community/glossary.md#decentralized-application-dapp) typically involves using quite a lot of JavaScript. This can happen in several different areas:

- The DApp [client](../../support-and-community/glossary.md#client) itself, when the DApp is browser-based
- The backend APIs, when the DApp uses some web2 server components
- Test code for [smart contracts](../../support-and-community/glossary.md#smart-contract)
- Test code for DApp client
- Test code for backend APIs

Looking at the latter three, suffice it to say that learning testing with JavaScript is a fundamental skill for DApp developers. Unfortunately, it is one that is often overlooked. This tutorial aims to fill that gap. If you're planning to create a DApp on Hedera, but wish to brush up on the basics of testing first, start here!

#### What we will cover

A test runner is a developer tool that helps you to:

- Structure your tests.
- Execute your tests against a target application (known as the _system under test_).
- Report the results of the tests.

One of the most popular JavaScript test runners is [Mocha](https://mochajs.org/); and this is what you will be using in this tutorial. We picked this because both [Hardhat](https://hardhat.org/) and [Truffle](https://trufflesuite.com/), two of the most popular smart contract development frameworks, use Mocha under the hood in their built-in smart contract testing features. This means that the syntax you learn here will be familiar and useful when building smart contracts for the Hedera Smart Contract Service (HSCS).

In this tutorial, let's start with a very simple application and modify its implementation to cover four different scenarios you'll encounter during development.

- True positive
- True negative
- False negative
- False positive

<details>

<summary>Definitions of these test outcome scenarios</summary>

We'll define these as we go through the tutorial steps, but in case you would like a preview:

- **True positive** -> both the implementation and specification are correct.
- **True negative** -> the implementation is wrong, but the specification is correct.
- **False negative** -> the implementation is correct, but the specification is wrong.
- **False positive** -> both the implementation and specification are wrong.

</details>

For each scenario, you'll cover what to look out for and how to handle it properly.

Let's begin!

***

## Prerequisites

Prior knowledge

- JavaScript syntax

System

- `git` installed
  - Minimum version: 2.37
  - Recommended setup method: [Install Git (Github docs)](https://github.com/git-guides/install-git)
  - SSH keys configured for Github: [Add SSH key (Github docs)](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
- NodeJS + `npm` installed
  - Minimum version (NodeJS): 18
  - Recommended setup method for Linux & Mac: [`nvm`](https://github.com/nvm-sh/nvm)
  - Recommended setup method for Windows: [`nvm-windows`](https://github.com/coreybutler/nvm-windows)

***

## Step 1: Set up the project

This has already been (mostly) done. All that's left for you to do is clone the [accompanying tutorial GitHub repository](https://github.com/hedera-dev/js-testing) and install the dependencies:

```sh
git clone git@github.com:hedera-dev/js-testing.git
cd js-testing
npm install
```

{% hint style="info" %}
If you do not have SSH available on your system, or are unable to configure it for GitHub, you may wish to try this git command instead:\
\
`git clone https://github.com/hedera-dev/js-testing.git`
{% endhint %}

Open this repository in your code editor, and you'll find the following files:

`package.json`

- `mocha` is installed as a `devDependency.`
- The `test` command is configured to run `mocha` on all files with a `.spec.js` file extension.
- The `test:generative` command is configured to run `mocha` on all files with a `.genspec.js` file extension - note that this is only needed for the bonus step.

`my-app.js`

- The system under test, also referred to as _the implementation._
- This will contain a single `add` function - very simple, but enough to demonstrate a point.

`my-app.spec.js`

- The tests, also referred to as _the specification._
- These specify what the behavior of the implementation should be.

***

## Step 2: Implement the system under test

In the `add` function within `my-app.js,` you should see a comment marking _Step 2_. It looks like this:

```javascript
    // TODO: Implement the system under test
    // Follow step (2) in tutorial to complete the following section.

```

This is where you'll add the code for this step. The implementation for addition is self-explanatory.

```javascript
return x + y;
```

It should now look like this:

```javascript
    // TODO: Implement the system under test
    // Follow step (2) in tutorial to complete the following section.
    return x + y;
```

{% hint style="info" %}
**Note**: In the subsequent steps of this tutorial, you will follow the same pattern as above. However, you will not copy the comments marking the steps for the remainder of the tutorial and instead only include the new/changed lines of code.
{% endhint %}

Now you have completed your system under test!

***

## Step 3: Implement the tests

In `my-app.spec.js`, find the test block with the title `works with known values`, and add the following implementation where indicated with the comment.

```javascript
const actual = add(1, 2);
const expected = 3;
assert.equal(actual, expected);
```

- The `actual` value is obtained from invoking the system under test.
- The `expected` value is simply written by you as the test writer.
- Finally, you have an assertion that checks that the `actual` and `expected` values match.

Now you have completed the specification!

Since both the system under test and the tests for it are ready, you're ready to invoke the test runner.

```bash
npm run test
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test
> mocha './**/*.spec.js'

  my-app
    add
      ✔ works with known values
      ✔ is associative with known values
      ✔ is commutative with known values

  3 passing (3ms)

```

So all the tests have passed: One that you have just added ('known values'), and two more that were included in the initial state of the tutorial repo ('associative', and 'commutative').

You could wrap up here... but you're not quite done yet. This is a **true positive scenario**, where both the implementation and the specification are correct. But there are three other possible scenarios that you're likely to encounter when writing tests, so let's go through them in the next few steps!

***

## Step 4: Switch to a true negative scenario

In the **true negative scenario**, the implementation is wrong, and the specification is correct. This is probably the most common test failure scenario you'll encounter during development.

In `my-app.js`, comment out the existing code from _Step 2_, and add this new implementation for _Step 4_.

```javascript
return x + y + 1;
```

In `my-app.spec.js`, comment out the assertion from _Step 3_ (keep the other 2 lines of code), and add this new line of code for _Step 4_. (Note that this happens to be the same as before.)

```javascript
assert.equal(actual, expected);
```

Now let's re-run the tests.

```bash
npm run test
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test
> mocha './**/*.spec.js'

  my-app
    add
      1) works with known values
      ✔ is associative with known values
      ✔ is commutative with known values

  2 passing (4ms)
  1 failing

  1) my-app
       add
         works with known values:

      AssertionError [ERR_ASSERTION]: 4 == 3
      + expected - actual

      -4
      +3
```

This shows that the `actual` value was `4`, while the `expected` value was `3`. While this is an error, it is actually a good thing - the main point of writing tests is to catch errors in your application, and that is precisely what has happened here. Even though this is a contrived example, as the error in the implementation is so obvious, it nevertheless illustrates the developer workflow involved: Finding bugs in the implementation of an application by specifying how it should behave, and using a test runner to detect where there are problems.

{% hint style="info" %}
Interestingly, the tests for associativity and commutativity do not fail, even with this bug in the implementation. This illustrates that not all tests are able to catch bugs, and why several different tests are necessary. This can be the case even for simple/ "obvious" bugs.
{% endhint %}

As a developer, at this point, you would typically _fix_ the error in the _implementation_, and then _re-run the tests_ to ensure that they pass once again. You will do so eventually, towards the end of this tutorial. However, for now, you'll move on to another scenario.

***

## Step 5: Switch to a false negative scenario

In the **false negative scenario**, the implementation is correct, and the specification is wrong. This is not as common of a scenario that you'll encounter during development as the false positive, but it is nonetheless important to be able to recognize it when it does occur and rectify it accordingly.

In `my-app.js`, comment out the existing code from _Step 4_, and add this new implementation for _Step 5_.

```javascript
return x + y;
```

Likewise, in `my-app.spec.js`, comment out the existing code from _Step 4_, and add this new implementation for _Step 5_.

```javascript
assert.equal(actual, expected + 1);
```

Now let's re-run the tests.

```sh
npm run test
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test
> mocha './**/*.spec.js'

  my-app
    add
      1) works with known values
      ✔ is associative with known values
      ✔ is commutative with known values

  2 passing (4ms)
  1 failing

  1) my-app
       add
         works with known values:

      AssertionError [ERR_ASSERTION]: 3 == 4
      + expected - actual

      -3
      +4
```

This time, the error shows that the `actual` value was `3`, while the `expected` value was `4`. This is the _opposite_ of previous test error - the `actual` and `expected` values have swapped positions. The somewhat tricky thing here is to not "default" to assuming that the error must be in the implementation, and therefore only look into finding a bug in the implementation. The correct thing to do, whenever the test runner reports a test failure is to _analyse both_ the implementation and specification, and _identify which one_ of them contains an error.

As a developer, at this point, you would typically _fix_ the error in the _specification_, and then _re-run the tests_ to see if they pass once again. Instead, let's move on to another scenario.

***

## Step 6: Switch to a false positive scenario

In the **false positive scenario**, the implementation is wrong, and the specification is also wrong. This is typically the least common scenario that you'll encounter during development. And it can be extremely tricky to even identify, as you'll see shortly.

In `my-app.js`, comment out the existing code from _Step 5_, and add this new implementation for _Step 6_.

```javascript
return x + y + 1;
```

Likewise, in `my-app.spec.js`, comment out the existing code from _Step 5_, and add this new implementation for _Step 6_.

```javascript
assert.equal(actual, expected + 1);
```

Now let's re-run the tests.

```sh
npm run test
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test
> mocha './**/*.spec.js'

  my-app
    add
      ✔ works with known values
      ✔ is associative with known values
      ✔ is commutative with known values

  3 passing (2ms)
```

Somewhat surprising is it not? Even though both the implementation and specification were wrong, all the tests pass. In fact, the output from the test runner looks _identical_ to when both the implementation and specification were correct. This occurs because the implementation code and the specification code coincidentally happen to make the same error, and they "cancel" each other out.

While in this case it may be obvious, that is only because of the simplicity of this tutorial. Spotting false positive scenarios in a complex application is _much harder_. More than just tricky, this scenario is insidious due to its ability to have bugs and yet pass the tests meant to catch them. In fact, there is no way for a developer to identify a false positive scenario from the test results alone.

{% hint style="info" %}
False positive scenarios are typically spotted through manual reviews of the both the implementation and specification code, or other code quality processes such as static/ dynamic analyses and code code coverage. These are beyond the scope of this tutorial.
{% endhint %}

Now let's finally fix the code, and go back to the true positive scenario, where you started off.

***

## Step 7: Switch back to a true positive scenario

In the **true positive scenario**, the implementation is correct, and the specification is correct as well. This is the **ideal** scenario among the 4 possible ones that you've covered thus far. Whenever there are errors identified in either the implementation or specification, the goal is to fix them such that you _return_ to this true positive scenario.

In `my-app.js`, comment out the existing code from _Step 6_, and add this new implementation for _Step 7_.

```javascript
return x + y;
```

Likewise, in `my-app.spec.js`, comment out the existing code from _Step 6_, and add this new implementation for _Step 7_.

```javascript
assert.equal(actual, expected);
```

Now let's re-run the tests.

```sh
npm run test
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test
> mocha './**/*.spec.js'

  my-app
    add
      ✔ works with known values
      ✔ is associative with known values
      ✔ is commutative with known values

  3 passing (2ms)
```

Back to all tests passing (for the right reasons)!

***

## Step 8: Bonus - Add generative testing

Thus far, all the tests that you have written (in `my-app.spec.js`) are example based tests. Essentially your tests consist of one or more interactions with the system under test actual value, an expected value, and an assertion that the actual value matches the expected value. In this step, we'll add a _different type of tests_ to this project: Generative testing.

{% hint style="info" %}
Generative testing may be thought of as an abstract form of example based testing, where you write the tests in such a way that they do not require you to specify any input values for the examples that you write tests for. Instead you let the test runner generate value at random as the input value used in your tests. In other words, the tested examples are generated by the test runner, instead of being manually specified.
{% endhint %}

The demo repo for this tutorial has already been wired up with a dependency named [`testcheck.js`](https://github.com/leebyron/testcheck-js). This provides a plugin that augments Mocha with a new `check.it()` function that behaves in a very similar manner to `it()` in Mocha. The key difference being that it contains some _generator functions_ that produce _random values_ of a specified type. You will be using `gen.int` to produce random Integer values to use as inputs for the parameters of the `add()` function the application.

Checkout the `my-app.genspec.js` file, where this has already been set up for you. The 'commutative' and 'associative' tests have been copied over from `my-app.spec.js`, and modified to use `check.it()` plus `gen.int`. Compare the method signatures of the callback functions, and the difference between example based testing and generative testing should be clear.

In `my-app.genspec.js`, replace these two lines of code for [_Step 8_](javascript-testing.md#step-8-bonus-add-generative-testing), in the 'is associative' test.

```javascript
const lhsOfEquation = add(a, add(b, c));
const rhsOfEquation = add(add(a, b), c);
```

Also, in the 'is commutative' test, replace these two lines of code.

```javascript
const lhsOfEquation = add(a, b);
const rhsOfEquation = add(b, a);
```

Note that in both tests, the assertions do not need to be modified, you can keep them as is.

{% hint style="info" %}
Compare this to the similar example based tests for associativity and commutativity within `my-app.spec.js`. These will use the same literal/ hard-coded values every time you run the tests, unlike the generative tests within `my-app.genspec.js`.

Note that the values of `a`, `b`, and `c` above are the randomly generated integers. This means that every time you run the generative tests, you will be testing the `add()` function using a new set of input values.
{% endhint %}

Now let's run the new generative tests.

```sh
npm run test:generative
```

You should now see some output that looks similar to the following:

```bash
> tutorial-js-testing@0.0.0 test:generative
> mocha './**/*.genspec.js'

  my-app
    add
      ✔ is associative
      ✔ is commutative
bas
  2 passing (17ms)
```

These generative tests are passing as well and form an additional layer of verification of the system under test. You can now be extra sure that the implementation is indeed correct.

#### Congrats!

You've completed this tutorial! :tada: :tada: :tada:

Now that you have covered the basics of testing, you're ready to test more complex applications. If you have smart contracts that you intend to deploy to Hedera Smart Contract Service (HSCS), it is best practice to test them before deployment. If you use Hardhat or Truffle, you will already be familiar with much of the syntax for the specification code.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Brendan, DevRel Engineer</p><p><a href="https://github.com/bguiz">GitHub</a> | <a href="https://blog.bguiz.com">Blog</a></p></td><td><a href="https://blog.bguiz.com/">https://blog.bguiz.com/</a></td></tr><tr><td align="center"><p>Editor: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr></tbody></table>

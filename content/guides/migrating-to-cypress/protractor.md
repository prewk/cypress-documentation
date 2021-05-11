---
title: Migrating from Protractor to Cypress
---

<Alert type="info">

## <Icon name="graduation-cap"></Icon> What you'll learn

- Benefits of using Cypress in Angular apps
- How Cypress can create reliable e2e tests for Angular apps
- How to migrate Protractor tests to Cypress

</Alert>

## Introduction

Considering using Cypress but unsure of whether the migration is worth it for your Angular app? We got you covered here with this migration guide to help transition from Protractor to Cypress.

If you see any inaccuracies with this or feel something has been misrepresented, please [submit an issue here](https://github.com/cypress-io/cypress-documentation/issues/new).

To start, let's take a quick look at a common test case scenario: allowing a user to sign up for a new account.

<Badge type="danger">Before: Protractor</Badge>

```js
describe('Authorization tests', () => {
  it('allows the user to signup for a new account', () => {
    browser.get('/signup')
    element(by.css('#email-field')).sendKeys('user@email.com')
    element(by.css('#confirm-email-field')).sendKeys('user@email.com')
    element(by.css('#password-field')).sendKeys('testPassword1234')
    element(by.cssContainingText('button', 'Create new account')).click()

    expect(browser.getCurrentUrl()).toEqual('/signup/success')
  })
})
```

<Badge type="success">After: Cypress</Badge>

```js
describe('Authorization Tests', () => {
  it('allows the user to signup for a new account', () => {
    cy.visit('/signup')
    cy.get('#email-field').type('user@email.com')
    cy.get('#confirm-email-field').type('user@email.com')
    cy.get('#password-field').type('testPassword1234')
    cy.get('button').contains('Create new account').click()

    cy.url().should('include', '/signup/success')
  })
})
```

## Why Choose Cypress over Protractor?

A common question when teams are considering migrating to a different tool is why choose a different tool when something like Protractor is created specifically for Angular. After all, isn't a tailored tool to the framework better than something more generic like Cypress?

While this logic holds true in many tool evaluations, end-to-end testing is different because **the value of end-to-end testing resides in ensuring that what the user will see and use on production works**. In other words, whether your application is created using AngularJS, Angular 2, or Angular 9, users will be unaware of this detail. After all, at the end of the day, the user cares primarily about one thing: "Does it work?"

## Benefits of Using Cypress

As many developers can attest to, end-to-end testing is one of those things that they know they _should_ do, but often do not. Or if they do run tests, the tests are often flaky and often very expensive due to how long it can take to run. And while there are often ideals of complete code coverage, the realities of business and deadlines often take precedence and the tests are left unwritten, or worse, ignored when errors are being reported because they are not reliable. With Cypress, not only will your tests be reliable, but it provides developers with tools that make e2e testing an asset to development rather than a hindrance.

### Interact with your tests in a browser

When Protractor runs tests, the browser automation launches a browser instance and often runs through tests too fast for the human eye. Without additional configuration, this often leads to a reliance on lengthy terminal errors that can be expensive from a context-switching perspective.

Video Placeholder

### Faster feedback loops

When it comes to your end-to-end tests, being able to see your tests as they run is critical to allowing you to iterate faster with confidence. Though this is not possible out of the box with Protractor, Cypress tests automatically re-run within the browser whenever they are saved which allows you to iterate faster with confidence.

Video Placeholder

### Time travel through tests

Rather than trying to decipher errors inside of your terminal, with Cypress you can debug your tests just like you would debug your application normally. In addition, Cypress allows you to see snapshots of your Angular application as the test is executed so you can debug your applications more easily.

Video Placeholder

### Easily automate screenshots

Another powerful feature of end-to-end testing is the ability to capture screenshots and recording. While possible in both tools, Protractor requires you to manually tap into Node.js and has limited configuration options.

<Badge type="danger">Before: Protractor</Badge>

```js
const fs = require('fs')

describe('Dashboard', () => {
  it('should render dashboard', () => {
    browser.get('/dashboard')
    browser.takeScreenshot().then((png) => {
      const stream = fs.createWriteStream(filename)
      writeScreenShot(png, 'dashboard.png')
      stream.write(new Buffer(png, 'base64'))
      stream.end()
    })
  })
})
```

<Badge type="success">After: Cypress</Badge>

```js
describe('Dashboard', () => {
  it('should render dashboard', () => {
    cy.visit('/dashboard')
    cy.screenshot('dashboard.png')
  })
})
```

Video Placeholder

<Alert type="info">

For more information on what configuration options are available, be sure to check out the [official Cypress documentation on the screenshot command](/api/commands/screenshot).

</Alert>

## FAQs

### Do I have to replace all of my tests with Cypress immediately?

Absolutely not. While it might sound ideal to replace Protractor entirely, we know that most projects and teams still need to deliver features to customers and it's unrealistic to halt the team's progress.

### Can Protractor and Cypress coexist in the same app?

Yes! Your Protractor tests would continue to live in the `e2e` directory that Angular CLI scaffolded while all Cypress tests would live in a sibling folder named `cypress`.

```
.
├── cypress
├── e2e
├── src
├── .editorconfig
├── .gitignore
├── angular.json
├── browserslist
├── cypress.json
├── karma.conf.js
├── package.json
├── README.md
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.spec.json
└── tslint.json
```

In fact, as you work through migrating to Cypress, we believe that progressively enhancing your e2e tests with Cypress is the best path forward to ensure that feature development is not impacted.

## Getting Started

### Angular Schematic

We recommend using our official Cypress Angular schematic to install Cypress in your Angular project:

```shell
ng add @cypress/schematic
```

With our schematic installed, you can run Cypress with the following commands:

```shell
ng e2e
```

```shell
ng run {your-project-name}:cypress-open
```

These two commands do the same thing. They will launch the Cypress Test Runner in an Electron browser.

```shell
ng run {your-project-name}:cypress-run
```

This command will open the Cypress Test Runner and run your tests one time, with output to your terminal.

### Manual Installation

You can still install Cypress without using our official Angular schematic.

<code-group>
  <code-block label="npm" active>

```bash
npm install --save-dev cypress
```

  </code-block>
  <code-block label="yarn">

```bash
yarn add -D cypress
```

  </code-block>
</code-group>

Then, since Cypress can run in parallel with your application, let's install [concurrently](https://www.npmjs.com/package/concurrently) to simplify our npm script.

<code-group>
  <code-block label="npm" active>

```bash
npm install --save-dev concurrently
```

  </code-block>
  <code-block label="yarn">

```bash
yarn add -D concurrently
```

  </code-block>
</code-group>

Then we will update our `package.json` with the following script:

```bash
"cypress": "concurrently \"ng serve\" \"cypress open\""
```

```json
// Example package.json
{
  "scripts": {
    "e2e": "ng e2e",
    "e2e:debug": "node --inspect-brk ./node_modules/.bin/protractor ./e2e/protractor.conf.js",
    "cypress": "concurrently \"ng serve\" \"cypress open\""
  },
  "dependencies": { ... },
  "devDependencies": { ... }
}
```

Now, when we run:

<code-group>
  <code-block label="npm" active>

```bash
npm run cypress
```

  </code-block>
  <code-block label="yarn">

```bash
yarn run cypress
```

  </code-block>
</code-group>

It will start up Cypress and our Angular app at the same time!

<DocsVideo src="/img/guides/protractor-to-cypress/npm-run-cypress.mp4" title="Cypress Tips and Tricks"></DocsVideo>

## Essentials

### How to Get DOM Elements

#### Getting a single element on the page

When it comes to e2e tests, one of the most common things you'll need to do is get one or more HTML elements on a page. Rather than split element fetching into multiple methods that you need to memorize, everything can be accomplished with `cy.get` while using CSS selectors to account for all use cases.

<Badge type="danger">Before: Protractor</Badge>

```js
// Get an element
element(by.tagName('h1'))

/// Get an element using a CSS selector.
element(by.css('.my-class'))

// Get an element with the given id.
element(by.id('my-id'))

// Get an element using an input name selector.
element(by.name('field-name'))
```

<Badge type="success">After: Cypress</Badge>

```js
// Get an element
cy.get('h1')

// Get an element using a CSS selector.
cy.get('.my-class')

// Get an element with the given id.
cy.get('#my-id')

// Get an element using an input name selector.
cy.get('input[name="field-name"]')
```

### Getting multiple elements on a page

When you want to get access to more than one element on the page, you would need to chain the `.all()` method. However, in Cypress, no syntax change is necessary!

<Badge type="danger">Before: Protractor</Badge>

```js
// Get all list-item elements on the page
element.all(by.tagName('li'))

/// Get all elements by using a CSS selector.
element.all(by.css('.list-item'))

// Find an element using an input name selector.
element.all(by.name('field-name'))
```

<Badge type="success">After: Cypress</Badge>

```js
// Get all list-item elements on the page
cy.get('li')

/// Get all elements by using a CSS selector.
cy.get('.list-item')

// Find an element using an input name selector.
cy.get('input[name="field-name"]')
```

You can learn more about [how to get DOM elements in our official documentation](/api/commands/get#Syntax).

### Element Explorer

For those who are big fans of [Protractor's Element Explorer functionality](https://www.protractortest.org/#/debugging#enabled-control-flow), Cypress also provides you with a [Selector Playground](/guides/core-concepts/test-runner#Selector-Playground) that allows you to:

- Determine a unique selector for an element
- See what elements match a given selector
- See what element matches a string of text

The Selector Playground can be useful when you need to find a specific selector to use in your Cypress tests.

<DocsVideo src="/img/snippets/selector-playground.mp4" title="Selector Playground"></DocsVideo>

## How to Interact with DOM Elements

<Badge type="danger">Before: Protractor</Badge>

```js
// Click on the element
element(by.css('button')).click()

// Send keys to the element (usually an input)
element(by.css('input')).sendKeys('my text')

// Clear the text in an element (usually an input).
element(by.css('input')).clear()

// Get the attribute of an element
// Example: Get the value of an input
element(by.css('input')).getAttribute('value')
```

<Badge type="success">After: Cypress</Badge>

```js
// Click on the element
cy.get('button').click()

// Send keys to the element (usually an input)
cy.get('input').type('my text')

// Clear the text in an element (usually an input)
cy.get('input').clear()

// Get the attribute of an element
// Example: Get the value of an input
cy.get('input').its('value')
```

You can learn more about [interacting with DOM elements in our official documentation](/guides/core-concepts/interacting-with-elements.html).

## Assertions

Similar to Protractor, Cypress enables use of human readable assertions.

<Badge type="danger">Before: Protractor</Badge>

```js
describe('verify elements on a page', () => {
  it('verifies that a link is visible', () => {
    expect($('a.submit-link').isDisplayed()).toBe(true)
  })
})
```

<Badge type="success">After: Cypress</Badge>

```js
describe('verify elements on a page', () => {
  it('verifies that a link is visible', () => {
    cy.get('a.submit-link').should('be.visible')
  })
})
```

But Cypress has one additional feature that can make a critical difference in the reliability of your tests' assertions: [retry-ability](https://docs.cypress.io/guides/core-concepts/retry-ability). When your test fails an assertion or command, Cypress will mimic a real user with build-in wait times and multiple attempts at asserting your tests in order to minimize the amount of false negatives / positives.

In the example above, if the submit link does not appear on the page at the exact moment when Protractor runs the test (which can be due to any number of factors including API calls, slow browser rendering, etc.), your test will fail. However, Cypress factors these conditions into its assertions and will only fail if the time goes beyond a reasonable amount.

You can learn more about how Cypress handles [assertions in our official documentation](/guides/references/assertions).

## WebDriver Control Flow vs Cypress

Protractor's WebDriverJS API is based on promises, which is managed by a control flow. This [Control Flow](https://www.protractortest.org/#/control-flow) enables you to write asynchronous Protractor tests in a synchronous style.

```js
// Click on the element
// This code looks synchronous!
element(by.css('button')).click()

// Send keys to the element (usually an input)
element(by.css('input')).sendKeys('my text')
```

Protractor's Control Flow can be disabled, allowing you to write your test cases as asynchronous functions.

```js
// Wait for the button to be found and click it
await element(by.css('button')).click()

// Wait for the input to be found and type into the field
await element(by.css('input')).sendKeys('my text')
```

Cypress commands are similar at first glance. Cypress commands are [not invoked immediately](/guides/core-concepts/introduction-to-cypress#Commands-Are-Asynchronous) and are enqueued to run at a later time. Cypress might look like promises, but the [Cypress API is not an exact implementation of Promises](/guides/core-concepts/introduction-to-cypress#Commands-Are-Not-Promises). The Control Flow example rewritten as a Cypress test would look something like this:

```js
// Click on the element
cy.get('button').click()

// Send keys to the element (usually an input)
cy.get('input').type('my text')
```

## Automatic Retrying and Waiting

Web applications are usually rarely synchronous. With Protractor, you may be accustomed to adding arbitrary timeouts or using the [waitForAngular](https://www.protractortest.org/#/api?view=ProtractorBrowser.prototype.waitForAngular) API to wait for Angular to finish rendering before attempting to interact with an element. With Cypress, commands that query the DOM are [automatically retried](/guides/core-concepts/retry-ability). Cypress will automatically wait and retry most commands until an element appears in the DOM. If an element is not [actionable](/guides/core-concepts/interacting-with-elements#Actionability) within the [`defaultCommandTimeout`](/guides/core-concepts/retry-ability#Timeouts) setting, the command will fail. This enables you to write tests without the need for arbitrary timeouts, enabling you to write more predictable tests.

<Badge type="danger">Before: Protractor</Badge>

```js
// Clicking a button
element(by.css('button')).click()
// Waiting for Angular to re-render the page
browser.waitForAngular()
// Make assertion after waiting for Angular to update
expect(by.css('.list-item').getText()).toEqual('my text')
```

<Badge type="success">After: Cypress</Badge>

```js
// Clicking a button
cy.get('button').click()
// Make assertion. No waiting necessary!
cy.get('.list-item').contains('my text')
```

## Network Spies

Protractor doesn't offer a built-in solution for network spying. With Cypress, you can leverage the [intercept API](/api/commands/intercept) to spy on and manage the behavior of any network request.

For example, if you wanted to wait on a network request to complete before continuing your test, you could write the following:

```js
cy.intercept('/users/**')
```

Cypress will automatically wait for any request to `/users/**` to complete before continuing your test.

## Stubbing

Cypress's [intercept API](/api/commands/intercept) also allows you to stub any network request for your app under test. You can use the [intercept API](/api/commands/intercept) to make assertions based on different simulated responses for your network requests. For example, you might want to simulate a 3rd-party API outage by forcing a network error and test your app under those conditions. With Cypress's [intercept API](/api/commands/intercept), this and more is possible!

```js
cy.intercept('GET', '/my-api', { forceNetworkError: true })
```

You can also use the intercept API to stub a custom response for specific network requests:

```js
// requests to '/update' will be fulfilled with a body of 'success'
cy.intercept('/update', 'success')
```

For more information, check out the [intercept API documentation](/api/commands/intercept).

## Navigating Websites

When you want to visit a page, you can do so with the following code:

<Badge type="danger">Before: Protractor</Badge>

```js
it('visits a page', () => {
  browser.get('/about')
  browser.navigate().forward()
  browser.navigate().back()
})
```

<Badge type="success">After: Cypress</Badge>

```js
it('visits a page', () => {
  cy.visit('/about')
  cy.go('forward')
  cy.go('back')
})
```

However, Protractor assumes that all websites you want to visit are Angular apps. As a result, you have to take an extra step to disable this behavior. When you write Cypress tests though, you don't need to do any extra work!

<Badge type="danger">Before: Protractor</Badge>

```js
it('visit a non-Angular page', () => {
  browser.waitForAngularEnabled(false)
  browser.get('/about')
})
```

<Badge type="success">After: Cypress</Badge>

```js
it('visit a non-Angular page', () => {
  cy.visit('/about')
})
```

For more information, check out our [official documentation on navigation](https://example.cypress.io/commands/navigation)!

## Using Page Objects

A common pattern when writing end-to-end tests, especially with Protractor, is [Page Objects](https://github.com/SeleniumHQ/selenium/wiki/PageObjects). Page Objects can simplify your test code by creating reusable methods if you find yourself writing the same test code across multiple test cases.

### Protractor without Page Objects

```js
// Type into username field
element(by.css('.username')).sendKeys('my username')
// Type into password field
element(by.css('.password')).sendKeys('my password')
// Click the login button
element(by.css('button')).click()
```

### Protractor with Page Objects

```js
const page = {
  login: () => {
    element(by.css('.username')).sendKeys('my username')
    element(by.css('.password')).sendKeys('my password')
    element(by.css('button')).click()
  },
}

it('should display the username of a logged in user', () => {
  page.login()
  expect(by.css('.user').getText()).toEqual('my username')
})
```

You can use the same Page Object pattern within your Cypress tests:

### Cypress without Page Objects

```js
cy.get('.username').type('my username')
cy.get('.password').type('my password')
cy.get('button').click()
```

### Cypress with Page Objects

```js
const page = {
  login: () => {
    cy.get('.username').type('my username')
    cy.get('.password').type('my password')
    cy.get('button').click()
  },
}

it('should display the username of a logged in user', () => {
  page.login()
  cy.get('.username').contains('my username')
})
```

Cypress also provides a [Custom Command](/api/cypress-api/custom-commands) API to enable you to add methods to the `cy` global directly:

```js
Cypress.Commands.add('login', (username, password) => {
  cy.get('.username').type(username)
  cy.get('.password').type(password)
})
```

You can use your own custom commands in any of your tests:

```js
it('should display the username of a logged in user', () => {
  cy.login('Matt', Cypress.env('password'))
  cy.get('.username').contains('Matt')
})
```

## Debugging Tests

In Protractor, per [the official docs](https://github.com/angular/protractor/blob/master/docs/debugging.md#disabled-control-flow), the process for debugging your tests interactively involves a few steps:

1. Add `debugger` keyword to the test case that you want to debug

<Badge type="danger">Before: Protractor</Badge>

```js
describe('example test suite', () => {
  it('contains an error we need to debug', () => {
    browser.get('/login')
    debugger
    element(by.css('#password-field')).sendKeys('testPassword1234')
  })
})
```

2. Set the inspector agent with a breakpoint flag and a config file

3. Use Chrome's DevTools devices to locate the correct target

With Cypress however, because your tests are available through the browser dashboard, you can debug with DevTools without any additional configuration. Rather than rely solely on the `debugger` keyword, Cypress allows you to debug specific stages of your test by chaining the `debug()` command!

<Badge type="success">After: Cypress</Badge>

```js
describe('example test suite', () => {
  it('contains an error we need to debug', () => {
    cy.visit('/login')
    cy.get('#password-field')).debug().sendKeys('testPassword1234')
  })
})
```

Cypress can also be run in [headed mode](TODO) which displays the browser running your tests. By default, when running `cypress open`, the [Cypress Test Runner](/guides/core-concepts/test-runner) provides an interactive debugging experience while also displaying the application under test. The Cypress Test Runner can also be ran in [headless mode](/guides/guides/command-line#cypress-run-headless) which will force the browser to be hidden. This is especially useful when you want to run your Cypress tests as part of your Continuous Integration (CI) pipeline.

For more information, check out our [official documentation on debugging](/guides/guides/debugging#Using-debugger)!

## Continuous Integration

Cypress makes it easy to [run your tests in your Continuous Integration environment](/guides/continuous-integration/introduction).

Check out our guides to run your Cypress tests in a [GitHub Action](/guides/continuous-integration/github-actions), [CircleCI](/guides/continuous-integration/introduction#CircleCI), [GitLab CI](/guides/continuous-integration/gitlab-ci), [Bitbucket Pipeline](/guides/continuous-integration/bitbucket-pipelines), or [AWS CodeBuild](/guides/continuous-integration/aws-codebuild).

## Parallelization

One of the worst things that can happen to a developer is to be forced to wait for a 2 hour end-to-end test suite to before verifying something works or not. Instead, the [Cypress Dashboard Service](/guides/dashboard/introduction) allows your tests to run in parallel. If your longest test only test a minute to run, this means that you've just cut down your testing by over 12,000%!

This feature is available in Protractor and requires you to configure your application with multiple options (i.e., `sharedTestFiles`, `maxInstances`, etc.). An example is provided below:

<Badge type="danger">Before: Protractor</Badge>

```js
// Example from https://developers.perfectomobile.com/display/PD/Protractor+parallel+execution
// An example configuration file.
exports.config = {
  seleniumAddress:
    'https://cloudName.perfectomobile.com/nexperience/perfectomobile/wd/hub',

  // Capabilities to be passed to the webdriver instance.
  capabilities: {
    browserName: 'chrome',

    // allows different specs to run in parallel.
    // If this is set to be true, specs will be sharded by file
    // (i.e. all files to be run by this set of capabilities will run in parallel).
    // Default is false.
    sharedTestFiles: true,

    // Maximum number of browser instances that can run in parallel for this
    // set of capabilities. This is only needed if shardTestFiles is true.
    // Default is 1.
    maxInstances: 2,

    // Cloud capabilities
    user: 'user@perfectomobile.com',
    password: 'password',

    // Device capabilities
    platformName: 'Android',
    manufacturer: 'Samsung',
    model: 'Galaxy S5',
  },

  // Framework to use. Jasmine is recommended.
  framework: 'jasmine',

  // Spec patterns are relative to the current working directly when
  // protractor is called.
  specs: ['spec.js', 'spec2.js'],

  // Options to be passed to Jasmine.
  jasmineNodeOpts: {
    showColors: true,
    defaultTimeoutInterval: 30000,
  },
}
```

However, with Cypress, all you need to do is pass the `--parallel` and `--record` flag to `cypress run`, and it will take care of the rest for you:

<Badge type="success">After: Cypress</Badge>

```bash
cypress run --record --parallel
```

For more information, check out our [official docs on parallelization](https://docs.cypress.io/guides/guides/parallelization#Overview)!

## Test Retries

End-to-end tests can be complicated because modern web applications are also complex. You may find that some features of your web application are challenging to test or the tests sporadically fail. We call these tests "flaky." Cypress allows you to [retry failed tests](/guides/guides/test-retries). Sometimes tests will fail in a CI environment when they otherwise would pass on a developer's machine. Enabling test retries in your Cypress configuration can help you to get unblocked when unpredictable, flaky tests are occasionally failing.

The Cypress Dashboard goes a step further and helps you and your team to [detect flaky tests](/guides/dashboard/flaky-test-management) that run in your CI/CD pipeline.

## Next Steps

For more information on how to create end-to-end tests with Cypress, be sure to check out [our official documentation here](/guides/overview/why-cypress.html).

If you see any inaccuracies with this or feel something has been misrepresented, please [submit an issue here](https://github.com/cypress-io/cypress-documentation/issues/new).
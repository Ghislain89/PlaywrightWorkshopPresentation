### Advanced Usage
- Network Monitoring & Manipulation
- Reusing authentication sessions
- Page objects
- Fixtures

----

### Network Monitoring & Manipulation

----
### Waiting for Requests

```ts []
// Start waiting for request before clicking. Note no await.
const requestPromise = page.waitForRequest('https://example.com/resource');
await page.getByText('trigger request').click();
const request = await requestPromise;

// Alternative way with a predicate. Note no await.
const requestPromise = page.waitForRequest(request =>
  request.url() === 'https://example.com' && request.method() === 'GET',
);
await page.getByText('trigger request').click();
const request = await requestPromise;
```

----
### Waiting for Responses

```ts []
// Start waiting for response before clicking. Note no await.
const responsePromise = page.waitForResponse('https://example.com/resource');
await page.getByText('trigger response').click();
const response = await responsePromise;

// Alternative way with a predicate. Note no await.
const responsePromise = page.waitForResponse(response =>
  response.url() === 'https://example.com' && response.status() === 200
);
await page.getByText('trigger response').click();
const response = await responsePromise;
```

----
### Mocking Responses

```ts []
await page.route('**/api/fetch_data', route => route.fulfill({
  status: 200, // Set an explicit response code
  body: testData, // Put whatever data you need here!
}));
await page.goto('https://example.com');
```

----
### Modifying Responses
```ts []
test('gets the json from api and adds a new fruit', async ({ page }) => {
  // Get the response and add to it
  await page.route('*/**/api/v1/fruits', async route => {
    const response = await route.fetch();
    const json = await response.json();
    json.push({ name: 'Playwright', id: 100 });
    // Fulfill using the original response, while patching the response body
    // with the given JSON object.
    await route.fulfill({  status: 200, response, json });
  });

  // Go to the page
  await page.goto('https://demo.playwright.dev/api-mocking');

  // Assert that the new fruit is visible
  await expect(page.getByText('Playwright', { exact: true })).toBeVisible();
});
```

----
#### Assignment 2
Attempting to create a user that already exists results in a HTTP Statuscode _409_ on the register endpoint
* Duplicate the testcase you created earlier
* Apply response modification to trigger a 409 and a toasters that shows the user already exists.
* Rerun your other testcases, do they still work?

> Especially for toasters, web first assertions are your friend!

----
### Reusing authentication sessions

----
#### Storing Authentication State
```ts []
//auth.setup.ts
import { test as setup, expect } from '@playwright/test';

const authFile = 'playwright/.auth/user.json';

setup('authenticate', async ({ page }) => {
  await page.goto('https://github.com/login');
  await page.getByLabel('Username or email address').fill('username');
  await page.getByLabel('Password').fill('password');
  await page.getByRole('button', { name: 'Sign in' }).click();
  await page.waitForURL('https://github.com/');
  await expect(page.getByRole('button', { name: 'View profile and more' })).toBeVisible();
  await page.context().storageState({ path: authFile });
});
```

----
#### Using Authentication State
```ts []
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  projects: [
    // Setup project
    { name: 'setup', testMatch: /.*\.setup\.ts/ },

    {
      name: 'chromium',
      use: {
        ...devices['Desktop Chrome'],
        // Use prepared auth state.
        storageState: 'playwright/.auth/user.json',
      },
      dependencies: ['setup'],
    },
  ],
});
```
----
#### Assignment 3

* Write a setup testcase that stores authentication state
* Update the testcase you created earlier to make use of this authenticated state
* Run your testcase again!

----
### Page Objects (React Example)
* Page Object Classes describing interactions for _each_ component
* Page Object Classes describing interactions for _each_ page, typically uses 1 or more components
* Tests only use interactions/functions and do not reference elements directly.

* _KISS_

Let's look at an example!

----
### Fixtures
* Fixtures can contain just about anything you want
  * Testdata
  * Helpers
  * Page Objects!
* Fixtures are scoped to the consuming testcase.
* The _page_ we've been seeing in many slides, is one of Playwrights built in fixtures. 

----
### Page Objects without fixtures
```ts []
test.describe('todo tests', () => {
  let todoPage;

  test.beforeEach(async ({ page }) => {
    todoPage = new TodoPage(page);
    await todoPage.goto();
    await todoPage.addToDo('item1');
    await todoPage.addToDo('item2');
  });
  test('should add an item', async () => {
    await todoPage.addToDo('my item');
    // ...
  });
});
```

----
### Page Objects with fixtures
```ts []
test('should add an item', async ({ todoPage }) => {
  await todoPage.addToDo('my item');
  // ...
});

test('should remove an item', async ({ todoPage }) => {
  await todoPage.remove('item1');
  // ...
});
```
----
#### Assignment 4

* Refactor your testcase to use the page objects I defined. 
* Try to use Fixtures!
* BONUS: Expand the testcase to also add a Todo and assert that your todo was succesfully created.

*hint*: Some preperation has already been done

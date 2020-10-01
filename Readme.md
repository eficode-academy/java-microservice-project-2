# Microservice Project Part 2

This is a continuation, find [part 1 here](https://github.com/thedrlambda/java-microservices-project).

## Continuing from part 1
Add my advert service to your microservice system from last time.

## Continuous deployment

### Step 1: Automated integration
Protect `master` from direct pushing.

On push deploy to a branch compile, if no errors merge into `master`.

### Step 2: Automated deploy

Add running fast tests to branch pipeline.

On push to `master` deploy to Heroku.

### Step 3: Safer deploy

Modify the pipeline: On push to `master` run slow tests, if no errors merge into `release`.

On push to `release` deploy to Heroku.

## Testing

Perform a manual system test for the `fetch-product-page` event. The result of the event should be (at least) a `display` event containing `SESSION_ID,advert,PRODUCT_ID,PRODUCT_NAME`.

Write an automated end-to-end test for my service. Given a `fetch-product-page` event it should send a `display` event containing `SESSION_ID,advert,PRODUCT_ID,PRODUCT_NAME`.

If the end-to-end test failed, then we need to figure out if it is our code or our data. Write an automated component test for my service. Mocking out everything external.

If the component test failed, our code is the issue. Write automated unit tests for each public function to figure out what is broken.

If the component test passed, the data is the issue. Write an automated integration test for my service.

If the integration test failed, the issue might be the 3rd party service. Write an automated contract test for the 3rd party service.

## Feature toggles

Create an Optimizely.

Create a feature flag for the fix.

Fix the code under the feature flag.

Deploy it.

Turn it on for one user. Perform the manual system test.

Turn it on for all users. Perform the manual system test.

Turn it off again. Perform the manual system test.

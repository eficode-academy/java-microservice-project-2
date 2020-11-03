# Microservice Project Part 2

This is a continuation, find [part 1 here](https://github.com/eficode-academy/java-microservices-project).

## Continuing from part 1
We need to add my advert service to your microservice system from last time.

Go to https://github.com/eficode-academy/adify-service and fork the repo.

Clone it to your local machine.

You can now run it locally like we did in part 1.

## Continuous deployment

### Step 0: Compile

Generate a personal access token on github with access to public repos. Put the token into a repo secret in your forked repo. 

Create a workflow file `.github/workflows/continuous-integration.yml`. With a name of the workflow:

```
name: Continuous Integration
```

We want to specify that on push to a ready branch:

```
on:
  push:
    branches:
      - "ready/**"
```

In order to build the code, first check it out, then compile:

```
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
          token: ${{secrets.[Your secret name]}}

      - name: Compile
        run: mvn install
```

### Step 1: Automated integration

Extend the workflow from step 0 such that if there are no errors merge the branch into `master`. You can use these commands to merge into a branch, `master` in this case:

```
git checkout ${GITHUB_REF#refs/heads/}
git rebase origin/master
git branch master
git push origin master:master
git push origin :${GITHUB_REF#refs/heads/}
```

From now on push only to branches. Use this command to trigger integration:

```
git push origin [BRANCH]:ready/[BRANCH]
```

### Step 2: Safer integration

Add running fast tests to branch pipeline.

```
mvn test -DskipTests=false -Dgroups=fast
```

### Step 3: Automated deploy

Create a second workflow, where on push to `master` deploy to Heroku. See this for instructions: https://github.com/marketplace/actions/deploy-to-heroku

### Step 4: Safer deploy

Modify the second workflow, so on push to `master` run slow tests, if no errors merge into `release`.

```
mvn test -DskipTests=false -Dgroups=slow
```

Create a third workflow, where on push to `release` deploy to Heroku.

## Testing

Perform a manual system test for the `fetch-product-page` event. The result of the event should be (at least) a `display` event containing `SESSION_ID,advert,PRODUCT_ID,PRODUCT_NAME`.

In the following remember to tag tests as `slow` if they call anything external.

Write an automated end-to-end test for my service. Given a `fetch-product-page` event it should send a `display` event containing `SESSION_ID,advert,PRODUCT_ID,PRODUCT_NAME`. 

If the end-to-end test failed, then we need to figure out if it is our code or our data. Write an automated component test for my service. Mocking out everything external.

If the component test failed, our code is the issue. Write automated unit tests for each public function to figure out what is broken.

If the component test passed, the data is the issue. Write an automated integration test for my service.

If the integration test failed, the issue might be the 3rd party service. Write an automated contract test for the 3rd party service.

## Feature toggles

Referring this tutorial: https://docs.developers.optimizely.com/full-stack/docs/java.

Create an Optimizely.

Add an environment variable to Heroku with the optimizely PROD sdk_key.

Create a feature flag for the fix.

Fix the code under the feature flag.

Deploy it.

Turn it on for one user. Perform the manual system test.

Turn it on for all users. Perform the manual system test.

Turn it off again. Perform the manual system test.

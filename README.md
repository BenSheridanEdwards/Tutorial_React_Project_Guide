# Tutorial_React_Project_Guide
How to build a React application with Jest, Enzyme, CircleCI, Code Climate &amp; Firebase

## Introduction & running create-react-app

In this tutorial, I'm going to walk through how to setup a React project from create-react-app with testing using Jest and Enzyme, install check-props and check-prop-types as dependencies so we can test props, then create continuous integration and deployment with CircleCI and Firebase. In my circleci configuation, I'll be adding in some Code Climate setup so we can add badass badges to every project.

First things first, initialize a git repo in an empty folder and run create-react-app with your desired project name: 

```
$ git init
```

```
$ npx create-react-app project_name
```

![Terminal Screenshot - Running create-react-app]()

Once create-react-app has finished doing it's thing, lets do some folder cleanup (Optional).

I like to just drag my react project up one level to my root repo folder, delete the now empty React project folder, and add my own README to my project.

![Project Screenshot - Completed create-react-app project]()

## Setting up testing with jest and enzyme

Now that's done, lets install enzyme and the adapters needed to make it work with jest, as jest is already installed with create-react-app:

```
npm install --save-dev jest jest-enzyme enzyme enzyme-adapter-react-16
```

Next lets setup our `test.js` file. First we'll import our Enzyme shallow wrapper as well as our Enzyme Adapter. Then we'll write a one-line configuration to setup Enzyme with our new adapter. 

![test.js file]()
*Before and After*

You might encounter an issue with the latest version of jest not being compatable with react-scripts, like the one I encountered below: 

![Setup Error - Jest]()

Don't worry, just follow the steps below this error to fix the issue. You'll have to delete your `package-lock.json`, delete jest as a dependency from your `package.json` file, and delete your `node_modules`. Then run `$ npm install`. This will resolve the issue. 

Alternitively, you can create an .env file in your project and add the line `SKIP_PREFLIGHT_CHECK=true` which ignores the warning and your jest remains at the latest version.

Next, lets delete the render test and replace it with an empty boilerplate render test. 

![test.js boilerplate]()

Running `$ npm test` at this point should give us one passing test. 

![boilerplate passing test]()

That's it for setting up React with Jest and Enzyme! 

## CircleCI

Setting up CircleCi is easy once you know how. 

First lets create a new folder in our project root folder called '.circleci', then create a file inside called 'config.yml'. These are special names so be sure to copy them. 

![empty config.yml file inside .circleci folder]()

Now lets add build scripts to our config.yml file:

```
orbs:
  react: thefrontside/react@0.2.0
  code-climate-quality: freighthub/code-climate-quality@0.0.4
version: 2.1
jobs:
  build:
    docker:
      - image: circleci/node:10.16.0
    working_directory: ~/repo
    steps:
      - checkout
      - restore_cache:
            keys:
              - v1-dependencies-{{ checksum "package.json" }}
              - v1-dependencies-
      - run:
            name: Install Dependencies
            command: npm install
      - save_cache:
            key: v1-npm-deps-{{ checksum "package-lock.json" }}
            paths:
              - ./node_modules
      - run:
            name: Build Project
            command: npm run-script build
      - run:
            name: Run Tests
            command: npm run-script test
      # - run:
      #       name: Setup Code Climate test-reporter
      #       command: |
      #         curl -L https://codeclimate.com/downloads/test-reporter/test-reporter-latest-linux-amd64 > ./cc-test-reporter
      #         chmod +x ./cc-test-reporter
      # - run:
      #       name: Build, Save and Send Coverage Report to Code Climate
      #       command: |
      #         ./cc-test-reporter before-build
      #         npm run test:coverage
      #         ./cc-test-reporter after-build --debug -t lcov --exit-code $?
      # - run:           
      #       name: Deploy to Firebase Hosting
      #       command: './node_modules/.bin/firebase deploy --token=$FIREBASE_TOKEN'
      #       description: Run tests with coverage and upload results to code climate          
```

Don't worry about the commented out scripts for Code Climate and Firebase, they're for later to make everything work together seemlessly. 

Now is a good time to commit and push up to GitHub, nothing will happen, yet. So for now head over to [CircleCi.com](https://circleci.com/) and create an account or login with Github.

First before we add our project we want to build, head over to your settings, then security, and enable third-party orbs, we'll need to use them later to work with Code Climate and Firebase. 

Next, add your desired project by clicking on 'add project' on the left-hand sidebar and clicking 'Setup Project' next to the repo name. 

![Add project on circleci]()

![Set up project on circleci]()

You'll reach this page: 

![circleci project setup]()

Click 'start building' then 'add manually' then 'start building' again

Your build should look like this: 

![CircleCI Project First Build]()

Next, we'll setup code climate. 

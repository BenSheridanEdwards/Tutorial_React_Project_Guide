# Tutorial - How to setup a React project with create-react-app, Circle CI, Code Climate, and Firebase. 

[![CircleCI](https://circleci.com/gh/BenSheridanEdwards/Tutorial_React_Project_Guide.svg?style=svg)](https://circleci.com/gh/BenSheridanEdwards/Tutorial_React_Project_Guide)
[![Maintainability](https://api.codeclimate.com/v1/badges/87096c1417a643cd3efd/maintainability)](https://codeclimate.com/github/BenSheridanEdwards/Tutorial_React_Project_Guide/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/87096c1417a643cd3efd/test_coverage)](https://codeclimate.com/github/BenSheridanEdwards/Tutorial_React_Project_Guide/test_coverage)

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

# Code Climate 

Next we'll setup Code Climate, so circleci sends test coverage reports for our project. 

First, lets head over to [CodeClimate.com](https://codeclimate.com/) and sign up using GitHub to their quality product if you haven't already. You'll be redirected to your dashboard, that will look like this:

![Code Climate Dashboard]()

Click on 'Open Source', then 'Add a repository', then 'Add repo' next to your desired project. Your project will immediately start building and you should see a success alert like this: 

![Successful First Code Climate Build]()

Click `See the results`, and you should see a similar page to this:

![Code Climate Build Report]()

Click on the umbrella underneath 'Test Coverage', it'll bring you to a page to help you setup your coverage. Scroll down till you find your test reporter ID, copy it, we'll need it for our next step. 

With your test reporter ID copied to your clipboard, head back over to [CircleCi.com](https://circleci.com/) and go into your desire projects overview.

Click on the cog icon next to your project. This will lead you to your settings for the project: 

![Project settings on CircleCi]()

Under build settings, click on 'Environmental Variables'. Then 'Add Variable', a pop up like this should appear: 

![Add environmental variable on CircleCI]()

In the value input box, paste your test reporter ID, and name the variable 'CC_TEST_REPORTER_ID'. After it's copied perfectly, click add variable and you'll see your new environment variable listed like so:

![Added test reporter id as environment variable]()

Now head back over to your project, and uncomment the scripts 'Setup Code Climate test-reporter' and 'Build, Save and Send Coverage Report to Code Climate': 

![Uncomment code climate scripts]()

Lastly head over to your package.json, there's one final thing we need to do. Between your test and eject scripts, add the following line, followed by a comma: 

```
"test:coverage": "set CI=true && react-scripts test --coverage",
```

and to the bottom of your package.json file:

```
 "jest": {
    "collectCoverageFrom": [
      "**/*.{js,jsx}",
      "!**/node_modules/**",
      "!**/coverage/**",
      "!**/serviceWorker.js",
      "!**/index.js"
    ]
  }
```

Your package.json should now look like: 

![Add test:coverage script and jest ignore coverage]()

Now commit and push to GitHub, and head over to your repo on circleci.com to see the magic happen.

![Code Climate integration successful build]()

Success! We're now sending test coverage reports to Code Climate, head back over to your repo on CodeClimate to check how we did: 

![Test Coverage on CodeClimate]()

A big fat F for test coverage, that sounds about right considering we have one empty test! That's a problem for future you to solve. 

Now lets complete this tutorial with some deployment to FireBase! 

## Firebase

First lets head over to Firebase.com, sign up or login with GitHub and when you're redirected to your project console, add your desired project. Easy stuff. 

![Add firebase project]()

You'll then be asked to name your project:

![Name firebase project]()

Then you can choose to add google analystics to your project, this is optional either way, I choose yet in case I want to see how my app is doing in the future. 

![Google Analytics Option]()

After a few seconds of waiting, your project will be ready:

![Firebase project ready]()

Next, head to your terminal inside your react project, you need to run two very similar commands, but trust me, all will be explained. 

First, install firebase-tools globally, allowing you to use firebase commands throughout your local machine:

```
npm install -g firebase-tools
```

Next is a similar command that has to be run with every project you create:

```
npm install -D firebase-tools
```

This installs firebase-tools locally as a dev dependency — but also means you install the dependencies on the build server, and this ensures you can execute the firebase command from circleci.

Before we initialize firebase, we need to create a build for our project, so run:

```
npm run-script build
```

![Successful build]()

With that done, head on over to your terminal inside of your desired project directory, and run:

```
firebase init
```

This is what you'll see: 

![Firebase in terminal]()

Use your arrow keys to select Hosting and click your space bar to highlight this like so:

![Firebase Hosting Highlighted]()

Hit enter, and then enter again to 'Use an existing project`:

![Firebase use existing project]()

Next, select your desired project: 

![Firebase select project]()

and hit enter. You'll be asked three questions for this point, the answers to which i'll show below:

```
? What do you want to use as your public directory? build
? Configure as a single-page app (rewrite all urls to /index.html)? N
✔  Wrote build/404.html
? File build/index.html already exists. Overwrite? N
```

After entering these answers you'll see: 

![Firebase initialization complete]()

Now to deploy, run: 

```
firebase deploy
```

You should see this in the terminal: 

![Deploy complete]()

And if you navigate to your hosting url, you should see: 

![Deployed App]()

And last but not least, lets integrate this all with CircleCI. 

In your terminal, run:

```
firebase login:ci
```

![firebase authentication terminal]()

This will launch a browser window, asking you to login and authenticate the use of firebase:

![browser authenticate firebase]()

Upon success, you'll be given a Firebase token: 

![successful authentication firebase token]()

Copy your token to your clipboard, and lets head over to your project's settings in circleci. This will be another environment variable like the one we did previously for Code Climate. 

Click to add a new environment variable and paste your token into the value box. Name your environment variable 'FIREBASE_TOKEN'. 

![Add firebase token as environment variable]()

Click add variable and you should see your token next to your Code Climate test coverage reporter ID: 

![Project environment variables]()

Now that's done, head back over to your project for the final step.

Inside your circle ci folder, bring up your config.yml file and head to the bottom to where our firebase deploy script lives. Uncomment the line:

![uncomment firebase deploy]()

Now commit and push everything to GitHub and lets head over to our project build in circle ci to see it all come together: 

![success build full integration]()

Success! Everything is integrated! Now you have a React project complete with testing, coverage reporting, continuous integration, and continuous deployment. What you create with this foundation is up to you! 

# Status Badges (Optional)

The next step is optional, but one I love to do after doing all this setup, lets quickly add some status badges!

The first badge is our build badge, head over to your project's settings on CircleCI.com:

![CircleCI project settings]()

Under notifications on the left-hand side bar you'll find 'Status Badges', click on it:

![circleci build badge]()

Copy the markdown code to your clipboard and head to your README, where to paste it is up to you, but I like it at the very top, underneath my project's title:

![Insert build badge inside README]()

Next, we're going to our maintainability and test coverage badges, so head over to your project's setting on CodeClimate.com:

![Code Climate project settings]()

Under extras on the left-hand side bar you'll find 'Badges', click on it:

![Code Climate badges]()

Click the markdown tab for each, and copy both links over to your README, right below your circleci build badge:

![Insert maintainability and coverage badges in README]()

Now commit and push, and head to GitHub to see the fruit of our labours: 

![Badges on README]()

And that's it, now it's time to get building something amazing!

Happy coding!

Ben
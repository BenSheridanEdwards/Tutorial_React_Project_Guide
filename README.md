# React Project Guide (Tutorial) - with Create React App, Jest & Enzyme, Circle CI, Code Climate, and Firebase

[![CircleCI](https://circleci.com/gh/BenSheridanEdwards/Tutorial_React_Project_Guide.svg?style=svg)](https://circleci.com/gh/BenSheridanEdwards/Tutorial_React_Project_Guide)
[![Maintainability](https://api.codeclimate.com/v1/badges/87096c1417a643cd3efd/maintainability)](https://codeclimate.com/github/BenSheridanEdwards/Tutorial_React_Project_Guide/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/87096c1417a643cd3efd/test_coverage)](https://codeclimate.com/github/BenSheridanEdwards/Tutorial_React_Project_Guide/test_coverage)

How to setup a React project with create-react-app, test with code coverage using Jest and Enzyme, and integrate Circle CI, Code Climate, and Firebase.

### [Testing with Jest & Enzyme](#testing)
1. [Installing Dependancies](#1-installing-our-dependancies) <br>
2. [Setting up your App.test.js file](#2-setting-up-your-app.test.js-file) <br>
3. [100% Test Coverage from the start (Optional)](#3-coverage-from-your-first-build) <br>
4. [Common Jest Error when running npm test](#4-common-jest-error) <br>
5. [Tests Passing](#5-running-our-tests) <br>

### [CircleCI](#circleci-header)
1. [Sign up to Circleci with GitHub and add your repo](#1-sign-up-for-circleci) <br>
2. [Enable third-party orbs in your project settings](#2-enable-third-party-orbs) <br>
3. [Create your configuation file inside a new .circleci folder](#3-create-config-file) <br>
4. [Copy & Paste Build Scripts](#4-copy-paste-build-scripts) <br>
5. [First Successful Build](#5-first-successful-build) <br>

### [Code Climate](#code-climate)
### [Firebase](#firebase)
### [Putting it all together](#together)
### [Fun Extra: Status Badges](#badges)

## Introduction & running create-react-app

In this tutorial, I'm going to walk through how to setup a React project from create-react-app with testing using Jest and Enzyme, install check-props and check-prop-types as dependencies so we can test props, then create continuous integration and deployment with CircleCI and Firebase. In my circleci configuation, I'll be adding in some Code Climate setup so we can add badass badges to every project.

First things first, initialize a git repo in an empty folder and run create-react-app with your desired project name: 

```
$ git init
```

```
$ npx create-react-app project_name
```

![Terminal Screenshot - Running create-react-app](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/ReactAppSetup/Tutorial-GitInit.png)

Once create-react-app has finished doing it's thing, lets do some folder cleanup (Optional).

I like to just drag my react project up one level to my root repo folder, delete the now empty React project folder, and add my own README to my project.

![Project Screenshot - Completed create-react-app project](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/ReactAppSetup/Tutorial-DeleteCreateReactAppFolder.png)

Next lets head over to our package.json. Between your test and eject scripts, add the following line, followed by a comma: 

```
"test:coverage": "set CI=true && react-scripts test --coverage",
```

and to the bottom of your package.json file, add:

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

Your package.json should now look like this: 

![Add test:coverage script and jest ignore coverage](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-AddScripts-Coverage%26IgnoreCoverage.png)

## <a name="testing">Setting up testing with jest and enzyme</a>

### <a name="1-installing-our-dependancies">1. Installing our dependancies</a>

Now that's done, lets install enzyme and the adapters needed to make it work with jest, which is already installed with create-react-app by default:

```
npm install --save-dev jest jest-enzyme enzyme enzyme-adapter-react-16
```

### <a name="2-setting-up-your-app.test.js-file">2. Setting up your App.test.js file</a>

Next lets setup our `App.test.js` file. We'll import our Enzyme shallow wrapper as well as our Enzyme Adapter. We'll write a one-line configuration to setup Enzyme with our new adapter. If you clear the test contents it should still pass, but your coverage will be 0%:

*In App.test.js, add:*
```
import React from 'react';
import Enzyme, { shallow } from 'enzyme';
import EnzymeAdapter from 'enzyme-adapter-react-16'

import App from './App';

Enzyme.configure({ adapter: new EnzymeAdapter() })
```

### <a name="3-coverage-from-your-first-build">3. 100% Test coverage from the first build</a>
<a name="3-coverage-from-your-first-build"></a>

It's optional but if you want 100% test coverage, you can do a simple render test on your App component. To do so just copy the code below into the respective App.js and App.test.js files: 

*In App.test.js, add:*
```
import React from 'react';
import Enzyme, { shallow } from 'enzyme';
import EnzymeAdapter from 'enzyme-adapter-react-16'

import App from './App';

Enzyme.configure({ adapter: new EnzymeAdapter() })

const setup = (props={}, state=null) => {
  const wrapper = shallow(<App {...props} />)
  if (state) wrapper.setState(state)
    return wrapper;
}

const findByTestAttr = (wrapper, val) => {
  return wrapper.find(`[data-test="${val}"]`)
}

it('renders without error', () =>{
  const wrapper = setup();
  const appComponent = findByTestAttr(wrapper, 'component-app');
  expect(appComponent.length).toBe(1);
});
```

It should now look like this: 

![App.test.js full render test]()

*and in App.js, insert `data-test="component-app"` into your top level <div> element*
  
![App.js add data-test attribute]()

![test.js file after](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Testing/Tutorial-TestFileSetup.png)
*After*

### <a name="4-common-jest-error">4. Common Jest Error</a>

You might encounter an issue with the latest version of jest not being compatable with react-scripts, like the one I encountered below: 

![Setup Error - Jest](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Testing/Tutorial-NpmTestInitialError.png)

Don't worry, here are the steps to solve the issue:
1. Delete your `package-lock.json` file and your `node_modules` folder. 
2. Delete jest as a dependency from your `package.json` file.
3. Then run `$ npm install`. This will resolve the issue. 

Alternitively, you could quickly create .env file in the root folder of your project and add the line `SKIP_PREFLIGHT_CHECK=true`, which will tell your project you to ignore warnings of this kind in the future.

### <a name="5-running-our-tests">5. Running our Tests</a>

Running `$ npm test` at this point should give us one passing test. 

That's it for setting up React with Jest and Enzyme! 

## <a name="circleci-header">CircleCI</a>

### <a name="1-create-config-file">1. Create your configuation file inside a new .circleci folder</a>

First lets create a new folder in our project root folder called '.circleci', then create a file inside that called 'config.yml'. These are special names so be sure to copy them exactly. 

![empty config.yml file inside .circleci folder](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CreateCircleCIConfigFile.png)

### <a name="2-copy-paste-build-scripts">2. Copy & Paste Build Scripts</a>

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
      #       name: Test Coverage and send Coverage Report to Code Climate
      #       command: |
      #         ./cc-test-reporter before-build
      #         npm run test:coverage
      #         ./cc-test-reporter after-build --debug -t lcov --exit-code $?
      # - run:           
      #       name: Deploy to Firebase Hosting
      #       command: './node_modules/.bin/firebase deploy --token=$FIREBASE_TOKEN'
      #       description: Run tests with coverage and upload results to code climate          
```

![Pasted Config.yml file](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CircleCIConfigFile-RETAKE.png)

Don't worry about the commented out scripts for the test coverage reporting and deployment, we'll be uncommenting them later to make everything work together seemlessly with Code Climate and Firebase respectively. 

Now commit and push to GitHub. Next we'll setup our project with CircleCi.

### <a name="3-sign-up-for-circleci">3. Sign up to Circleci with GitHub and add your project</a>

Next, head over to [Circleci.com](https://circleci.com/) and login or create an account with GitHub, then add your project repo:

![Add project on circleci](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-AddNewProject.png)

![Gif - Sign up to CircleCI, Set up Project]()

You'll reach this page: 

![circleci project setup](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CircleCi-SetupProjectConfig.png)

Click 'start building' then 'add manually' then 'start building' again, this will trigger an initial build.

### <a name="4-first-successful-build">4. First Successful Build</a>

Your first build should trigger automatically, and should look like this: 

![CircleCI Project First Build](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-FirstBuildOverview.png)

All green bliss, now one last step before we move onto Code Climate. 

### <a name="5-enable-third-party-orbs">5. Enable third-party orbs in your project settings</a>

Head over to your project settings, opt-in to third-party orbs.

![Enable Third-Party Orbs](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-EnableOrbs.png)

We'll use orbs later to enable CircleCi to work with Code Climate and Firebase seamlessly.

## <a name="code-climate">Code Climate</a>

Next we'll setup Code Climate, so circleci sends test coverage reports for our project. 

First, lets head over to [CodeClimate.com](https://codeclimate.com/) and sign up using GitHub to their quality product if you haven't already. You'll be redirected to your dashboard, that will look like this:

![Code Climate Dashboard](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-CodeClimateFirstPage.png)

Click on 'Open Source', then 'Add a repository', then 'Add repo' next to your desired project. Your project will immediately start building and you should see a success alert like this: 

![Successful First Code Climate Build](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-FirstBuildSuccessMessage.png)

Click `See the results`, and you should see a similar page to this:

![Code Climate Build Report](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-CodeClimateSetup.png)

Click on the umbrella underneath 'Test Coverage', it'll bring you to a page to help you setup your coverage. Scroll down till you find your test reporter ID, copy it, we'll need it for our next step. 

![](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-Find-CodeClimateProjectSettings.png)

With your test reporter ID copied to your clipboard, head back over to [CircleCi.com](https://circleci.com/) and go into your desire projects overview.

Click on the cog icon next to your project. This will lead you to your settings for the project: 

![Project settings on CircleCi](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CircleCI-ProjectSettings.png)

Under build settings, click on 'Environmental Variables'. Then 'Add Variable', a pop up like this should appear: 

![Add environmental variable on CircleCI](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CircleCi-AddEnvVariable.png)

In the value input box, paste your test reporter ID, and name the variable 'CC_TEST_REPORTER_ID'. After it's copied perfectly, click add variable and you'll see your new environment variable listed like so:

![Added test reporter id as environment variable](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-AddedTestReporterIDEnvVariable.png)

Now head back over to your project, and uncomment the scripts 'Setup Code Climate test-reporter' and 'Build, Save and Send Coverage Report to Code Climate': 

![Uncomment code climate scripts](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-UncommentCodeClimate.png)

Now commit and push to GitHub, and head over to your repo on circleci.com to see the magic happen.

![Code Climate integration successful build](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-SuccessfulBuildwithSentTestCoverageReport.png)

Success! We're now sending test coverage reports to Code Climate, head back over to your repo on CodeClimate to check how we did: 

![Test Coverage on CodeClimate](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-CodeClimateBuild-WithTestCoverage.png)

100% Test coverage, perfect! 

Now lets complete this tutorial with some deployment to FireBase! 

## Firebase

First lets head over to Firebase.com, sign up or login with GitHub and when you're redirected to your project console, add your desired project. Easy stuff. 

![Add firebase project](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-Firebase-ProjectSetup-Start.png)

You'll then be asked to name your project:

![Name firebase project](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-Firebase-ProjectSetup-Naming.png)

Then you can choose to add google analystics to your project, this is optional either way, I choose yet in case I want to see how my app is doing in the future. 

![Google Analytics Option](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-AddProject-ConfigAnalytics.png)

After a few seconds of waiting, your project will be ready:

![Firebase project ready](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-Firebase-AddProject-Done.png)

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

![Successful build](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/ReactAppSetup/Tutorial-NPM-Build.png)

With that done, head on over to your terminal inside of your desired project directory, and run:

```
firebase init
```

Use your arrow keys to select Hosting and click your space bar to highlight:

![Firebase in terminal](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseInitialize.png)

Hit enter, and then enter again to 'Use an existing project`:

![Firebase use existing project](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseProjectSetup.png)

Next, select your desired project: 

![Firebase select project](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseChooseProject.png)

and hit enter. You'll be asked three questions for this point, the answers to which i'll show below:

```
? What do you want to use as your public directory? build
? Configure as a single-page app (rewrite all urls to /index.html)? N
✔  Wrote build/404.html
? File build/index.html already exists. Overwrite? N
```

After entering these answers you'll see: 

![Firebase initialization complete](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseHostingSetup.png)

Now to deploy, run: 

```
firebase deploy
```

You should see this in the terminal: 

![Deploy complete](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseDeployFromCommandLine.png)

And if you navigate to your hosting url, you should see: 

![Deployed App](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-SuccessfulCommandLineDeploy.png)

And last but not least, lets integrate this all with CircleCI. 

In your terminal, run:

```
firebase login:ci
```

![firebase authentication terminal](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseWaitingForAuth.png)

This will launch a browser window, asking you to login and authenticate the use of firebase:

![browser authenticate firebase](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-FirebaseWaitingForAuth.png)

Upon success, you'll be given a Firebase token: 

![successful authentication firebase token](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/Firebase/Tutorial-ObtainFirebaseTokenFromCommandLine.png)

Copy your token to your clipboard, and lets head over to your project's settings in circleci. This will be another environment variable like the one we did previously for Code Climate. 

Click to add a new environment variable and paste your token into the value box. Name your environment variable 'FIREBASE_TOKEN'. 

![Add firebase token as environment variable](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-AddFirebaseTokentoCircleCi.png)

Click add variable and you should see your token next to your Code Climate test coverage reporter ID: 

![Project environment variables](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-BothEnvironmentalVariablesAdded.png)

Now that's done, head back over to your project for the final step.

Inside your circle ci folder, bring up your config.yml file and head to the bottom to where our firebase deploy script lives. Uncomment the line:

![uncomment firebase deploy](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-UncommentFirebaseScript.png)

Now commit and push everything to GitHub and lets head over to our project build in circle ci to see it all come together: 

![success build full integration]()

Success! Everything is integrated! Now you have a React project complete with testing, coverage reporting, continuous integration, and continuous deployment. What you create with this foundation is up to you! 

# Status Badges (Optional)

The next step is optional, but one I love to do after doing all this setup, lets quickly add some status badges!

The first badge is our build badge, head over to your project's settings on CircleCI.com:

![CircleCI project settings](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CircleCI/Tutorial-CircleCI-ProjectSettings.png)

Under notifications on the left-hand side bar you'll find 'Status Badges', click on it:

![circleci build badge](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/StatusBadges/Tutorial-CopyCircleCIBuildStatusBadge.png)

Copy the markdown code to your clipboard and head to your README, where to paste it is up to you, but I like it at the very top, underneath my project's title:

![Insert build badge inside README](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/StatusBadges/Tutorial-AddCircleCIBuildStatusBadge.png)

Next, we're going to our maintainability and test coverage badges, so head over to your project's setting on CodeClimate.com:

![Code Climate project settings](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/CodeClimate/Tutorial-Find-CodeClimateProjectSettings.png)

Under extras on the left-hand side bar you'll find 'Badges', click on it:

![Code Climate badges](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/StatusBadges/Tutorial-CodeClimateStatusBadges.png)

Click the markdown tab for each, and copy both links over to your README, right below your circleci build badge:

![Insert maintainability and coverage badges in README](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/StatusBadges/Tutorial-AddStatusBadges.png)

Now commit and push, and head to GitHub to see the fruit of our labours: 

![Badges on README](https://github.com/BenSheridanEdwards/Tutorial_React_Project_Guide/blob/master/images/StatusBadges/Tutorial-StatusBadgesImplemented.png)



And that's it, now it's time to get building something amazing!

Happy coding!

Ben

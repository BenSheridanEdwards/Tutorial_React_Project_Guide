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

You might encounter an issue with jest not being the latest version locally, like the one I encountered below: 

![Setup Error - Jest]()

Don't worry, just follow the steps below this error to fix the issue. You'll have to delete your `package-lock.json`, delete jest as a dependency from your `package.json` file, and delete your `node_modules`. Then run `$ npm install`. This will resolve the issue. 

Next, lets delete the render test and replace it with an empty boilerplate render test. 

![test.js boilerplate]()

Running `$ npm test` at this point should give us one passing test. 

![boilerplate passing test]()

That's it for setting up React with Jest and Enzyme! 

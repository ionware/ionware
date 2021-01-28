# Static checker or analysis.
No doubt, writing automated tests improves code quality and assurance. But as a developer, we want to catch errors as quickly as possible and ensure that they never even slip into our codebase —especially when it's a project having collaborators.

You want to ensure a ~~maniac~~ dev doesn't intentionally (or by mistake) push a lousy commit that chokes your CI or enforces a style guide strictly followed by all developers on the project (even you) —whatever it is you are doing, the goal of static analysis is to quickly catch an error and make sure that everyone is on the same page.

Here's how I often find myself setting up this process, from project to project (saving myself the time of looking up the docs).

## Code formatting and prettifying.

First thing to do is to install prettier as a development dependency.
```npm
npm i -D prettier
```
Set an NPM script to format all sort of files, simply add this to your `package.json` file, in the `scripts` segment and ensure the glob is appropriate for your file structure. (Add all your file exts)
```json
"format": "prettier --write \"src/**/*.+(js|jsx|json|yml|yaml|md|css|scss)\""
```
Of course, you can call the command any other name aside `format`, ~~who cares? (just you)~~. Now you can just run ```npm run format``` to run prettier through an matching file specified by the glob pattern.
<br><br>
Now, the prettier configuration file. Head over to [Prettier Playground](https://prettier.io/playground) to generate your config or use [mine](.prettierrc), then create a file at the root of your project (where your `package.json` file resides) called __`.prettierrc`__ and paste your config in there. You are set!
<br><br>
**TIP**: You can install prettier extension on vscode or Jetbrains IDE (PHPStorm and Webstore) to allow formatting on the fly (cool stuff).

## Linting with ESLint
> A life saver tool this one is!

Install `eslint` and its prettier config (so it won't conflict with eslint).
```
npm i -D eslint eslint-config-prettier
```
Create the configuration file (JSON type) named `.eslintrc.json`
```
touch .eslintrc.json
```
Copy and paste this in the config file.
```json
{
  "extends": ["eslint:recommended", "prettier"],
  "parserOptions": {
    "ecmaVersion": 2020,
    "sourceType": "module"
  },
  "env": {
    "browser": true,
    "node": true
  }
}
```

Finally, add a linting command to your NPM scripts. In the scripts portion of your `package.json`, add this:
```json
"lint": "eslint --fix \"src/**/*.+(js|jsx)\""
```


## Style guide with Airbnb
Let's ensure we are all on the same page with Airbnb style guide. [Learn more about it](https://github.com/airbnb/javascript).
__First, install the packages.__<br>
### If you are using `React` in your project:
```
npm i -D eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react
```
And your `.eslintrc.json` file becomes
```json
{
  "extends": ["airbnb", "prettier"],
  "parserOptions": {
    "ecmaVersion": 2020,
    "ecmaFeatures": {
      "jsx": true
    },
    "sourceType": "module"
  },
  "env": {
    "browser": true,
    "node": true
  }
}
```

### If you are **NOT** using `React` in your project:
```
npm i -D eslint-config-airbnb-base eslint-plugin-import
```
And your `.eslintrc.json` file becomes
```json
{
  "extends": ["airbnb-base", "prettier"],
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": false
    },
    "ecmaVersion": 2020,
    "sourceType": "module"
  },
  "env": {
    "node": true,
    "browser": false
  }
}
```

Wow! That is that.

## Catch it! Bob smells stinky code.
The whole stuffs we have done above, we will put everything in a pipeline here, to ensure formatting and linting is run before a commit is made.
<br><br>
Add this to your `package.json` file:
```json
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
}
```
We are telling Husky to run lint-staged before any commit happens. Now, let's setup lint-staged. <br>

Add this line to your `package.json` file:
```json
"lint-staged": {
  "src/**/*.+(js|jsx|json|yml|yaml|md)": [
    "prettier --write"
  ],
  "src/**/*.+(js|jsx)": [
    "eslint --fix --quiet"
  ]
}
```


And finally, install `husky` and `lint-staged`.
```
npm i -D husky lint-staged
```
We are using Lint-staged to ensure that only the files that are being staged are run through our code quality tools (eslint and prettier), not the entire matching files in the codebase. You don't want to type `git commit`, then have to mix a coffe (probably finish it) before you are able to write your commit message.

<p>If you feel like warming up your PC, you can set a script to run your linter over your entire codebase.


That's IT!
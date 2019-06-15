---
layout: article
title: Testing a React app with Jasmine npm
---

# Testing a React app with Jasmine npm

The [Jasmine NPM](/setup/nodejs.html) package was originally designed just to run tests against your Node.js code, but with a couple of other packages, you can get it to run your react specs as well. This tutorial assumes you're using [babel](https://www.npmjs.com/package/babel) to compile your code and [enzyme](https://www.npmjs.com/package/enzyme) to test it. We'll also be using [jsdom](https://www.npmjs.com/package/jsdom) to provide a fake HTML DOM for the tests.

Install these packages if you haven't already:

```
npm install --save-dev babel-cli \
                       @babel/register \
                       babel-preset-react-app \
                       enzyme \
                       enzyme-adapter-react-16 \
                       jasmine-enzyme \
                       jsdom \
                       jasmine
```

The first thing we'll do is make a helper to register babel into the `require` chain. Run `jasmine init` if you haven't already done so. Then make a new file in the `spec/helpers` directory, we'll call it `babel.js`:

```javascript
require('@babel/register');
```

Or, if using TypeScript:

```
require('@babel/register')({
    "extensions": [".js", ".jsx", ".ts", ".tsx"]
});

```


Then we'll want to make sure that we have enzyme loaded up, so make another file in `spec/helpers`, we'll call this one `enzyme.js`:

```javascript
import jasmineEnzyme from 'jasmine-enzyme';
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });

beforeEach(function() {
  jasmineEnzyme();
});
```

And finally, `jsdom.js` in `spec/helpers`:

```javascript
import {JSDOM} from 'jsdom';

const dom = new JSDOM('<html><body></body></html>');
global.document = dom.window.document;
global.window = dom.window;
global.navigator = dom.window.navigator;
```

In order to ensure these files are loaded first, we'll edit the `jasmine.json`. The default location is in `spec/support`. We want these new helpers to be loaded before any other helpers, so we modify it like so:

```javascript
"helpers": [
  "helpers/babel.js",
  "helpers/enzyme.js",
  "helpers/jsdom.js",
  "helpers/**/*.js"
],
```

It's common for React code to import CSS or image files. Normally those imports are resolved at build time but they'll produce errors when the tests are run in Node. To fix that, we add one more package:

```
npm install --save-dev ignore-styles
```

And put the following code in `spec/helpers/exclude.js`.

```javascript
import 'ignore-styles';
```

Finally, we need to tell Babel what flavor of Javascript we want by adding the following to `package.json`:

```json
  "babel": {
    "presets": [
     "react-app"
    ]
  }
```


You're all set. [Write your specs](/tutorials/your_first_suite.html) and run them with the `jasmine` command.

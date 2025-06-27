Create a new project with latest Expo: npx create-expo-app@latest my-app

Or use the latest template: npx create-expo-app@latest my-app --template blank

i used latest blank template
Expo version: 53.0.13

npx expo install react-dom
npx expo install react-native-web

Then the Babel plugin for optimization:
npx expo install babel-plugin-react-native-web

Install @expo/metro-runtime@~5.0.4 by running:
npx expo install @expo/metro-runtime

npx expo install react-native-dotenv
puts it in production dependencies
moved to dev dependency

after setting up a simple .env file and test setup in App.js
I got an error that said
ERROR index.js: api is not defined

I fixed it by removing the api.cache call in babel.config.js

I then got an error saying I needed babel preset-flow plugin:

```
Web Bundling failed 1448ms index.js (3 modules)
 ERROR  SyntaxError: C:\Users\shahm\Documents\GitHub\react-native-dotenv-example\react-native-dotenv-example\node_modules\expo\src\Expo.ts: Support for the experimental syntax 'flow' isn't currently enabled (23:8):

  21 | } from 'expo-modules-core';
  22 |
> 23 | export type {
     |        ^
  24 |   SharedRef as SharedRefType,
  25 |   EventEmitter as EventEmitterType,
  26 |   NativeModule as NativeModuleType,

Add @babel/preset-flow (https://github.com/babel/babel/tree/main/packages/babel-preset-flow) to the
'presets' section of your Babel config to enable transformation.
If you want to leave it as-is, add @babel/plugin-syntax-flow (https://github.com/babel/babel/tree/ma

If you already added the plugin for this syntax to your config, it's possible that your config isn't being loaded.
You can re-run Babel with the BABEL_SHOW_CONFIG_FOR environment variable to show the loaded configuration:
        npx cross-env BABEL_SHOW_CONFIG_FOR=C:\Users\shahm\Documents\GitHub\react-native-dotenv-example\react-native-dotenv-example\node_modules\expo\src\Expo.ts <your build command>
See https://babeljs.io/docs/configuration#print-effective-configs for more info.

```

I then got this error:

```
Web Bundling failed 2491ms index.js (14 modules)
 ERROR  SyntaxError: C:\Users\shahm\Documents\GitHub\react-native-dotenv-example\react-native-dotenv-example\node_modules\expo\src\hooks\useEvent.ts: Missing semicolon. (10:12)

   8 |  */
   9 | type InferEventName<TEventsMap> =
> 10 |   TEventsMap extends Record<infer TEventName extends keyof TEventsMap, AnyEventListener>
     |             ^
  11 |     ? TEventName
  12 |     : never;
  13 |


```

I think I am missing @babel/preset-flow:
npx expo install @babel/preset-flow

to get the errors to stop I had to install all of the following:
npx expo install @babel/preset-typescript
npx expo install @babel/preset-env
npx expo install @babel/preset-react

npx expo install @babel/preset-flow @babel/preset-typescript @babel/preset-env @babel/preset-react

I then had a blank page when I ran my app
To fix this I had to import 'react' into my App.js at the start of the file

```
import React from 'react';
```

My final working babel.config.js file was:

```
module.exports = function(api) {
  api.cache(false);
  return {
    presets: [
      ['@babel/preset-env', { targets: { node: 'current' } }],
      '@babel/preset-react',
      '@babel/preset-typescript',
      '@babel/preset-flow'
    ],
    plugins: [
      ['module:react-native-dotenv']
    ]
  };
};

```

# Babel 6 & Webpack

- Notes on Babel 6 and Webpack. 

## Sources

- [ES6 Cheatsheet](http://jamesknelson.com/wp-content/uploads/2015/09/es6-cheatsheet.pdf)
- [Promises Cheatsheet](http://jamesknelson.com/wp-content/uploads/2015/09/promises-cheatsheet.pdf)
- [Webpack Made Simple: Building ES6 & LESS with autorefresh](http://jamesknelson.com/webpack-made-simple-build-es6-less-with-autorefresh-in-26-lines/)


## Babel 6 NPM packages

- `babel-cli`: babel cli interface
- `babel-core`: node api and require hook
- `babel-polyfill`: full es2015 environment
- `babel-preset-es2015`: es2015 plugun collection
- `babel-preset-stage-0`: es7-ish features
- `babel-plugin-transform-runtime`: 
    - externalize references to helpers and builtins, automatically polyfilling your code without polluting globals
    - avoids repeated inclusion of Babel’s helper functions
    - package must be installed via npm, and then `babel-runtime` package must be required in code
- `babel-runtime`: babel self contained runtime
- `babel-loader`: webpack plugin for bable


## Babel with Webpack

### Example applications

- [webpack-black-triangle](https://github.com/jamesknelson/webpack-black-triangle)
- [unicorn-standard starter-kit](https://github.com/unicorn-standard/starter-kit)

## Setting up Webpack

Start with an empty folder. Run `npm init` to create an empty `package.json` file before starting:

1.) Install build-time packages and plugins:

    # Babel core and webpack loader plugin
    npm install babel-core babel-loader --save-dev 
    
    #ES6/ES2015 plugin collection
    npm install babel-preset-es2015 --save-dev 
    
    #ES7 plugin collection
    npm install babel-preset-stage-0 --save-dev

3.) Add support for ES6 polyfills

- certain ES6 features must be polyfilled by adding helper code to the project
- ok to use if app doesn't share JavaScript environment with other apps

`npm install babel-polyfill --save`


- helper code is injected into single files, which can result in repeated code for a larger multi-file project
- to prevent repeated code, use `transform-runtime`

```
npm install babel-runtime --save
npm install babel-plugin-transform-runtime --save-dev
```

4.) Configuring Webpack With Babel Loader

- main config file is `webpack.config.js`

    - normal JS file, evaluated by Node
    - do normal Node things like require modules and check environment variables
    - webpack's config is placed in whatever is assigned to `module.exports`
    - example `webpack.config.js`:
        - `entry`:
            - array of files which will be run at startup
            - run Babel’s polyfill first, before running main JavaScript file
        - `output`:
            - tells webpacke-dev-server where to serve compiled files from
        - `devtool`:
            - enable source maps for development
        - `module.loaders`:
            - list webpack loaders that javascript is passed through to transform source
            - for example, below: `.js` files in the `src` directory are being passed to the "babel-loader", using the es2015 plugin to transform code to ES5
        - `debug`:
            - don't make release ready code
    - in summary:
        - `entry` points are where self contained scripts go
        - `module.loaders` is where you put things that transform your code

    ```javascript

    var path = require('path');
    var webpack = require('webpack');

    module.exports = {
        entry: [
          'babel-polyfill', // babel's polyfills
          './src/main', // main application scripts
        ],
        output: {
          publicPath: '/',
          filename: 'main.js'
        },
        devtool: 'source-map',
        module: {
          loaders: [
            {
              loader: "babel-loader",
              include: [
                path.resolve(__dirname, "src"),
              ],
              test: /\.js$/, // only run on .js files
              query: {
                plugins: ['transform-runtime'], 
                presets: ['es2015', 'stage-0'],
              }
            }
          ]
        },
        debug: true
    }

    ```

## Webpack Tweaks

### Reload page on save
    
- add `webpack-dev-server/client?http://localhost:8080` to the end of the `entry` section of `webpack.config.js`

### Dev Server Options

- instead of passing config options to dev server like `webpack-dev-server --content-base src src/main.js`, add the following object to `webpack.config.js`:

```
devServer: {
    contentBase: "./src"
}
```

- run the dev server from cli:

`node_modules/webpack-dev-server/bin/webpack-dev-server.js`



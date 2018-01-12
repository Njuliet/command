
## node v8.9.4 (LTS: long time supper)

```
$ nvm install v8.9.4
```
## create  `package.json` for rag.js
```
$ npm init -y
```

## install node package

install dev package.
```
$ npm install --save-dev babel-core babel-eslint babel-jest babel-loader babel-plugin-module-resolver babel-plugin-syntax-dynamic-import babel-plugin-transform-class-properties babel-plugin-transform-decorators-legacy babel-plugin-transform-object-rest-spread babel-plugin-transform-regenerator babel-polyfill babel-preset-env babel-preset-react brotli-webpack-plugin chunk-manifest-webpack-plugin copy-webpack-plugin cross-env  css-loader cssnano cssnano-preset-default enzyme eslint eslint-config-airbnb eslint-import-resolver-webpack eslint-plugin-babel eslint-plugin-compat eslint-plugin-import eslint-plugin-jest eslint-plugin-jsx-a11y eslint-plugin-react extract-text-webpack-plugin file-loader graphql-tag html-webpack-plugin
$ npm install --save-dev iltorb image-webpack-loader jest less less-loader node-sass node-zopfli npm-run-all postcss-cssnext postcss-loader postcss-nested progress-bar-webpack-plugin react-test-renderer resolve-url-loader rimraf sass-loader serve style-loader webpack webpack-bundle-analyzer webpack-chunk-hash webpack-config webpack-dev-server webpack-manifest-plugin webpack-node-externals zopfli-webpack-plugin
```

install package
```
$ npm install --save antd apollo-local-query apollo-server-koa boxen chalk graphql history ip isomorphic-fetch kcors koa koa-bodyparser koa-helmet koa-router koa-send koa-sslify microseconds prop-types react react-apollo react-dom react-helmet react-hot-loader react-redux react-router react-router-dom redux redux-thunk regenerator-runtime seamless-immutable 
```



## Dircent 

```
├── LICENSE
├── README.md
├── client            // client port 5005
├── common            // common function.
├── config            // configure for webpack so on .
├── package.json
├── public            // public for html js css files.
└── server            // server port 5000
```

---
title: 'Configuration'
order: 6
---

## Table of Contents

- [devServer](#devserver)
- [proxy](#proxy)
- [outputMapper](#outputmapper)
- [clean](#clean)
- [apps](#apps)
- [templateFilename](#templatefilename)
- [generateTemplate](#generatetemplate)
- [mergeWebpackConfig](#mergewebpackconfig)
- [asyncSuffix](#asyncsuffix)
- [copyToPublicIgnore](#copytopublicignore)
- [workspaces](#workspaces)
- [uglifyOptions](#uglifyoptions)

Place a `union.config.js` file in the root of your project if you want to configure react-union-scripts.

Configuration file can export either:

- a static JSON object or
- a function.

If you pass a function, it will be called with an object describing the CLI arguments passed to a script:

```js
// example of dynamic union.config.js
module.exports = ({
	target, // custom value
	script, // build, start or test
	app,
	debug,
	proxy,
	verbose,
	noHmr,
	analyze,
}) => ({
	outputMapper: target === 'liferay' ? { js: 'widgets/js' } : {},
});
```

Resulting configuration can redefine the following properties.

## **devServer**

`devServer.port` : _number_

Port on which the development server listens for requests. Defaults to `3300`.

`devServer.historyApiFallback` : _boolean_

If `true`, add [connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback) middleware. Defaults to `true`.

## **proxy**

`proxy.port` : _number_

Port on which the proxy server listens for requests. Defaults to `3300`.

**Note:** This is the port that you want to visit in your browser.

`proxy.target` : _string_

URL to "wrap" with the proxy server. This should the URL that you're running the CMS or portal instance on.

`proxy.publicPath` : _string_

Public path of the application. See [webpack](https://github.com/webpack/docs/wiki/configuration#outputpublicpath). Required if you want to run proxy.

## **outputMapper**

Output mapper makes it possible to further customize the folder structure that is produced by the build. All paths are relative to the `apps[].paths.build` directory.

`outputMapper.js` : _string_

Path of JavaScript assets. Defaults to `static/js`.

`outputMapper.media` : _string_

Path of media assets. Defaults to `static/media`.

## **clean**

`outputMapper.paths` : _string_

Paths to clean before build. By default equals to `[paths.build]`

`outputMapper.options` : _object_

See [clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin)

## **apps**

Array of configurations for your applications.
Every configuration is merged with above properties. You can rewrite them separately for every application.

For example in the following configuration:

```js
module.exports = {
	proxy: { port: 3333 },
	apps: [
		{
			name: 'MyFirstApp',
			proxy: { port: 5000 },
		},
		{ name: 'MySecondApp' },
	],
};
```

MyFirstApp will use `proxy.port: 5000` and MySecondApp will use `3333`.

`apps[].name` : _string_

Name of your application that is used for:

- finding HTML template in `./public` directory and
- naming your bundle file.

Required.

`apps[].paths.build` : _string_

Path to the build directory. Defaults to `<project root>/build/<app name>`.

`apps[].paths.public` : _string_

Path to public directory. The directory should contain:

- static assets to copy to the build directory
- a HTML template that is named according to `templateFilename` property.

Defaults to `<project root>/public/<app name>`.

`apps[].paths.index` : _string_

Path to the entry file of the application. Defaults to `<project root>/apps/<app name>`.

## **templateFilename**

`templateFilename` : _string_

Name of the HTML template. Defaults to `index.ejs`.

## **generateTemplate**

`generateTemplate` : _boolean_

If true, generates template using html-webpack-plugin. Defaults to `true`.

## **mergeWebpackConfig**

`mergeWebpackConfig` : _function_

If specified, `webpack.config` generated by `react-union-scripts` is passed as the argument. Function must return new valid webpack config.

## **copyToPublicIgnore**

`copyToPublicIgnore` : _RegExp_

Pattern for files that should not be copied from `public` folder in the build process. Defaults to `/\.ejs$/`.

## **workspaces**

Workspaces can rewrite default patterns for monorepo matching.

`workspaces.widgetPattern` : _string, array[string], RegExp_

Pattern for the widget packages. Defaults to `union-widget`.

`workspaces.appPattern` : _string, array[string], RegExp_

Pattern for the app packages. Defaults to `union-app`.

## **uglifyOptions**

Options for configuration of the underlying minifier engine (currently [uglify-es](https://www.npmjs.com/package/uglify-es)).

Supported options:

- [`cache`](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/#cache)
- [`parallel`](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/#parallel)
- [`sourceMap`](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/#sourcemap)

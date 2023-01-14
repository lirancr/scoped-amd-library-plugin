[![Run tests](https://github.com/lirancr/scoped-amd-library-plugin/actions/workflows/test.yml/badge.svg)](https://github.com/lirancr/scoped-amd-library-plugin/actions/workflows/test.yml)
[![NPM Version](https://badge.fury.io/js/scoped-amd-library-plugin.svg?style=flat)](https://www.npmjs.com/package/scoped-amd-library-plugin)

# Scoped AMD Library Webpack Plugin

This is a fork of Webpack's [AmdLibraryPlugin](https://github.com/webpack/webpack/blob/main/lib/library/AmdLibraryPlugin.js)

The intent is to safely and reliably shadow global scope arguments from Webpack's own runtime and use user provided external
scope dependency instead.

# Usage

1. Set `library.type` configuration to `scoped-amd`.
2. Set the `externalsType` configuration to `amd`.
3. Add plugin to plugins list and pass the scope dependency name which will be provided by your amd loader.
    ```javascript
    new ScopedAmdLibraryPlugin({ scopeDependencyName: 'myScope' })
    ```
4. Use the [ProvidePlugin](https://webpack.js.org/plugins/provide-plugin/) point any global namespaces your code access (and you wish to scope) to your scope dependency.
5. If your scope is not provided from a npm library but rather directly by you at runtime, declare your scope dependency as external by adding it to the `externals` array.
   Otherwise webpack will look it up in your `node_modules` directory and fail

Following steps 1-5, your configuration file should end up looking something like this:

**webpack.config.js**

```javascript
const { ProvidePlugin } = require('webpack')
const { ScopedAmdLibraryPlugin } = require('scoped-amd-library-plugin')

const scopeDependencyName = 'myScope'

module.exports = {
	output: {
		library: {
			type: 'scoped-amd', // step 1
		},
	},
	externalsType: 'amd', // step 2
	plugins: [
		new ScopedAmdLibraryPlugin({ scopeDependencyName }), // step 3
		new ProvidePlugin({
			// step 4
			window: scopeDependencyName,
			document: [scopeDependencyName, 'document'],
			fetch: [scopeDependencyName, 'fetch'],
		}),
	],
	externals: [
		{
			[scopeDependencyName]: scopeDependencyName, // optional step 5
		},
	],
}
```

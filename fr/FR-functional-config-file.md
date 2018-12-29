# Allow `vue.config.js` to export a function

## What problem does this feature solve?

Currently `vue.config.js` only allowed to export an object containing options.

When user want to have more complicated configuration, they have to do:

```js
const baseConfig = {
  // basic configurations
}

if (something) {
  module.exports = {
    ...baseConfig,
    // specific configurations
    baseUrl: '/foo/'
  }
} else {
  module.exports = {
    ...baseConfig,
    // specific configurations
    baseUrl: '/bar/'
  }
}
```

Moreover, we have received several issues asking about how to build with different configurations. (Like this one: https://github.com/vuejs/vue-cli/issues/2861) Although this can be done with current api, I thinks it's not intutive enough so that people with limited nodejs knowledge couldn't figure it out.

## What does the proposed API look like?

Like [what `babel.config.js` did](https://babeljs.io/docs/en/config-files#root-babelconfigjs-files), let user use function to generate configuration:

```js
module.exports = function () {
  const baseConfig = {
    // basic configurations
  }
  if (something) {
    return {
      ...baseConfig,
      // specific configurations
      baseUrl: '/foo/'
    }
  } else {
    return {
      ...baseConfig,
      // specific configurations
      baseUrl: '/foo/'
    }
  }
}
```

Further more, we can pass some useful information/api as arguments into the configuration function, for example:

```js
module.exports = function (options, api) {
  const {
    args, // command line arguments parsed with cli
    env // project default ENV
    // any other ones ?
  } = options

  // like chainWebpack and configureWebpack inside PluginAPI
  api.chainWebpack(fn)
  api.configureWebpack(config)

  // this could solve the problem met in:
  // https://github.com/vuejs/vue-cli/issues/2506
  // https://github.com/vuejs/vue-cli/pull/3135
  api.loadEnvDir(pathToDir)
  api.loadEnvFile(pathToFile)
  // user can use ENV variables below this function
}
```

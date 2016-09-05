在你开发Ember应用的过程中，你可能会碰到一些常见的Ember本身没有提到的场景，如身份验证或使用SASS作为样式格式 Ember CLI 提供了一个通用的形式叫做 [Ember 插件](#toc_addons) 来发布解决这些问题的可重用的库。 此外，您可能还希望使用一些像 CSS 框架这样的前端依赖或 Javascript 日期选择插件这样并不是特定于Ember应用的组件。 Ember CLI 通过标准的 [Bower 包管理器](#toc_bower)来安装这些软件.

## 插件

Ember插件的安装可以通过[Ember CLI](http://ember-cli.com/extending/#developing-addons-and-blueprints)(例如：`ember install ember-cli-sass`)。 插件可能会通过自动修改项目的 `bower.json` 文件引入其他依赖项。

你可以在[Ember Observer](http://emberobserver.com)找到所有的插件.

## Bower

Ember CLI使用[bower](http://bower.io)作为前端的依赖管理器，使你能更容易更新你的前端依赖项到最新版本。 Bower的配置文件`bower.json`位于项目的根目录，里面列出来你项目的所有依赖。 在命令行中执行`bower install`将会安装记录在`bower.json`里的前端依赖。

Ember CLI会扫描`bower.json`文件的改动，因此，当你通过`bower install <dependencies> --save`安装了新的依赖时，它会自动刷新你的应用。.

## Other assets

Third-party JavaScript not available as an addon or Bower package should be placed in the `vendor/` folder in your project.

Your own assets (such as `robots.txt`, `favicon`, custom fonts, etc) should be placed in the `public/` folder in your project.

## Compiling Assets

When you're using dependencies that are not included in an addon, you will have to instruct Ember CLI to include your assets in the build. This is done using the asset manifest file `ember-cli-build.js`. You should only try to import assets located in the `bower_components` and `vendor` folders.

### Globals provided by Javascript assets

The globals provided by some assets (like `moment` in the below example) can be used in your application without the need to `import` them. Provide the asset path as the first and only argument.

```ember-cli-build.js app.import('bower_components/moment/moment.js');

    <br />You will need to add `"moment": true` to the `predef` section in `.jshintrc` to prevent JSHint errors
    about using an undefined variable.
    
    ### AMD Javascript modules
    
    Provide the asset path as the first argument, and the list of modules and exports as the second.
    
    ```ember-cli-build.js
    app.import('bower_components/ic-ajax/dist/named-amd/main.js', {
      exports: {
        'ic-ajax': [
          'default',
          'defineFixture',
          'lookupFixture',
          'raw',
          'request'
        ]
      }
    });
    

You can now `import` them in your app. (e.g. `import { raw as icAjaxRaw } from 'ic-ajax';`)

### Environment Specific Assets

If you need to use different assets in different environments, specify an object as the first parameter. That object's key should be the environment name, and the value should be the asset to use in that environment.

```ember-cli-build.js app.import({ development: 'bower_components/ember/ember.js', production: 'bower_components/ember/ember.prod.js' });

    <br />If you need to import an asset in only one environment you can wrap `app.import` in an `if` statement.
    For assets needed during testing, you should also use the `{type: 'test'}` option to make sure they
    are available in test mode.
    
    ```ember-cli-build.js
    if (app.env === 'development') {
      // Only import when in development mode
      app.import('vendor/ember-renderspeed/ember-renderspeed.js');
    }
    if (app.env === 'test') {
      // Only import in test mode and place in test-support.js
      app.import(app.bowerDirectory + '/sinonjs/sinon.js', { type: 'test' });
      app.import(app.bowerDirectory + '/sinon-qunit/lib/sinon-qunit.js', { type: 'test' });
    }
    

### CSS

Provide the asset path as the first argument:

```ember-cli-build.js app.import('bower_components/foundation/css/foundation.css');

    <br />All style assets added this way will be concatenated and output as `/assets/vendor.css`.
    
    ### Other Assets
    
    All assets located in the `public/` folder will be copied as is to the final output directory, `dist/`.
    
    For example, a `favicon` located at `public/images/favicon.ico` will be copied to `dist/images/favicon.ico`.
    
    All third-party assets, included either manually in `vendor/` or via a package manager like Bower, must be added via `import()`.
    
    Third-party assets that are not added via `import()` will not be present in the final build.
    
    By default, `import`ed assets will be copied to `dist/` as they are, with the existing directory structure maintained.
    
    ```ember-cli-build.js
    app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf');
    

This example would create the font file in `dist/font-awesome/fonts/fontawesome-webfont.ttf`.

You can also optionally tell `import()` to place the file at a different path. The following example will copy the file to `dist/assets/fontawesome-webfont.ttf`.

```ember-cli-build.js app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf', { destDir: 'assets' });

    <br />If you need to load certain dependencies before others,
    you can set the `prepend` property equal to `true` on the second argument of `import()`.
    This will prepend the dependency to the vendor file instead of appending it, which is the default behavior.
    
    ```ember-cli-build.js
    app.import('bower_components/es5-shim/es5-shim.js', {
      type: 'vendor',
      prepend: true
    });
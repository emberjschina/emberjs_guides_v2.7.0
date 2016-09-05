Beim Entwickeln deiner Ember-Anwendung wirst du wahrscheinlich in manchen Situationen Funktionalität benötigen, die Ember nicht standardmäßig mitbringt. Dabei kann es sich um Authentifizierung handeln oder darum, SASS für deine Stylesheets zu verwenden. Mit den sogenannten [Ember Addons](#toc_addons) stellt Ember CLI ein einheitliches Format zur Verfügung, um wiederverwendbare Bibliotheken zu erstellen und verteilen, die solche Aufgaben lösen. Außerdem möchtest du vielleicht Frontend-Abhängigkeiten wie ein CSS-Framework oder einen JavaScript-Datepicker einsetzen, die nicht spezifisch mit Ember-Anwendungen zusammenhängen. Ember CLI unterstützt das Installieren solcher Pakete mit dem Standard-[Bower-Pakemetmanager](#toc_bower).

## Addons

Ember Addons can be installed using [Ember CLI](http://ember-cli.com/extending/#developing-addons-and-blueprints) (e.g. `ember install ember-cli-sass`). Addons können auch weitere Abhängigkeiten einbringen und dadurch die `bower.json`-Datei in deinem Projekt automatisch anpassen.

Eine Übersicht von Addons kannst du auf [Ember Observer](http://emberobserver.com) vorfinden.

## Bower

Ember CLI benutzt den Paketmanager [Bower](http://bower.io), mit dem du deine Frontend-Abhängigkeiten auf einfache Art aktuell halten kannst. Die Bower-Konfigurationsdatei, `bower.json`, befindet sich im Wurzelverzeichnis deines Ember-CLI-Projektes und erfasst die Abhängigkeiten deines Projekts. Durch Ausführen von `bower install` kannst du alle Abhängigkeiten, die in `bower.json` aufgeführt sind, auf einmal installieren.

Ember CLI überwacht `bower.json` auf Änderungen, d.h. es wird deine Anwendung neuladen, wenn du neue Abhängigkeiten mit `bower install <abhaengigkeiten> --save` installierst.

## Andere Ressourcen

Third-party JavaScript not available as an addon or Bower package should be placed in the `vendor/` folder in your project.

Your own assets (such as `robots.txt`, `favicon`, custom fonts, etc) should be placed in the `public/` folder in your project.

## Ressourcen kompilieren

When you're using dependencies that are not included in an addon, you will have to instruct Ember CLI to include your assets in the build. This is done using the asset manifest file `ember-cli-build.js`. You should only try to import assets located in the `bower_components` and `vendor` folders.

### Globale Variablen

The globals provided by some assets (like `moment` in the below example) can be used in your application without the need to `import` them. Provide the asset path as the first and only argument.

```ember-cli-build.js app.import('bower_components/moment/moment.js');

    <br />Es ist nötig, `"moment": true` zum `predef`-Abschnitt der `.jshintrc` hinzuzufügen, um Fehlermeldungen zu unterdrücken, dass eine undefinierte Variable benutzt werde.
    
    ### AMD-Javascript-Module
    
    Hier ist es nötig, den Pfad zur Ressource als das erste Argument und die Liste der Module und exports als das zweite Argument mitzugeben.
    
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

### Umgebungsabhängige Ressourcen

If you need to use different assets in different environments, specify an object as the first parameter. That object's key should be the environment name, and the value should be the asset to use in that environment.

```ember-cli-build.js app.import({ development: 'bower_components/ember/ember.js', production: 'bower_components/ember/ember.prod.js' });

    <br />Falls du eine Ressource nur in einer Umgebung benötigst, kannst du `app.import` in ein `if`-Statement einschließen.
    Für Ressourcen, die für Tests benötigt werden, solltest du auch die `{type: 'test'}`-Option benutzen, um sicherzustellen, dass sie auch im Test-Modus verfügbar sind.
    
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

    <br />Alle Stylesheet-Ressourcen, die so hinzugefügt werden, werden zusammengefügt und in die `/assets/vendor.css` geschrieben.
    
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

    <br />Falls du manche Abhängigkeiten vor anderen laden musst, kannst du im zweiten Argument von `import()` die `prepend`-Eigenschaft auf `true` setzen.
    Dadurch wird die Abhängigkeit in der vendor-Datei am Anfang eingefügt anstatt am Ende, wie es das Standard-Verhalten wäre.
    
    ```ember-cli-build.js
    app.import('bower_components/es5-shim/es5-shim.js', {
      type: 'vendor',
      prepend: true
    });
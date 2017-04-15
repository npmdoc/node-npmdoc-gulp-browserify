# api documentation for  [gulp-browserify (v0.5.1)](https://github.com/deepak1556/gulp-browserify)  [![npm package](https://img.shields.io/npm/v/npmdoc-gulp-browserify.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-gulp-browserify) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-gulp-browserify.svg)](https://travis-ci.org/npmdoc/node-npmdoc-gulp-browserify)
#### Bundle modules with Browserify

[![NPM](https://nodei.co/npm/gulp-browserify.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/gulp-browserify)

[![apidoc](https://npmdoc.github.io/node-npmdoc-gulp-browserify/build/screenCapture.buildCi.browser.apidoc.html.png)](https://npmdoc.github.io/node-npmdoc-gulp-browserify/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-gulp-browserify/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-gulp-browserify/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Robo"
    },
    "bugs": {
        "url": "https://github.com/deepak1556/gulp-browserify/issues"
    },
    "contributors": [
        {
            "name": "Robo"
        },
        {
            "name": "Steve Lacy"
        },
        {
            "name": "Shuhei Kagawa"
        }
    ],
    "dependencies": {
        "browserify": "3.x",
        "browserify-shim": "~2.0.10",
        "gulp-util": "~2.2.5",
        "readable-stream": "~1.1.10",
        "through2": "~0.4.0"
    },
    "description": "Bundle modules with Browserify",
    "devDependencies": {
        "chai": "~1.9.0",
        "coffeeify": "~0.6.0",
        "mocha": "~1.17.1"
    },
    "directories": {},
    "dist": {
        "shasum": "820108ac2554a954adb8be17d23958b0c04be083",
        "tarball": "https://registry.npmjs.org/gulp-browserify/-/gulp-browserify-0.5.1.tgz"
    },
    "engines": {
        "node": ">= 0.9"
    },
    "gitHead": "db0f8c151458703ea2eedd30f651ae2e2e912475",
    "homepage": "https://github.com/deepak1556/gulp-browserify",
    "keywords": [
        "gulpplugin",
        "gulpfriendly",
        "browserify"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "deepak_robo"
        }
    ],
    "name": "gulp-browserify",
    "optionalDependencies": {},
    "repository": {
        "type": "git",
        "url": "git+https://github.com/deepak1556/gulp-browserify.git"
    },
    "scripts": {
        "test": "mocha"
    },
    "version": "0.5.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module gulp-browserify](#apidoc.module.gulp-browserify)
1.  [function <span class="apidocSignatureSpan"></span>gulp-browserify (opts, data)](#apidoc.element.gulp-browserify.gulp-browserify)
1.  [function <span class="apidocSignatureSpan">gulp-browserify.</span>toString ()](#apidoc.element.gulp-browserify.toString)



# <a name="apidoc.module.gulp-browserify"></a>[module gulp-browserify](#apidoc.module.gulp-browserify)

#### <a name="apidoc.element.gulp-browserify.gulp-browserify"></a>[function <span class="apidocSignatureSpan"></span>gulp-browserify (opts, data)](#apidoc.element.gulp-browserify.gulp-browserify)
- description and source-code
```javascript
gulp-browserify = function (opts, data){
  opts = opts || {};
  data = data || {};

  ['noParse', 'extensions', 'resolve'].forEach(function(opt){
    if(opts[opt]) {
      data[opt] = opts[opt];
      delete opts[opt];
    }
  });

  function transform(file, enc, cb){
    var self = this;

    if (file.isStream()) {
      self.emit('error', new PluginError(PLUGIN_NAME, 'Streams not supported'));
      return cb();
    }

    // browserify accepts file path or stream.

    if(file.isNull()) {
      data.entries = file.path;
    }

    if(file.isBuffer()) {
      data.entries = arrayStream([file.contents]);
    }

    data.basedir = path.dirname(file.path);

    // nobuiltins option
    if (!opts.builtins && opts.nobuiltins) {
      var nob = opts.nobuiltins;
      var builtins = require('./node_modules/browserify/lib/builtins.js');
      nob = 'string' == typeof nob ? nob.split(' ') : nob;

      for (var i = 0; i < nob.length; i++) {
        delete builtins[nob[i]];
      };

      opts.builtins = builtins;
    }

    var bundler = browserify(data, opts);

    if(opts.shim) {
      for(var lib in opts.shim) {
          opts.shim[lib].path = path.resolve(opts.shim[lib].path);
      }
      bundler = shim(bundler, opts.shim);
    }

    bundler.on('error', function(err) {
      self.emit('error', wrapWithPluginError(err));
      cb();
    });

    [
      'exclude',
      'add',
      'external',
      'transform',
      'ignore',
      'require'
    ].forEach( function(method) {
      if (!opts[method]) return;
      [].concat(opts[method]).forEach(function (args) {
        bundler[method].apply(bundler, [].concat(args));
      });
    });

    self.emit('prebundle', bundler);

    var bStream = bundler.bundle(opts, function(err, src){
      if(err) {
        self.emit('error', wrapWithPluginError(err));
      } else {
        self.emit('postbundle', src);

        file.contents = new Buffer(src);
        self.push(file);
      }

      cb();
    });
  }

  return through.obj(transform);
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.gulp-browserify.toString"></a>[function <span class="apidocSignatureSpan">gulp-browserify.</span>toString ()](#apidoc.element.gulp-browserify.toString)
- description and source-code
```javascript
toString = function () {
    return toString;
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)

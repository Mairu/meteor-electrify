# (Meteor-)Electrify
[![npm](https://img.shields.io/npm/v/meteor-electrify.svg?logo=npm)](https://www.npmjs.com/package/meteor-electrify)
[![travis](https://img.shields.io/travis/Mairu/meteor-electrify/master.svg?label=build%20osx/linux&logo=travis-ci)](https://travis-ci.org/Mairu/meteor-electrify)
[![windows](https://img.shields.io/appveyor/ci/Mairu/meteor-electrify.svg?label=build%20windows&logo=appveyor)](https://ci.appveyor.com/project/Mairu/meteor-electrify)
[![Dependencies](https://img.shields.io/david/Mairu/meteor-electrify.svg)](https://david-dm.org/Mairu/meteor-electrify)
[![Test Coverage](https://api.codeclimate.com/v1/badges/949100a773b27b0ffc12/test_coverage)](https://codeclimate.com/github/Mairu/meteor-electrify/test_coverage)

Easily package your Meteor apps with Electron, and *butter*.

This package was not maintained for a long time and is archived now.

## TL;DR

````shell
npm install -g meteor-electrify electron
cd /your/meteor/app
electrify
````
## Compatibility

Works on all Meteor's supported [platforms](https://github.com/meteor/meteor/wiki/Supported-Platforms).

## Help

````bash
$ electrify -h

  Usage: electrify [command] [options]


  Commands:

    run       (default) start meteor app within electrify context
    bundle    bundle meteor app at `.electrify` dir
    package   bundle and package app to `--output` dir

  Options:

    -h, --help                 output usage information
    -V, --version              output the version number
    -i, --input    <path>      meteor app dir        | default = .
    -o, --output   <path>      output dir            | default = .electrify/.dist
    -s, --settings <path>      meteor settings file  | default = null (optional)
    -t, --temp     <path>      electrify temp folder | default = system temp folder
    -a, --arch     <arch>      arch to build for     | default = current arch
    -p, --platform <platform>  platform to build for | default = current platform

  Examples:

    # cd into meteor dir first
    cd /your/meteor/app
    
    electrify
    electrify run
    electrify package
    electrify package -o /dist/dir
    electrify package -o /dist/dir -s file.json
    electrify package -i /app/dir -o /dist/dir -s dev.json
    electrify package -- <electron-packager-options>
    
    # more info about electron packager options:
    # ~> https://www.npmjs.com/package/electron-packager

````

## Installation

Install meteor-electrify and electron globally:

````shell
npm install -g meteor-electrify electron
````

Alternatively you can install meteor-electrify and electron as dev dependency in you meteor application:

````shell
npm install --save-dev meteor-electrify electron
````

> For invoking Electron methods from Meteor, you'll also need to install the
> `meteor-electrify-client` package in your project.
> For more info check [Meteor x Electron integration](#meteor-x-electron-integration).

## Running app

````shell
cd /your/meteor/app
electrify
````

## Packaging

````shell
cd /your/meteor/app
electrify package
````

The packaging process is done under the hood using `electron-packager`
npm package. The following variables are automatically set:

  * `--out` -- *comes from cli option [-o, --output]*
  * `--arch` -- *comes from system [current arch]*
  * `--platform` -- *comes from system [current platform]*
  * `--app-version` -- *comes from .electrify/package.json [current app version]*
  * `--electron-version` -- *comes from the used electron npm package*
  * `--tmpdir` -- *disabled*

You can overwrite these default values and also set others by passing custom
arguments directly to `electron-packager` after `--`, i.e:

````shell
cd /your/meteor/app
electrify package -- --icon=/folder/x/img/icon.png --app-version=x.y.z
````

All the available options for `electron-packager` can be found here:
https://github.com/electron-userland/electron-packager/blob/master/usage.txt


Alternatively you can add an electronPackager object in package.json of the .electrify folder
with [options for the electron-packager](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md). For the icon property it is possible to provide an object
instead of string to define different icons for the different platforms.
f.e.
```json
{
  "electronPackager": {
     "icon": {
       "win32": "../something.ico",
       "darwin": "../mac.icns"
     }
  }
}
```

### Notes

The output app will match your current operational system and arch type.

  * To get an **OSX** app, run it from a **Osx** machine.
  * To get an **Linux 32bit** app, run it from a **32bit Linux** machine.
  * To get an **Linux 64bit** app, run it from a **64bit Linux** machine.
  * To get an **Windows 32bit** app, run it from a **32bit Windows** machine.
  * To get an **Windows 64bit** app, run it from a **64bit Windows** machine.

Due to NodeJS native bindings of such libraries such as Fibers -- *which are
mandatory for Meteor*, you'll need to have your Meteor app fully working on the
desired platform before installing this plugin and packaging your app.

So, at this time, you cannot package your app in a cross-platform fashion from
one single OS.

Perhaps you can live with it? :)

> **DO NOT** use options to output for multiple arch/platforms at once, such as
`--arch=all`. It won't work, Electrify can bundle Meteor apps only for the
platform you're running on.

> **NOTICE** you can use the --arch and --platform of electrify BUT you won't get a working version
for different platforms/archs out of the box. It's possible to make the generated application working
with further steps that are not in scope of this project.

## Options

1. `-i, --input` - Meteor app folder, default is current directory (`process.cwd()`).
2. `-o, --output` - Sets output folder for your packaged app, default is
`/your/meteor/app/.dist`
3. `-s, --settings` - Sets path for Meteor
[settings](http://docs.meteor.com/#/full/meteor_settings) file, this will be
available inside your Meteor code both in development and after being packaged.
4. `-t, --temp` - Sets a temp folder other than the system temp folder to prevent moving errors (tmp and destination should be on the same partition)
5. `-a, --arch` - Sets a different arch for building NOTICE: only the current arch will work "out of the box"
6. `-p, --platform` - Sets a different platform for building NOTICE: only the current platform will work "out of the box"

## Structure

You'll notice a new folder called `.electrify` in your meteor app dir, its
structure will be like this:

````
/your/meteor/app
├── .electrify
│   ├── .gitignore
│   ├── electrify.json
│   ├── index.js
│   └── package.json
├── .meteor
└── ...
````

This is a pure Electron project, so you can use the whole Electron API from JS
files in this folder. Also, you can install electron dependencies and store them
in the `package.json` file. Note that the `electrify` package is itself a
dependency.

See this folder as the `desktop layer` for your Meteor app. Remember to check
out the `index.js` file, it constains the electrify start/stop usage.

The `electrify.json` file will hold specific preferences for Electrify, such as
plugins and so on.
If you want other settings during development than for bundling you can create a `electrify.local.json` file
which will be used instead of `electrify.json` as long the app is not packaged. Be aware that nothing will be merged.

### Config (`electrify.json` / `electrify.local.json`)

Following options are available:

1. `preserve_db` - Set it to true to preserve database between installs. It works by saving the
                   mongo data dir inside user's data folder, instead of being self contained within
                   the app folder (which gets deleted when new version is installed).
2. `port`        - Set a port to be used while in dev mode (can also be set by PORT environment variable)
3. `mongo`       - Possibility to use an external Mongo database (ATTENTION: You cannot hide the credentials safely, be sure you want to give them out!)<br>
   Alternatively you can use [DDP connections to connect to an external Meteor server](https://docs.meteor.com/api/connections.html#DDP-connect).<br>
   This option is an object of following [Meteor environment variables](https://docs.meteor.com/environment-variables.html):
   - MONGO_URL
   - MONGO_OPLOG_URL
   
   To allow a bit of safety, you can give a filepath to a javascript file that exports a function that sets the variables.
   So it is on you to secure the URLs (But it cannot be safe!) Filepath is relative to .electrify folder.
````javascript
// sample file to define mongo urls
// "mongo": "mongo.js" -> .electrify/mongo.js
/**
* Function to set the mongo related env variables
* @param {object} env set mongo url env var to this object
* @param {object} config parsed electrify.json
* @param {function} done call this if you are done
*/
module.exports = function(env, config, done) {
  env.MONGO_URL = '...';
  done();
}
```` 

# Customizing

Let's see how one would be able to do a simple SplashScreen:

````javascript
const { app, BrowserWindow } = require('electron');
const electrify = require('meteor-electrify')(__dirname);

let window;
let splash;

app.on('ready', function() {
  splash = new BrowserWindow({ // starts splash window
    // >>> your configs here
  });
  splash.loadUrl('./splash.html'); // create the ".electrify/splash.html" file
  
  // then move along and start electrify
  electrify.start(function(meteor_root_url) {
    // before opening a new window with your app, destroy the splash window
    splash.close(); // >>> or .destroy(), check what works for you

    // from here on, well, nothing changes..

    // creates a new electron window
    window = new BrowserWindow({
      width: 1200, height: 900,
      nodeIntegration: false // node integration must to be off
    });

    // open up meteor root url
    window.loadURL(meteor_root_url);
  });
});

// ....
````

## Meteor x Electron integration

You can seamlessly call Electron methods from your Meteor's client/server code.

Define your Electron methods inside the `.electrify` folder:

````javascript
// `.electrify/index.js` file
electrify.methods({
  'hello.world': function(firstname, lastname, done) {
    // do things with electron api, and then call the `done` callback
    // as ~> done(err, res);
    done(null, 'Hello '+ firstname +' '+ lastname +'!');
   }
});
````

Then, in your Meteor code (client and server), you can use the [meteor-electrify-client](https://www.npmjs.com/package/meteor-electrify-client) to call these methods. 

## Upgrading

When upgrading to newer versions, it's **important** to know that:

### ~> templates

Once these files exists on disk, they *will not* be overwritten.
 * `.electrify/index.js`
 * `.electrify/package.json`
 * `.electrify/electrify.json`
 * `.electrify/.gitignore.json`

### ~> api

As these files above will never be overwritten, in case of any API changes that needs
adjustments, these will have to be made manually.

### ~> version matching

Always keep the same electrify version in your Meteor, and inside the
`.electrify` folder, *as per specified in `.electrify/package.json` file*.

## Questions?

Do not open issues, use the chat channel instead.

[![Join the chat at https://gitter.im/arboleya/electrify](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/arboleya/electrify)

## Problems?

Please make sure your are always up to date before opening an issue. Follow the released fixes through the
[HISTORY.md](HISTORY.md) file.

If you find any problem, please open a meaningful issue describing in detail how
to reproduce the problem, which platform/os/arch type you're using, as well as
the version of Meteor and Electrify, and any other info you may find usefull.

## License

The MIT License (MIT)

Copyright (c) 2017-2019 Sebastian Große
Electrify originally created by Copyright (c) 2015 Anderson Arboleya

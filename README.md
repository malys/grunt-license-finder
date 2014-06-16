# grunt-license-finder [![Build Status](https://secure.travis-ci.org/iandotkelly/grunt-license-finder.png)](http://travis-ci.org/iandotkelly/grunt-license-finder)[![Dependency Status](https://gemnasium.com/iandotkelly/grunt-license-finder.svg)](https://gemnasium.com/iandotkelly/grunt-license-finder)

> Finds licenses in a node project and its dependencies

## Getting Started
This plugin requires Grunt `~0.4.5`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-license-finder --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-license-finder');
```

## The "license_finder" task

### Overview
In your project's Gruntfile, add a section named `license_finder` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  license_finder: {
    options: {
      // Task-specific options go here.
    }
  },
});
```

### Options

#### options.directory
Type: `String`
Default value: `current project directory`

A string value for the path of the node project to scan

#### options.production
Type: `Boolean`
Default value: false

A boolean value. If true, only production dependencies are included in the scan

#### options.out
Type: `String`
Default value: `./licenses.txt`

A string value for the filename of the output of the scan.

#### options.csv
Type: `Boolean`
Default value: `./licenses.txt`

A boolean value.  If true the output is in a comma-separated-variable format for import into a spreadsheet.

### Usage Examples

#### Custom Options
Example

```js
grunt.initConfig({
  license_finder: {
    main:{
        options: {
          production: true,
          directory: '/Home/me/some-project',
          out: '/Home/me/some-project/licenses.txt',
          csv: true,
          checker: function (data) {
            var badLicenses = ['GPL'];
            var j;
            var lim1 = data.length;
            var globalList = [];
            for (j = 0; j < lim1; j++) {
                var dep = data[j];
                var list = [];
                list = list.concat(dep.licenseSources.package.summary());
                list = list.concat(dep.licenseSources.license.summary());
                list = list.concat(dep.licenseSources.readme.summary());

                globalList.push({
                    obj: dep,
                    licenses: list
                });

            }
            var result = [];
            var i;
            var limit = globalList.length;

            for (i = 0; i < limit; i++) {
                j = 0;
                lim1 = badLicenses.length;
                for (j = 0; j < lim1; j++) {

                    if (globalList[i].licenses.indexOf(badLicenses[j]) > -1) {
                        if (result.length === 0) {
                            grunt.log.error('Detected viral Licenses :');
                        }
                        result.push(globalList[i].obj);
                        grunt.log.error(globalList[i].obj.name + ' : ' + globalList[i].licenses.toString());
                    }
                }

            }
            if (result.length > 0) {
                throw grunt.util.error('Viral Licenses has been detected !!!');
            }
        }
        }
    }
  }
});
```

### Example output

<pre>
commander@0.6.1 [license(s): MIT]
└── readme files: MIT

read-installed@0.2.2 [license(s): BSD]
└── license files: BSD

glob@3.2.3 [license(s): BSD]
├── package.json:  BSD
└── license files: BSD

archy@0.0.2 [license(s): MIT/X11]
└── package.json:  MIT/X11

json-stringify-safe@5.0.0 [license(s): BSD]
├── package.json:  BSD
└── license files: BSD

should@1.2.2 [license(s): MIT]
└── readme files: MIT
</pre>

For output in CSV format use the csv: true option.

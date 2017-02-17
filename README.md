# Bitgrip - Boilerplate

## Static site generator with grunt and assemble

## Index
1. [Introduction](##Introduction)
1. [File Structure](##File-Structure)
1. [Setup](##Setup)
1. [Persons in charge](##persons-in-charge)

***

## Introduction

This is a short example to create a basic project setup for working with assemble, handlebars and sass.
You can clone an example repository: https://bitbucket.org/bitgrip/bitgrip-boilerplate.git
The dist-folder won't be tracked via GIT.

***

## File-Structure
The file structure should be setup like this:
```
project-name
|-- dist
|-- src
|   |-- assemble
|   |   |-- data
|   |   |-- layout
|   |   |-- pages
|   |   |-- partials
|   |-- assets
|   |   |-- fonts
|   |   |-- img
|   |-- js
|   |   |-- components
|   |   |-- vendor
|   |   | app.js
|   |-- sass
|   |   |-- components
|   |   |-- extends
|   |   |-- functions
|   |   |-- layout
|   |   |-- mixins
|   |   | app.scss
|   |   | _base.scss
|   |   | _resets.scss
|   |   | _settings.scss
| Gruntfile.js
| package.json
| readme.md
```
***

## Setup

#### Installing project dependencies

**Make sure that grunt is already installed.**

Go to the project directory (Terminal.app).

Use the fowllowing npm-commands to setup the project:
+ npm init (builds the package.json file)
+ npm install grunt --save-dev (grunt task runner)
+ npm install grunt-contrib-connect --save-dev (start a connect web server)
+ npm install grunt-contrib-watch --save-dev (run predefines tasks whenever watched files are added, changed or deleted)
+ npm install grunt-contrib-clean --save-dev (clean files and folders)
+ npm install grunt-contrib-copy --save-dev (copy files and folders)
+ npm install grunt-sass --save-dev (compile sass to css with libsass)
+ npm install assemble --save-dev (site generator for grunt)
+ npm install autoprefixer --save-dev (autoprefixer for sass)
+ npm install grunt-contrib-cssmin --save-dev (minifiy the css)
+ npm install grunt-contrib-uglify --save-dev (minify the js)
+ npm install --save-dev jit-grunt (a JIT(Just In Time) plugin loader)
+ npm install --save-dev time-grunt (calculates the time it took for each task in executing your grunt build)
 
-> run 'npm intall' (to get the node-modules and packages)
 
The 'package.json' should look like this:
```json
"devDependencies": {
    "assemble": "^0.12.0",
    "autoprefixer-core": "^6.0.1",
    "grunt": "^1.0.1",
    "grunt-assemble": "^0.4.0",
    "grunt-contrib-clean": "^1.0.0",
    "grunt-contrib-connect": "^1.0.2",
    "grunt-contrib-copy": "^1.0.0",
    "grunt-contrib-cssmin": "^1.0.1",
    "grunt-contrib-uglify": "^1.0.1",
    "grunt-contrib-watch": "^1.0.0",
    "grunt-sass": "^1.2.0",
    "jit-grunt": "^0.10.0",
    "time-grunt": "^1.3.0"
  }
```
#### Load and register Grunt-Tasks
**Create a basic 'Gruntfile.js' in the project directory.**
```js
module.expormodule.exports= function(grunt) {

    var rootPath = {};
    rootPath.src = "src";
    rootPath.srcAssemble = rootPath.src + "/assemble";
    rootPath.dist = "dist";
    rootPath.distCSS = rootPath.dist + "/css";
    rootPath.distJS = rootPath.dist + "/js";
    
    // default assemble layout
    var assembleLayout = "default.hbs";
    
    // browsers to target when prefixing CSS.
    var compatibility = ['last 2 versions', '> 5%'];
    
    // port to use for the development server.
    var PORT = 63342;

    require('jit-grunt')(grunt);
    require('time-grunt')(grunt);
    
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
    });
 
  grunt.loadNpmTasks('grunt-assemble');
  grunt.loadNpmTasks('grunt-sass');
  //grunt.loadNpmTasks('grunt-contrib-connect'); todo
  grunt.loadNpmTasks('grunt-contrib-watch');
  grunt.loadNpmTasks('grunt-contrib-clean');
  grunt.loadNpmTasks('grunt-contrib-copy');
  grunt.loadNpmTasks('grunt-contrib-cssmin');
  grunt.loadNpmTasks('grunt-contrib-uglify');
  // grunt.loadNpmTasks('grunt-autoprefixer'); todo
     
  grunt.registerTask('dev', ['clean', 'assemble', 'sass', 'copy']);
  grunt.registerTask('build-all', ['clean', 'assemble', 'sass', 'uglify', 'cssmin', 'copy']);
  grunt.registerTask('default', ['build-all', 'watch']);
};
```

**Configure the single grunt-tasks**

Make sure that every task followed by another ends up with a comma!

- The assemble task:

```js
assemble: {
      options: {
        flatten: true,
        layout: "default.hbs",
        layoutdir: rootPath.srcAssemble + "/layout/",
        partials: rootPath.srcAssemble + "/partials/**/*.hbs",
        data: rootPath.srcAssemble + "/data/*.json"
      },
      pages: {
        files: {
          'dist/': [rootPath.srcAssemble + '/pages/*.hbs']
        }
      }
    }
```
- The SASS task

```js
sass: {
      dist: {
        options: {
          outputStyle: 'expanded',
          sourceMap: true
        },
        files: {
          'dist/css/app.css': rootPath.src + '/sass/app.scss'
        }
      }
    }
```
- Minify CSS and JS files

```js
cssmin: {
      target_css: {
        files: [{
          expand: true,
          cwd: rootPath.distCSS,
          src: ['*.css', '!*.min.css'],
          dest: rootPath.distCSS,
          ext: '.min.css'
        }]
      }
    },

    uglify: {
      target_js: {
        files: {
          'dist/js/app.min.js': [rootPath.src + '/js/app.js']
        }
      }
    }
```
- the watch task

```js
watch: {
      grunt: {
        options: {
          reload: true
        },
        files: ['Gruntfile.js']
      },

      hbs: {
        files: '**/*.hbs',
        tasks: ['assemble'],
        options: {
          livereload: true
        }
      },

      sass: {
        files: '**/*.scss',
        tasks: ['sass'],
        options: {
          livereload: true
        }
      },

      json: {
        files: '**/*.json',
        tasks: ['assemble'],
        options: {
          livereload: true
        }
      },

      js: {
        files: ['js/*.js'],
        tasks: ['copy'],
        options: {
          livereload: true
        }
      }
    }
```
- The clean task

```js
clean: {
      all: [rootPath.dist + '/*']
    }
```
- The copy task

```js
copy: {
      built: {
        files: [
          {
            expand: true,
            cwd: rootPath.src,
            src: ['js/**', 'assets/**'],
            dest: rootPath.dist,
            filter: 'isFile'
          }
        ]
      }
    }
```
- The autoprefixer task

```js
autoprefixer: {
      options: {
        map: true,
        browsers: ['last 2 versions', '> 5%']
      },
      dist: {
        files: {
          'dist/css/app.css': 'dist/css/app.css'
        }
      }
    }
```

Once all loaded tasks are cofigured you can use the grunt build process.

#### Run a grunt task
Run your grunt-tasks with the following commands (Terminal.app) or use an IDE integrated grunt plugin:

+ grunt dev (only builds a development version without minifying files)
+ grunt build-all (builds a dist version)
+ grunt (the default task. same as 'build all', but includes the watch-task)

## Persons in charge

Marc Mucha (mailto: marc.mucha@bitgrip.de)
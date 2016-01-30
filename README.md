# yoga

[![npm version](https://img.shields.io/npm/v/generator-yoga.svg)](https://npmjs.org/package/generator-yoga) 

**yoga** is a prompt-driven *scaffolding system* (boilerplate/starter-code generation). Its aim is to make it easier to create and maintain personal boilerplates that evolve over time.

What makes **yoga** different?

- 100% driven by command-line prompts (via [Inquirer](https://github.com/SBoudrias/Inquirer.js)).
- The logic of copying files is in the filenames themselves, e.g. `{isGulp}gulpfile.js` or `{useSass}main.scss`.
- Files are templated using [ejs](https://github.com/mde/ejs), extended with additional whitespace-friendly syntax for better readability (via [striate](https://github.com/metaraine/striate)).

*(see [How it works](#how-it-works) below for details)*

Generators created by yoga are [yeoman](http://yeoman.io/) generators, so they can be published and consumed them like any other yeoman generator. You do not need to understand yeoman to use yoga, however, and you do not need to write any code to create and maintain your generator.

## Usage

### 1. Create your generator

**yoga** requires npm, which comes with [node](https://nodejs.org/en/download/).

Your initial generator is itself generated by yoga. You only need to do this once.

```sh
$ npm install -g yo                 # install yeoman
$ npm install -g generator-yoga     # install yoga
$ mkdir mygenerator                 # create a directory for your new generator
$ cd mygenerator                    
$ yo yoga                           # generate your personal generator
$ npm link                          # alias your generator to your globally
                                    # installed npm modules so that you can run
                                    # it with yeoman.
```

### 2. Generate projects to your heart's content

Yay! Now you can run `yo mygenerator` in an empty directory to generate a new project.

```sh
$ yo mygenerator
? Does your project use Javascript? Yes
? Does your project use css? Yes
...
```

An initial **yoga** generator just consists of a blank README, so you have to customize it to generate something useful.

#### Customizing your generator

You can customize your **yoga** generator without writing any generator code:

- Drop files into `app/templates`. All files from this directory will be copied into your project directory when you run the generator.
- Edit the [Inquirer](https://github.com/SBoudrias/Inquirer.js) prompts in `app/yoga.json`. These will determine which files get copied (via [prefixnotes](https://github.com/metaraine/prefixnote)) and what code gets copied (via [striate](https://github.com/metaraine/striate)).

For example, if you have a prompt in your yoga.json that inquires about Javascript being included in the project:

```js
{
  prompts: [{
    "type": "confirm",
    "name": "js",
    "message": "Does your project use Javascript?",
    "store": true
  }]
}
```

And a file in `app/templates` which specifies the prompt `name` in a [prefixnote](https://github.com/metaraine/prefixnote):

```
.
└── {js}main.js
```

Then when you run `yo mygenerator`, you will be prompted `Does your project use Javascript?`. If you choose yes, `main.js` will be copied into the project directory. If you choose no, it will not be copied.

Check out [generator-yoga-sample](https://github.com/metaraine/generator-yoga-sample) for an example yoga generator.

## More detailed usage

[Inquirer](https://github.com/SBoudrias/Inquirer.js) prompts drive everything in a yoga generator.

Sample yoga.json file:

```js
{
  "prompts": [
    {
      "type": "confirm",
      "name": "js",
      "message": "Does your project use Javascript?",
      "store": true
    },
    {
      "type": "confirm",
      "name": "css",
      "message": "Does your project use css?",
      "store": true
    }
  }
}
```

The above yoga.json file would prompt you with two questions every time you run your generator and store them in `js` and `css` variables. These variables drive the main two aspects of scaffolding: file copying and templating.

### 1. File Copying

You can control which files in `/app/templates` get copied into your new project by prefixing filenames with expressions that include prompt variables.

```
.
├── index.html
├── {js}scripts
│   └── main.js
└── {css}styles
    └── main.css
```

In the above example, the scripts folder will only be copied if `js` (as declared in yoga.json) is true, and the `styles` folder will only be copied if `css` is true.

Some notes about file copying:

- Empty expressions are a great way to include system and hidden files in your templates folder without them having an effect until they are copied:
  - `{}package.json`
  - `{}.gitignore`
- If a folder name only consists of an expression, all files will be copied to the parent folder:

  ```
  main.js
  {js}
    ├── 1.js
    ├── 2.js
    └── 3.js
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⇨

  ```
  main.js
  1.js
  2.js
  3.js
  ```

- Expressions can be any boolean Javascript statement:

  ```
  {js && gulp}gulpfile.js
  ```

See [prefixnote](https://github.com/metaraine/prefixnote) for the nitty-gritty.


### 2. Templating

You can use [striate](https://github.com/metaraine/striate), a superset of [ejs](https://github.com/mde/ejs), to control which code gets generated within the files. The answers given to the prompts in yoga.json are available as variables within the scope of your template files.

```html
<!DOCTYPE html>
<html>
<head>
  >> if(css) {
  <link rel='Stylesheet' type='text/css' href='styles/main.css'>
  >> }
</head>
<body>
  >> if(js) {
  <script src='scripts/main.js'></script>
  >> }
</body>
</html>
```

You can see a complete yoga generator with prompts, file prefixes, and templating at [generator-yoga-sample](https://github.com/metaraine/generator-yoga-sample).

## License

ISC © [Raine Lourie](https://github.com/metaraine)

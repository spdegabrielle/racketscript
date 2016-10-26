# RacketScript

[![Build Status](https://travis-ci.org/vishesh/racketscript.png?branch=master)](https://travis-ci.org/vishesh/racketscript)

RacketScript is an *experimental* lightweight Racket to JavaScript
compiler. The generated code is ES6, which can be translated to ES5
using [Babel](https://babeljs.io/)
or [Traceur](https://github.com/google/traceur-compiler). RacketScript
aims to leverage both JavaScript and Racket's ecosystem, and make
interoperability between them clean and smooth.

RacketScript takes in Racket source files, uses Racket's macro
expander to
produce
[Fully Expanded Programs](https://docs.racket-lang.org/reference/syntax-model.html#%28part._fully-expanded%29),
and then compile these fully expanded programs to
JavaScript. RacketScript doesn't support Racket features which are
expensive, for example proper tail calls and continuations.

## Try RacketScript

You can try RacketScript in your browser
at [RacketScript Playground](http://rapture.twistedplane.com:8080/).

## Installation

Following system packages are required -

- Racket 6.4 or higher
- NodeJS and NPM
- Make

NPM and Racket package installation steps could be done by directly by
executing `make setup` or `raco setup` at RacketScript root
directory.

RacketScript will generate Gulpfiles to compile ES6 to ES5 using
Traceur or Babel.  If you wish to run ES6 modules directly, install
Traceur using NPM. Babel is recommended for writing NodeJS programs.

If you wish to hack on RacketScript code or run tests, lints and
coverage tool, run `make setup-extra` form RacketScript root
directory.  See `Makefile` for more information. This will also
install `js-beautify` which produces beautified JavaScript output.

If you do not wish to pollute your root NPM directory, you can set a
custom global location by changing your `npmrc` (eg.  `echo "prefix =
$HOME/.npm-packages" >> ~/.npmrc`. Then add `/prefix/path/above/bin`
to your `PATH`.

### Build

The compiler is written in Typed Racket. To avoid long startups, it is
advised to pre-compile Racket sources to bytecode. This step is not
required if are not making any changes to compiler.

    # From source root
    $ make build

## Basic Usage

    $ racks -h

    racketscript [ <option> ... ] [<filename>]
      Compile Racket to JavaScript
     where <option> is one of
      -d <dir>, --build-dir <dir> : Output directory
      -n, --skip-npm-install : Skip NPM install phase
      -g, --skip-gulp-build : Skip Gulp build phase
      -b, --js-beautify : Beautify JS output
      --dump-debug-info : Dumps some debug information in output directory
      --stdin : Reads module from standard input, with file name argument being pseudo name
      --enable-self-tail : Translate self tail calls to loops
    * -t <target>, --target <target> : ES6 to ES5 compiler [traceur|babel]
    / --expand : Fully expand Racket source
    | --ast : Expand and print AST
    | --rename : Expand and print AST after α-renaming
    | --il : Compile to intermediate langauge (IL)
    | --js : Compile and print JS module to stdout
    \ --complete : Compile module and its dependencies to JS
      --help, -h : Show this help
      -- : Do not treat any remaining argument as a switch (at this level)
     * Asterisks indicate options allowed multiple times.
     /|\ Brackets indicate mutually exclusive options.
     Multiple single-letter switches can be combined after one `-'; for
      example: `-h-' is the same as `-h --'

Default output directory is named `js-build`. To compile a Racket file
named `foobar.rkt`, run -

        # Installs all NPM dependencies and compile foobar.rkt
        $ racks foobar.rkt

		# To avoid re-installing NPM packages use `-n`
		$ racks -n foobar.rkt

        # Override default output directory
        $ racks -d /path/to/output/dir foobar.rkt

		# To beautify assembled modules use `-b`
		$ racks -b foobar.rkt

The compiled ES6 modules typically goto one of following three folders:

- "modules": The normal Racket files.
- "collects": Racket collects source files.
- "links": Other third party packages.

If you use _skip-gulp-build_ option, run `gulp` in output directory to
produce final output. To speed up later builds, you may want to skip
NPM install phase by using _skip-npm-install_ flag.

### Traceur

By default RacketScript will use Traceur and produce `dist/compiled.js`. To
execute inside NodeJS, execute `bootstrap.js` in output directory. For
running in browser, include the Traceur runtime along with
`dist/compiled.js`.

A more robust (and less portable) way, is to run the ES6 modules
generated in `modules` directly from Traceur. Goto `modules` output
directory and execute `$ traceur foobar.js`.

### Babel

RacketScript could also use `Babel`. It will compile each assembled ES6
module to ES5, and put it in `dist` directory, persevering original
directory structure. Replace above command with following -

        $ racks --es6 babel foobar.rkt

This will compile each ES6 module generated by RacketScript, and put in
dist with same directory structure.

## Related Work

- [Whalesong](https://github.com/dyoo/whalesong/tree/master/whalesong)
- [Urlang](https://github.com/soegaard/urlang)

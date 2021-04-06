# tsk

uncomplicated task runner

## setup

- save [tsk.js](https://raw.githubusercontent.com/r1vn/tsk/master/tsk.js) in the directory you want to be the root of your project
- run `node tsk -tskinit` to initialize the example project or create the manifest file manually

## example

project root directory:

```
tsk.js
tsk.manifest.js
lib/hello/index.js
lib/world/index.js
```

**`tsk.manifest.js`**

defines the sequences and module configurations

modules are specified in a sequence with the relative filepath of their entry point (`.js` and `/index.js` may be omitted)

```ecmascript 6
exports.helloworld =
[
    {
        module: 'lib/hello'
    },
    {
        module: 'lib/world',
        config:
        {
            exclamation: true
        }
    }
]
```

**`lib/hello/index.js`**  
**`lib/world/index.js`**

the function exported by a module file is imported and called in a separate child process.  
a module may be used in the sequence any number of times and will be ran in a new process each time.  
module processes inherit the command line arguments from the parent process. the working directory is set to the project root.  
if module process exits with a non-zero code (explicitly or because of an unhandled exception), the sequence is terminated and the parent process exits.  

`lib/hello/index.js`

```ecmascript 6
module.exports = function ()
{
    console.log('hello')
}
```

`lib/world/index.js`

```ecmascript 6
module.exports = function (config)
{
    console.log(config.exclamation ? 'world!' : 'world')
}
```

**`tsk.js`**

validates and runs the sequence specified in the command line.  
`helloworld` sequence defined in the manifest is invoked with `node tsk helloworld`.  
with `-step` argument (i.e. `node tsk helloworld -step`), the sequence execution is paused before each module:

```
1 lib/hello
2 lib/world
Press Enter to start, Ctrl+C to exit. Next (1/2): lib/hello
----------------------------------------
1/2 lib/hello

hello

lib/hello : OK (33ms)
Press Enter to continue, Ctrl+C to exit. Next (2/2): lib/world
----------------------------------------
2/2 lib/world

world!

lib/world : OK (36ms)
----------------------------------------
OK (69ms)
```

## demos

- https://github.com/r1vn/slate - basic multipage brochure
- https://github.com/r1vn/whitenoise - ditto
- https://github.com/r1vn/the_great_wave - static gallery/blog
# Bake

Simple Bash build/project utility in the style of rake.

## Installation

Use [basher](https://github.com/basherpm/basher), a package manager for shell scripts and functions.

    basher install dylanmei/bake

# Usage

Display all tasks

    bake

Run `build` task

    bake build

Run `archive` task with `output` param

    bake archive output=dist

## Example Bakefile

    PATH=node_modules/coffee-script/bin:$PATH

    function private {
        echo in private
    }


    #. Builds the project
    function build {
        # ensures clean is called only once
        invoke clean
        bake_ok building ...
    }


    #. Cleans the project
    function clean {
        bake_ok "cleaning ..."
        private
    }


    #. Compiles coffee scripts
    function coffee {
        outdated build src || return 0
        coffee -c -o build src
        bake_ok "coffe" "compiled"
    }


    function on_task_not_found {
        echo "Task not found $1"
    }

## Rules

* `bake` searches the current and parent directories for a `Bakefile` to run.
* Tasks are defined as normal Bash functions.
* A task description is simply a comment that starts with `#.` and precedes
  a function.


## Functions

Prints a red error message.

    bake_error <action> <description>

    example: bake_error "compiling" "src/lib/test.coffee"

Prints a plain message.

    bake_log <action> <description>

    example: bake_log "bake" "Processing bakefile..."

Prints a green ok message.

    bake_ok <action> <descsription>

    example: bake_ok "compiling" "compiled src/lib/test.js"

Prints a cyan info message.

    bake_info <action> <description>

    example: bake_info "bake" "built project in 700ms"

Invokes a task only once.

    invoke <function_name>

    example: invoke "clean"

Determines if target is older than reference, returning 1 if outdated.

    outdated <target> <reference>

    examples:

    outdated build src || return 1          # skip rest of task
    outdated build src && invoke "compile"  # compile if outdated

Includes external script(s).

    include "<file_glob>"

    example: include "includes/*"
 - _Note: use `$bake` to invoke bake from an included task retaining the original top level directory_


Run a dynamic task when task `$1` is not found. For example, to run
a test as the first argument to `bake`, add this to `Bakefile`

    function on_task_not_found {
        [[ -f test/$1.js ]] && mocha test/$1.js && return 0
        return 1
    }

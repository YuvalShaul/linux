# Variables

## What are positional parameters?

- When you run a script (or call a function), bash automatically fills in a set of variables from the arguments you passed — these are the **positional parameters**.
- **$1** is the first argument, **$2** the second, and so on. **$0** is special: it holds the name the script was invoked with, not an argument.

  ```bash
  #!/bin/bash
  echo $0
  echo $1
  echo $2
  ```

  ```bash
  ./params hello world
  # ./params
  # hello
  # world
  ```

- Past `$9` you must wrap the number in braces — `$10` is parsed as `${1}0` (parameter 1 followed by a literal `0`), not parameter ten:

  ```bash
  echo $10     # WRONG — this is $1 followed by "0"
  echo ${10}   # correct — this is parameter #10
  ```

## Positional Parameters

- Write a script called **params**.
- Use your script to print:
  - the script file name itself
  - parameter # 8
  - parameter # 11

## What are the special parameters?

- Alongside `$1`, `$2`, ... bash keeps a handful of built-in variables that report on the parameters and the shell itself:

  | Variable | Meaning |
  |---|---|
  | **`$?`** | **the exit status of the last command that ran (`0` = success, non-zero = failure)** |
  | `$0` | the name the script (or shell) was invoked with |
  | `$#` | the number of positional parameters passed in |
  | `$*` | all positional parameters, as a single word (joined by the first character of `IFS`, a space by default) |
  | `$@` | all positional parameters, each kept as its own separate word |
  | `$-` | the flags currently set for this shell (e.g. `i` for interactive) |
  | `$$` | the process ID (PID) of the current shell |
  | `$!` | the PID of the most recently started background job |

- **`$?` is the most important one of the bunch — it's the foundation of all bash "logic".** Every command, when it finishes, sets `$?` to its exit status. Everything bash uses to make decisions — `if`, `while`, `&&`, `||` — is really just checking exit statuses under the hood, not "true"/"false" in the way other languages mean it:

  ```bash
  grep -q "yuval" /etc/passwd
  if [ $? -eq 0 ]; then
    echo "found"
  else
    echo "not found"
  fi
  ```

- You rarely check `$?` by hand like above — `if`, `&&` and `||` do it for you automatically, testing the exit status of the command that just ran:

  ```bash
  grep -q "yuval" /etc/passwd && echo "found" || echo "not found"

  if grep -q "yuval" /etc/passwd; then
    echo "found"
  fi
  ```

- The convention is the opposite of most programming languages: **0 means success**, and any non-zero value means some kind of failure (often used to signal *which* failure).

- `$?` only reflects the command that ran *immediately* before you check it — checking it a second time reads the exit status of the check itself:

  ```bash
  false
  echo $?
  # 1
  echo $?
  # 0   (this is the exit status of the previous "echo", not "false")
  ```

- `$*` vs `$@` matters once you quote them. `"$@"` expands to each argument as its own quoted word (safe for arguments containing spaces); `"$*"` collapses everything into one word:

  ```bash
  ./params "hello world" foo
  # with "$@": two arguments -> "hello world" and "foo"
  # with "$*": one argument  -> "hello world foo"
  ```

## Special Variables

- Use a script called **params** to try out the following special parameters:
  - $*
  - $@
  - $#
  - $?
  - $-
  - $$
  - $!
  - $0

## Variables

- Define two variables:
  - **var1** shoudle be equal to 5
  - **var2**  shoudle be equal to hello world
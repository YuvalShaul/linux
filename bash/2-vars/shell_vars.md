# Shell Variables

## What is a shell variable?

- A shell variable is just a name bound to a value, kept in the shell's memory for as long as that shell is running.
- You assign one with `name=value` — **no spaces** around the `=`, and none between the name and the value.

  ```bash
  myname=yuval
  ```

- Spaces would break it, because the shell decides how to parse a command *before* it knows anything about variables. A space tells the shell "new word starts here", so `myname = yuval` is read as three separate things: a command called `myname`, the argument `=`, and the argument `yuval` — not an assignment at all.

  ```bash
  # this is an assignment
  myname=yuval

  # this is NOT an assignment — the shell tries to run
  # a command named "myname" with arguments "=" and "yuval"
  myname = yuval
  ```

- If the value itself contains spaces, quote the whole value — the `=` still has no space around it:

  ```bash
  myname="yuval shaul"
  ```

- You read a variable's value back with `$name` (commonly wrapped as `${name}` when it needs to be unambiguous next to other text):

  ```bash
  echo $myname
  # yuval
  ```

- To fill a variable from user input, use the **read** builtin — it pauses the script and waits for a line typed on stdin:

  ```bash
  read myname
  # (user types: yuval)
  echo $myname
  # yuval
  ```

- **read** can also fill several variables from one line at once — one word per variable, with any leftover words going into the last one:

  ```bash
  read first last
  # (user types: yuval shaul)
  echo $first
  # yuval
  echo $last
  # shaul
  ```

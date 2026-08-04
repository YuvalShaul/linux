# Environment

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

## What is an environment variable?

- A **shell variable** lives only inside the current shell. If that shell starts a child process (a new shell, a script, a program), the child does **not** see it.
- An **environment variable** is a shell variable that has been marked "for export". When a child process starts, it gets a **copy** of all of its parent's environment variables — that's how programs pass settings down to the processes they launch (e.g. **PATH**, **HOME**, **LANG**).
- So: every environment variable is a shell variable, but not every shell variable is an environment variable. Exporting is what turns one into the other.
- The copy only flows **downward**, parent → child. A child can change or export its own variables, but that never affects the parent shell it came from.

## How do you see them?

- **env** or **printenv** — list only the variables that are exported (i.e. the actual environment)
- **printenv VARNAME** — print one specific environment variable
- **set** — list *everything* visible to the shell: exported variables, plain shell variables, and shell functions
- **echo $VARNAME** — print the value of any variable (shell or environment) by name

## Where are they stored?

- Environment variables aren't saved in a file — they live in the memory of each running process, as a list of `KEY=value` strings.
- You can inspect that list for any process through the `/proc` filesystem:  
**cat /proc/\<pid\>/environ | tr '\\0' '\\n'**  
(try it with `$$`, which is the PID of your current shell)
- Every process keeps its own copy. Nothing is shared or synced between them after the copy is made at startup.

## Watch the environment

- Use **env** to see the current shell environment
- 

## Behaviour of a shell variable

- Create a new variable:  
**nyname=\<my name\>**  
(in my case:  myname=yuval)
- Run a new shell:  
**bash**
- View your variable:  
**echo $myname**
- Why can't you see the variable?
- Exit from the new shell, back to the previous shell:  
**exit**
- View your variable again:  
**echo $myname**


## Export a variable to environment

- Export your variable to environment:  
**export myname**
- Run a new shell (again):  
**bash**
- View your variable:  
**echo $myname**


# Environment Variables

## What is an environment variable?

- A **shell variable** lives only inside the current shell. If that shell starts a child process (a new shell, a script, a program), the child does **not** see it.
- An **environment variable** is a shell variable that has been marked "for export". When a child process starts, it gets a **copy** of all of its parent's environment variables — that's how programs pass settings down to the processes they launch (e.g. **PATH**, **HOME**, **LANG**).
- So: every environment variable is a shell variable, but not every shell variable is an environment variable. Exporting is what turns one into the other.
- The copy only flows **downward**, parent → child. A child can change or export its own variables, but that never affects the parent shell it came from.

## How do you see them?

- **env** or **printenv** — list only the variables that are exported (i.e. the actual environment)
- **printenv VARNAME** — print one specific environment variable
- **echo $VARNAME** — print the value of any variable (shell or environment) by name
- Note: **set** also shows exported variables, but mixed in with plain shell variables and functions — it's not filtered to just the environment. See [shell_vars.md](shell_vars.md) for details.

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

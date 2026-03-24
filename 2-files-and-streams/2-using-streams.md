

#### Streams

- Initially, bash has these streams:
  - 0 (keyboard input)
  - 1 standard output (terminal)
  - 2 standard error (also terminal)
- You can add a stream to bash using the **exec** built-in:
```
exec 3< file.txt
```
and now 3 represents this file.


#### Redirections

- You can redirect input
- Choose a program that reads from the standard input, like **grep**:
```
grep bla < myfile.txt
```
- Redirect output.
- Choose a program that write to stdout:
```
ls / > files
```


#### pipelines

# Stream and Redirection Exercises

## Overview
These exercises will help you understand Linux streams and redirection. Each exercise focuses on a specific concept. Before attempting these exercises, make sure you understand the basic concepts of stdin (0), stdout (1), and stderr (2).

## Prerequisites
- Access to a Linux/Unix terminal
- Basic understanding of command line operations
- Text editor of your choice

## Exercises

1. **Basic Output Redirection**
   Create a command that will write "Hello World" to a file named output.txt instead of displaying it on the screen.

2. **Append Operation**
   Write a command that will add a new line of text to output.txt WITHOUT overwriting its existing contents.

3. **Error Stream**
   Use the grep command to search for a pattern in a nonexistent file. Redirect only the error message to a file named errors.log.

4. **Dual Redirection**
   Use the ls command with a mixture of existing and nonexistent files. Direct the successful listings to "success.txt" and errors to "errors.txt".

5. **Combining Streams**
   Create a command that will combine stderr and stdout into a single output stream and save it to a file.

6. **Discarding Output**
   Write a command that performs a file search but sends all output (both standard and error) to nowhere.

7. **Multi-line Input**
   Create a three-line text file using a here document (hint: use EOF markers).

8. **File Input**
   Write a command that takes its input from a file instead of keyboard input.

9. **Pipe Practice**
   Create a command that converts text to uppercase using a pipe.

10. **Custom File Descriptor**
    Open a custom file descriptor (3) and write some text to it.

11. **Multiple Destinations**
    Write a command that sends output to both a file AND displays it on the screen.

12. **Advanced Stream Combining**
    Create a command that:
    - Lists directory contents
    - Includes both successful and error output
    - Filters the combined output for specific text
    - Saves the result to a file

## Challenge Exercise
Create a series of commands that demonstrates using all three standard streams (stdin, stdout, stderr) in a single command line operation.

## Testing Your Work
For each exercise:
1. Try your solution
2. Verify the output files created
3. Check if error messages are handled as expected
4. Ensure no unintended side effects occurred

## Common Mistakes to Watch For
- Forgetting to close file descriptors
- Mixing up the order of redirection operators
- Not accounting for spaces in file names
- Incorrect stream numbers
- Forgetting to handle error cases

## Tips
- Always verify the contents of your output files
- Use 'cat' to check file contents
- Remember the difference between > and >>
- Pay attention to the order of redirection operators
- Test with both existing and nonexistent files

Turn in your solutions as a single script file with each exercise clearly marked and commented.
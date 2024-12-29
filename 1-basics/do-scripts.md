# Basic Script Creation Exercises

## Prerequisites
- Text editor
- Terminal access
- Basic understanding of Linux/Unix commands
- Understanding of file permissions

## General Instructions
For each exercise:
1. Create the script file using a text editor
2. Add the appropriate shebang line
3. Save the file with a meaningful name
4. Make the script executable
5. Test the script
6. Document any errors you encounter

## Exercises

1. **Hello World Script**
   - Create a script that prints "Hello, World!" to the screen
   - Name it hello.sh
   - Use the bash shell shebang
   - Make it executable
   - Run it using both relative and absolute paths

2. **Current Date Display**
   - Create a script that shows today's date
   - Name it showdate.sh
   - Use the appropriate command to display the date
   - Make it executable
   - Test it from different directories

3. **System Information**
   - Create a script that displays:
     * Your username
     * Current directory
     * Home directory
   - Name it sysinfo.sh
   - Make it executable
   - Run it from various locations

4. **File Creation**
   - Create a script that:
     * Creates a new directory called "test_dir"
     * Creates a text file inside with your username
   - Name it mktest.sh
   - Make it executable
   - Test it multiple times to ensure it works repeatedly

5. **Current Directory Contents**
   - Create a script that:
     * Shows all files in the current directory
     * Shows their sizes
     * Shows their permissions
   - Name it dirinfo.sh
   - Make it executable
   - Test it in different directories

6. **System Path**
   - Create a script that:
     * Displays the system PATH variable
     * Shows one path per line
   - Name it showpath.sh
   - Make it executable
   - Compare output with echo $PATH

7. **Machine Info**
   - Create a script that displays:
     * Hostname
     * Operating system name
     * Kernel version
   - Name it machine.sh
   - Make it executable
   - Document the commands used

8. **Disk Space**
   - Create a script that shows:
     * Available disk space
     * Used disk space
     * In human-readable format
   - Name it diskinfo.sh
   - Make it executable
   - Test on different filesystems

## Common Mistakes to Watch For
- Forgetting the shebang line
- Not making the script executable
- Using Windows line endings
- Incorrect file permissions
- Using spaces in script names
- Not using full paths when needed

## Tips
- Always test scripts with 'bash -x' for debugging
- Keep a backup of working scripts
- Use meaningful script names
- Comment your code
- Test scripts in different directories

## Expected Learning Outcomes
After completing these exercises, you should be able to:
- Create basic shell scripts
- Use appropriate shebang lines
- Set correct file permissions
- Execute scripts from any location
- Use basic Linux/Unix commands in scripts
- Understand script naming conventions
- Debug basic script issues

## Submission Requirements
- Submit all scripts in a single directory
- Include a README file explaining each script
- Ensure all scripts are executable
- Include any error messages encountered
- Document any system-specific requirements
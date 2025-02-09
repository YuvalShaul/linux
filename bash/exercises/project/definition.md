###     System Administration Tool
### ===================================

####  Basic Structure

Create a main script file with a .sh extension
Add a shebang line: #!/bin/bash
Make the script executable
Implement error checking for root privileges
Create a main menu function

#### System Health Monitoring

Create a function to:

Display CPU usage using top command  
Show memory usage using free command  
Display disk usage using df command  
Show system uptime  
Display recent system logs  



#### User Management

Implement functions for:  

Listing all system users  
Adding new users  
Deleting users  
Modifying user properties  


Include proper error handling
Add input validation

Backup Functionality

#### Create a backup function

Accepts source and destination directories
Validates directory existence
Creates timestamped backups
Uses tar for compression
Implements error checking
Provides success/failure messages



#### Log Analysis


View system logs
Search through log files
Display authentication logs
Show service-specific logs


Add filtering options
Include error handling for missing files

#### Service Management

Create functions to:

List running services
Start services
Stop services
Restart services

Implement status checking
Add error handling
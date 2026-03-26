## Linux Users

Linux, everything—from people to background services—needs an identity to interact with the system.

#### Users Properties

Every user account is defined by a specific set of attributes. When you create a user, the system assigns:
- **Username**: The human-readable name (e.g., jane).
- **UID (User ID)**: A unique number the system actually uses to track you.
  - **0**: Reserved for the root user.
  - **1-999**: Usually reserved for system accounts (services like databases).
  - **1000+**: Standard human users.
- **GID (Group ID)**: The ID of the user's primary group.
- **Home Directory**: The user's personal "sandbox" (usually /home/username).
- **Default Shell**: The command-line environment that opens when they log in (usually /bin/bash or /bin/zsh).

Linux doesn't hide this info in a secret registry; it’s stored in simple text files that you can actually read.

|  File |  Purpose |
|---------------- | -----------------|
|/etc/passwd | Contains the main user details (Username, UID, GID, Home Dir, Shell).|
|/etc/shadow | Stores encrypted passwords and account aging info (only readable by root for security).| 
| /etc/group |  Defines groups and lists which users belong to them. |
| /etc/sudoers | Defines who has "admin" privileges.|

## Linux Course - Final Exercise
This final exercise is designed to test your ability to manage networking, users, and file systems within a Linux environment.  
Good luck!

---

### **Part 1: Environment Setup & User Management**
1.  **Virtualization:** Deploy two separate instances of Ubuntu within your virtualization software. 
2.  **Network Isolation:** Configure the network adapter for both systems to a "Host-Only" or "Custom" private segment. This ensures they can communicate with each other but are completely isolated from your physical network and the internet.
3.  **Identity:** On both machines, create a new standard user account. Switch to this user for the remainder of the exercise (using elevated privileges only when necessary).

---

## **Part 2: Network Configuration**
4.  **Addressing:** Using the built-in system network management tool, assign a **static IP address** to each machine. 
    * *Constraint:* Use a different subnet than the one automatically provided by your virtualization software.
    * This should survive linux reboot, so be persistent.
5.  **Connectivity:** Verify that the two systems can see each other over this private network.

---

## **Part 3: Service Deployment & Communication**
6.  **Web Server:** On the first system, install and activate the popular high-performance web server. This should be done using containers.
7.  **Remote Request:** From the second system, perform a manual request to the first system’s IP address to retrieve the default landing page data.
8.  **Data Capture:** Instead of just viewing the results in your terminal, redirect the entire content of that request into a new file named `server_output.txt`.

---

## **Part 4: Data Analysis & File Operations**
9.  **Content Verification:** Compose a single-line instruction that searches through the `server_output.txt` file to confirm whether the name of the web server software appears in the text.
10. **Security:** Modify the file’s metadata so that only the owner has the ability to read and write to it, removing all access for groups and other users.
11. **Referencing:** Create a symbolic shortcut (soft link) in your home directory that points to `server_output.txt`.

---

### **Success Criteria**
* Both systems communicate on a non-default private subnet.
* The `curl` equivalent works without internet access.
* The soft link correctly opens the captured data.
* The search instruction successfully identifies the software string.
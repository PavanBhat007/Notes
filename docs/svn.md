# **SVN** (Apache **S**ub**V**ersio**N**)
###### Reference: [TutorialsPoint](https://www.tutorialspoint.com/svn/svn_environment.htm)

## **Terminologies**
| Terminology | Description | 
|:---------------:|:---------------------------------------------------------------|
| **Repository** | ***central storage where files*** and also, history is stored. <br> Repository &rarr; server, Version Control tool &rarr; client |
| **Trunk** | directory where all the ***main development happens***. | 
| **Tags** | tags directory is used to ***store named snapshots of the project***. <br> give descriptive and memorable names to specific version in the repository. |
| **Branch** | operation is used to ***create another line of development***. |
| **Working copy** | ***snapshot of the main repository***. <br> shared by all the teams, but not modified directly. <br> private and isolated workplace. |
| **Commits** | ***process of storing changes from private workplace to central server***. <br> after commit, changes are made available to all the team, which can retrieved by updating their working copy.|

<hr>

## **SVN Installation**
To check version if already installed: `$ svn --version`

| For RPM-Based Linux | For Debian-based Linux |
| --------------------|------------------------|
| `$ su -` <br>`Password:` <br> `# yum install subversion` | `$sudo apt-get update` <br> `sudo apt-get install subversion`

<hr>

## **Apache Setup**
**Apache httpd** module and **svnadmin** tool must be installed on the server.

- `# yum install mod_dav_svn subversion`
- `mod_dav_svn` package allows access to a repository using HTTP, via **Apache httpd server**
- **subversion** package installs **svnadmin** tool.

<br>

SVN reads its configuraton from `/etc/https/conf.d/subversion.conf` file that looks like

```title="subversion.conf" linenums="1"
LoadModule dav_svn_module     modules/mod_dav_svn.so
LoadModule authz_svn_module   modules/mod_authz_svn.so

<Location /svn>
   DAV svn
   SVNParentPath /var/www/svn
   AuthType Basic
   AuthName "Authorization Realm"
   AuthUserFile /etc/svn-users
   Require valid-user
</Location>
```

<br>

`htpasswd` command is used to create and update the plain-text files which are used to store usernames and passwords for basic authentication of HTTP users. 

- `-c` option creates new password file, and if already exists, then is overwritten.
- `-m` option enables MD5 encryption for passwords.

<hr>

## **User Setup**

### Adding users
```title="adding a user" linenums="1"
# htpasswd -cm /etc/svn-users pavan
New password:
Re-type new password:
Adding password for user pavan
```

### Creating SVN parent directory to store all work
```title="creating parent dir" linenums="1"
[pavan@Kali ~ ]# mkdir /var/www/svn
[pavan@Kali /var/www/svn ]# cd /var/www/svn
```

<hr>

## **Repository Setup**

### Create a project repository
svnadmin command will create a new repository and few other directories in it to store metadata.
```title="creating a repo" linenums="1"
[pavan@Kali ~ ]# svnadmin create project_repo

[pavan@Kali ~ ]# ls -l project_repo
total 24
drwxr-xr-x. 2 root root 4096 Aug  4 22:30 conf
drwxr-sr-x. 6 root root 4096 Aug  4 22:30 db
-r--r--r--. 1 root root    2 Aug  4 22:30 format
drwxr-xr-x. 2 root root 4096 Aug  4 22:30 hooks
drwxr-xr-x. 2 root root 4096 Aug  4 22:30 locks
-rw-r--r--. 1 root root  229 Aug  4 22:30 README.txt
```

### Changing ownerships
```title="chown" linenums="1"
[pavan@Kali ~ ]# chown -R apache.apache project_repo/
```


### Allow commits over HTTP
```title="chcon" linenums="1"
[pavan@Kali ~ ]# chcon -R -t https_sys_rw_content_t /var/www/svn/project_repo/
```


### Restart server
Restart Apache server to finish configuration of the server.
```title="restarting server" linenums="1"
[pavan@Kali ~ ]# service https restart
Stopping httpd:                                            [FAILED]
Starting httpd: httpd: apr_sockaddr_info_get() failed for CentOS
httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName
                                                           [  OK  ]
[pavan@Kali ~ ]# service https status
httpd (pid  1372) is running...                                                       
```

<hr>

## **Repository Configuration**
To provide access to only authentic users and to use default authorization file, following lines must be appended to `project_repo/conf/svnserve.conf`
```title="access control and authorization" linenums="1"
anon-access = none
authz-db = authz
```

### Creating trunk, tags and branches directories
```title="trunk, tags, branches directory structure" linenums="1"
[pavan@Kali ~ ]# mkdir /tmp/svn-template
[pavan@Kali ~ ]# mkdir /tmp/svn-template/trunk
[pavan@Kali ~ ]# mkdir /tmp/svn-template/branches
[pavan@Kali ~ ]# mkdir /tmp/svn-template/tags
```

### Import directories
```title="importing directories from /tmp/svn-template to repo" linenums="1"
[pavan@Kali ~ ]# svn import -m 'Create trunk, branches, tags structure' /tmp/svn-template
Adding         /tmp/svn-template/trunk
Adding         /tmp/svn-template/branches
Adding         /tmp/svn-template/tags
Committed revision 1.
```

<hr>

## **SVN Operations / Life Cycle**

| **Operation** | **Command** | **Description** |
|:-------------:|-------------|-----------------|
| Create | `create` | creates a new repository |
| Checkout | `checkout` | creates a working copy from the repository |
| Update | `update` | updates working copy <br> synchronizes working copy with the repository. |
| Add files | `add` |  SVN shows A before file, it means, the file is successfully added to the pending change-list. |
| Store file/ Commit | `commit` | use the commit command to store added files; with `-m` option followed by commit message.<br>if `-m` option is omitted SVN will bring up the text editor where you can type a multi-line message. |

### **Checkout Process**
Below command will create a new directory in the current working directory with the name project_repo.
```
[pavan@Kali ~]$ svn checkout http://svn.server.com/svn/project_repo --username=pavan
A    project_repo/trunk
A    project_repo/branches
A    project_repo/tags
Checked out revision 1.
```

After every successful checkout, revision no: printed.
To view more information about repository,
```title="view more info about repo" linenums="1"
[pavan@Kali ~]$ pwd
/home/tom/project_repo/trunk

[pavan@Kali ~]$ svn info
Path: .
URL: http://svn.server.com/svn/project_repo/trunk
Repository Root: http://svn.server.com/svn/project_repo
Repository UUID: 7ceef8cb-3799-40dd-a067-c216ec2e5247
Revision: 1
Node Kind: directory
Schedule: normal
Last Changed Author: pavan
Last Changed Rev: 0
Last Changed Date: 2023-09-23 18:15:52 +0530 (Sat, 23 Sept 2023)
```

### **Performing changes**

Suppose, the user pavan creates an `array.c` file.
```title="simulating dev behaviour" linenums="1"
[pavan@Kali ~]$ cd project_repo/trunk/
[pavan@Kali trunk]$ cat array.c
```

```c title="array.c" linenums="1"
#include <stdio.h>
#define MAX 16

int main(void) {
   int i, n, arr[MAX];
   printf("Enter the total number of elements: ");
   scanf("%d", &n);

   printf("Enter the elements: \n");

   for (i = 0; i < n; ++i)
        scanf("%d", &arr[i]);

   printf("Array has following elements: ");
   for (i = 0; i < n; ++i)
        printf("|%d| ", arr[i]);
   
   printf("\n");
   return 0;
}
```

Running program before commiting,
```title="testing" linenums="1"
[pavan@Kali trunk]$ make array
cc        array.c      -o array

[pavan@Kali trunk]$ ./array
Enter the total number of elements: 5
Enter the elements: 1 2 3 4 5
Array has following elements:  |1| |2| |3| |4| |5| 
```

Checking status,
```title="check file status" linenums="1"
[pavan@Kali trunk]$ svn status
?       array.c
?       array
```
The **?** denotes that SVN doesn't know what to do with these files. The files must be **add**ed to pending change-list first.
```title="adding files" linenums="1"
[pavan@Kali trunk]$ svn add array.c
A         array.c
```
The **A** denotes that the files has been added to the pending change-list successfully.
```
[pavan@Kali trunk]$ svn status
?       array
A       array.c
```


Commiting added files
```title="commit" linenums="1"
[pavan@Kali trunk]$ svn commit -m "Initial Commit"
Adding         trunk/array.c
Transmitting file data .
Committed revision 2.
```
Now, `array.c` has been added to repository and revision number is incremented.


### **Reviewing changes and modifying code**
After adding `array.c` if another user, say navap checks-out the repo,
```
[navap@Kali ~]$ svn co http://svn.server.com/svn/project_repo --username=navap
A    project_repo/trunk
A    project_repo/trunk/array.c
A    project_repo/branches
A    project_repo/tags
Checked out revision 2.
```

Logging changes
```title="logging" linenums="1"
[navap@Kali trunk]$ svn log
------------------------------------------------------------------------
r2 | pavan | 2023-09-24 20:51:43 +0530 (Sat, 23 Sept 2023) | 1 line

Initial Commit
------------------------------------------------------------------------
r1 | pavan | 2023-09-24 23:01:08 +0530 (Sun, 23 Sept 2023) | 1 line

Create trunk, branches, tags directory structure
------------------------------------------------------------------------
```

navap wants makes some changes to pavan's code. He changes to code to add an array overflow condition
```c title="array overflow condition added to array.c" linenums="1"
/* handle array overflow condition before taking in elements*/
   if (n > MAX) {
      fprintf(stderr, "Number of elements must be less than %d\n", MAX);
      return 1;
   }

```
```title="check pending change-list" linenums="1"
[navap@Kali trunk]$ svn status
M       array.c
```
`array.c` was modified, and is denoted by the **M**.

Double checking changes made,
```c title="reviewing changes" linenums="1"
[navap@Kali trunk]$ svn diff
Index: array.c
===================================================================
--- array.c   (revision 2)
+++ array.c   (working copy)
@@ -9,6 +9,11 @@
    printf("Enter the total number of elements: ");
    scanf("%d", &n);
 
+   if (n > MAX) {
+      fprintf(stderr, "Number of elements must be less than %d\n", MAX);
+      return 1;
+   }
+
    printf("Enter the elements\n");
 
    for (i = 0; i < n; ++i)
```
**+** is shown before any **new lines** that have been added, and **-** before any lines that were removed.

```title="committing modifications" linenums="1"
[navap@Kali trunk]$ svn commit "Fix array overflow"
Sending        trunk/array.c
Transmitting file data .
Committed revision 3.
```

### **Update Process**
Now if pavan wants to change his code (not knowing navap has alreadu modified the code once), to
```c title="pavan's modified array.c" linenums="1"
#include <stdio.h>
#define MAX 16

void accept_input(int *arr, int n) {
   int i;
   for (i = 0; i < n; ++i) 
    scanf("%d", &arr[i]);
}

void display(int *arr, int n) {
   int i;
   for (i = 0; i < n; ++i) 
    printf("|%d| ", arr[i]);
   
   printf("\n");
}

int main(void) {
   int i, n, arr[MAX];

   printf("Enter the total number of elements: ");
   scanf("%d", &n);

   printf("Enter the elements\n");
   accept_input(arr, n);

   printf("Array has following elements\n");
   display(arr, n);

   return 0;
}
```

Reviewing changes,
```c title="checking diff" linenums="1"
[pavan@Kali trunk]$ svn diff
Index: array.c
===================================================================
--- array.c   (revision 2)
+++ array.c   (working copy)
@@ -2,6 +2,24 @@
 
 #define MAX 16
 
+void accept_input(int *arr, int n)
+{
+   int i;
+
+   for (i = 0; i & n; ++i)
+      scanf("%d", &arr[i]);
+}
+
+void display(int *arr, int n)
+{
+   int i;
+
+   for (i = 0; i < n; ++i)
+      printf("|%d| ", arr[i]);
+   
+   printf("\n");
+}
+
 int main(void)
 {
    int i, n, arr[MAX];
@@ -10,15 +28,10 @@
    scanf("%d", &n);
 
    printf("Enter the elements\n");
+   accept_input(arr, n);
 
-   for (i = 0; i < n; ++i)
-      scanf("%d", &arr[i]);
-
    printf("Array has following elements\n");
-   for (i = 0; i < n; ++i)
-      printf("|%d| ", arr[i]);
-   
-   printf("\n");
+   display(arr, n);
 
    return 0;
 }
```

Commiting changes,
```title="pavan's commit"
[pavan@Kali trunk]$ svn commit -m "Add function to accept input and to display array contents"
Sending        trunk/array.c
svn: Commit failed (details follow):
svn: File or directory 'array.c' is out of date; try updating
svn: resource out of date; try updating
```

SVN is not allowing to commit pavan's changes, because navap has already modified the repository and pavan's working copy is out of date. To avoid overwriting each other's changes, SVN fails this operation. pavan must update working copy before committing his changes. So he uses update command as shown below.
```title="update command"
[pavan@Kali trunk]$ svn update
G    array.c
Updated to revision 3.
```
**G** denotes that the updates/versions have been merged.

Commiting changes after updating working copy,
```title="updated commit"
[pavan@Kali trunk]$ svn commit -m "Add function to accept input and to display array contents"
Sending        trunk/array.c
Transmitting file data .
Committed revision 4.
```
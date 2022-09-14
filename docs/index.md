# Introduction

[Duplicacy](https://duplicacy.com) is state-of-the-art backup tool that has extensive cloud support. It also supports local disks and your own **sftp** servers.     

Duplicacy is available as a web-based GUI or as a command line tool. 
The software does require a license but the **command-line interface** (CLI) version is free for personal use.  
  
We’ll be using the **CLI** version for this tutorial.

# Installation    

**STEP 1:**   

Pre-compiled binaries are available for Linux, macOS, and Windows directly from the Duplicacy [GitHub](https://github.com/gilbertchen/duplicacy/releases) repository.

Download the latest version for your system. Currently this is `version 2.7.2`.
~~~
$ wget https://github.com/gilbertchen/duplicacy/releases/download/v2.7.2/duplicacy_linux_x64_2.7.2
~~~

**STEP 2:**    

Make the file executable.
~~~
$ chmod +x duplicacy_linux_x64_2.7.2
~~~
   
**STEP 3:**  

Rename the file to **duplicacy**.
~~~
$ mv duplicacy_linux_x64_2.7.2 duplicacy
~~~

**STEP 4:**  

Change the file permissions.
~~~
$ chmod 755 duplicacy
~~~

**STEP 5:**  

Move the file to the **/usr/bin** directory.
~~~
$ sudo mv duplicacy /usr/bin/
~~~

# Initialize the remote storage and repository

The **duplicacy init** command is used to initialize the remote storage and the backup directory.
~~~
duplicacy init [command options] <snapshot id> <storage url>
~~~
The `<snapshot id>` refers to the name you want to give to your backup job.    
The `<storage url>` refers to the remote server and directory path for your backups.

We will only be using the following options:
~~~
-encrypt, -e          encrypt the storage with a password
-storage-name <name>  assign a name to the storage
-repository <path>    initialize a new repository at the specified path
~~~

An example scenario will make things easier to understand.

1. Sftp server hostname: **nacho.local**.
2. Directory to be backed up: **/home/curt/Photos**.
3. Backup directory on the sftp server: **photobackup**. This refers to the `-repository <path>` option.
4. Backup job name: **photoBackup**. This refers to the `<snapshot id>` option.
5. Remote storage name: **nachostorage**. This refers to the `-storage-name <name>` option.

For me, **sftp://curt@nacho.local/photobackup** would be the complete `<storage url>`.    

**STEP 1:**      

In duplicacy, the `repository` refers to the directory that you want to backup. Run the **duplicacy init** command to initialize the remote storage and the backup respository. Here is an example:
~~~
$ duplicacy init -e -storage-name nachostorage -repository /home/curt/Photos photoBackup sftp://curt@nacho.local/photobackup
~~~
You will see the following prompts:
~~~
Enter SSH password:
Enter the path of the private key file:
Enter storage password for sftp://curt@nacho.local/photobackup:
Re-enter storage password:
~~~

1. Hit `enter` to leave the password blank if you use **ssh** keys.       
2. Hit `enter` to bypass specifying the path to your private **ssh** key. This will be configured later to automate this.   
3. Type in a storage password you wish to use.

**STEP 2:**    

**Duplicacy** creates a configuration folder named **.duplicacy** in the current directory after the initialization is finished.   

Edit the **.duplicacy/preferences** file with a plain text editor to change the **"keys":**&nbsp; line.    

We'll be specifying the path to our private **ssh** key and **storage password** that was used for the initialization.
~~~
$ vim .duplicacy/preferences
~~~

Default setting:
~~~
"keys": null,
~~~

Using the following format, change the line to include your **storage password** and full path to your private **ssh** key. This is what it would look like for me:
~~~
 "keys": {
            "password": "MySecretPassword",
            "ssh_key_file": "/home/curt/.ssh/id_rsa"
        },
~~~

# Running the backup

**STEP 1:**    

Run the **duplicacy backup** command.
~~~
$ duplicacy backup -stats
~~~

# Restoring backups    

**STEP 1:**

Run the **duplicacy restore** command to restore files from your backups.

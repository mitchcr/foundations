# Module 2 - OS Fundamentals II Laboratories

##  Lab 6 - Hard and soft links

### About Lab 6

- It will take approximately 20 minutes.
- This lab provides hands-on activities.
- After this lab you will be able to:
  - How to create a soft link and check its properties

### Scenario 1: Creating a soft link

#### Overview Lab 6 Scenario 1 

Here are some of the soft links properties
- Have the capability to cross the file system.
- Allow you to link between directories.
- Have different inodes number and file permissions than original file. 
- Have only the path of the original file, not the contents (contents stay in the original file).

#### Instructions Lab 6 Scenario 1

Step 1: Create empty directory
- Connect to any Linux VM or connect to foundationvm1 VM created in previous module and create an empty directory with name ```softdir```
  ``` bash
  mkdir softdir
  ```

Step 2: Change directory
- Change to the ```softdir``` directory
  ``` bash
  cd softdir
  ```

Step 3: Create new file
- Create a new file with name ```source.file``` with some data
   ``` bash
   echo "Microsoft<3Linux" > source.file
   ```
    
Step 4: Show the file ```source.file```
- Check the contents of the ```source.file``` file
  ``` bash
  cat source.file
  ```

Step 5: Create symbolic link
- Now that the source file has been created let's proceed to create a symbolic or soft link of the source file
  ``` bash
  ln -s source.file softlink.file
  ```

Step 6: Show the soft link file
- Check the contents of the newly created ```softlink.file```
  ``` bash
  cat softlink.file
  ```

  > As you see in the above output, "softlink.file" displays the same contents that we have in the "source.file"

Step 7: Working with permissions of the source and soft link files
- Check the inodes and permissions of ```softlink.file``` and ```source.file```
  ``` bash
  ls -li
  ```
   - The first column corresponds to the inode number/ID of the file.  A soft link is an independent file, with its own inode ID but pointing to another file
   - Even though the ```softlink.file``` displays the same contents as ```source.file``` when we use ```ls```, the inode numbers and file permissions differ
   - The permissions of the soft link cannot be modified. If they are set to change, it is the original file that will be affected.
   - You can test this by changing the permissions of the ```softlink.file``` to 755, the original file will change from 664(```-rw-rw-r--```) to 775(```-rwxrwxr-x```) and the ```softlink.file``` permissions will remain the same.
     - Get the current permissions of the ```softlink.file``` (first column)
       ``` bash
       ls -l softlink.file
       ```
     - Get the current permissions of the ```source.file``` (first column)
       ``` bash
       ls -l source.file
       ```
     - Set the permissions to 755(rwx-rwx-r-x)
       ``` bash
       chmod 755 softlink.file
       ```
     - Confirm that the permission of ```softlink.file``` didn't change, but the ones from ```source.file``` did
       ``` bash
       ls -l softlink.file
       ls -l source.file
       ```

Step 8: Break the source file
- Remove the original file (i.e. ```source.file```) and see what happens
  - Delete the ```source.file```
    ``` bash 
    rm -f source.file
    ```
  - Validate the ```softlink.file``` by listing it and trying to see the contents of the file
    ``` bash
    ls -li
    cat softlink.file
    ```
  - The soft-link file exists as you can see when it's listed using ```ls -l```, but it's currently pointing to a file that no longer exists (it's a soft link with a null pointer), hence the contents can no longer be displayed. 
    > So, now we understand that a soft link is just a link that points to the original file. The soft link is like a shortcut to a file. If you remove the file, the shortcut is useless, but if you remove the soft link, the original file will still be present.
  - Create the source file again to activate the link.
    ``` bash
    touch source.file
    ls -l
    ```
    > This will be a new source file and the existing contents cannot be recovered
  - Unlink the softlink
    ``` bash
    unlink softlink.file
    ls -l
    ```

### Scenario 2: Creating a hard link

#### Overview Lab 6 Scenario 2

Here are some of the hard links properties
- Can't cross the file system boundaries.
- Can't link directories, only files.
- Have the same inode number/ID and permissions of the original file, which means they represent the same file.
- Share the same attributes than the original file, like contents and permissions. 
    - If we change the permissions of the original file, they get reflected in the hard link as well.
    - Share the same contents than the original file (they are the same file). 
- If the original file is removed or moved to another location, the hard link will still preserve the original contents and attributes. 

#### Instructions Lab 6 Scenario 2 

Step 1: Get a new file
- Create a file called ```source.file``` and verify its contents
  ``` bash 
  echo "Welcome to Linux" > source.file
  cat source.file
  ```

Step 2: Create a hard link
- Create ```hardlink.file``` that points to ```source.file```
  ``` bash
  ln source.file hardlink.file
  ```

Step 3: Show the content ofr both files
- Using ```cat``` with ```hardlink.file``` confirm it is the same content as ```source.file```
  ```` bash
  cat hardlink.file
  ```

Step 4: Check the details of both files
- Compare inodes and permissions for both source and hard link files
  ``` bash 
  ls -li
  ```
  > Both files share the same inode number and permissions. Any changes to the permissions of source.file will also apply to hardlink.file.

Step 5: Delete the source file
- Remove the original file ```source.file``` and see what happens. 
  ``` bash
  rm -f source.file
  ls -li
  cat hardlink.file
  ```

> As an additional exercise, you can attach a disk, create a filesystem, mount it, and try to create a hard link. Remember, hard links can’t span different filesystems because inode IDs are unique to each filesystem. A hard link is just a reference to the same inode as the original file, so it can only be created within the same filesystem. On the other hand, soft links are separate files that point to another file, allowing them to be created across different filesystems.

##  Lab 7 - Managing Services

### About Lab 7

- It will take approximately 30 minutes.
- This lab provides hands-on activities.
- After this lab you will be able to:
  - How to use system D as a system manager to manage services, check statuses, and change system states.

#### Deployment Lab 7

Step 1: Deploy a Red Hat VM
- Using the link below, please fill the required options identified with a `*`. Use this new VM for this lab.

  [![Click to deploy](https://aka.ms/deploytoazurebutton)](https://labboxprod.azurewebsites.net/api/Labbox?url=https://dev.azure.com/linuxninjas/azure%20linux%20academy%20-%20css/_git/azurelinuxacademy?path=/azure%20linux%20academy/azure_linux_foundation/module%202%20-%20os%20fundamentals%20ii/labs/lab07rhel.json) 


### Scenario 1: System D

#### Instructions Lab 7 

Step 1: Listing all units
- Using the command ```systemctl``` list the state of all SystemD units
  ``` bash
  systemctl
  ```
  <details>
    <summary>Screenshot: list state of all units at system startup</summary>

    ![Check for updates.](./.attachments/lab7img7.png =950x)
  </details>

Step 2: List units of system type
- List the state of only the service units
  ``` bash
  systemctl list-units --type=service --all
  ```
  <details>
    <summary>Screenshot: list state of service units</summary>

    ![Check for updates.](./.attachments/lab7img8.png =950x)
  </details>

Step 3: Check the failed unit
- Investigate any unit which are in a failed or maintenance state, for example: 
  ``` bash
  systemctl list-units --type=service --all|grep -i fail  #This command will show you all the failed systemd units, search for any to use in the next command.
  systemctl status MsftLinuxPatchAutoAssess.service       #This is just an example, you can change the systemd unit name here.
  ```
  <details>
    <summary>Screenshot: check status of a failed unit</summary>

    ![Check for updates.](./.attachments/lab7img9.png =950x)
  </details>


Step 4: Check active units
- List all loaded units with active state
  ``` bash
  systemctl list-units --state=active
  ```
  <details>
    <summary>Screenshot: list active units</summary>

    ![Check for updates.](./.attachments/lab7img10.png =950x)
  </details>

Step 5: List running units
- List all current running services
  ``` bash
  systemctl list-units --type service --state running
  ```
  <details>
    <summary>Screenshot: list running services</summary>

    ![Check for updates.](./.attachments/lab7img11.png =950x)
  </details>

Step 6: View all units
- View enabled and disabled settings for all units
  ``` bash
  systemctl list-unit-files
  ```
  <details>
    <summary>Screenshot: list settings for all units</summary>

    ![Check for updates.](./.attachments/lab7img12.png =950x)
  </details>

Step 7: List failed units
- View only failed services
  ``` bash
  systemctl --failed --type=service
  ```
  <details>
    <summary>Screenshot: list failed servicests</summary>

    ![Check for updates.](./.attachments/lab7img13.png =950x)
  </details>

Step 8: Working with a systemd unit
- Check on the status of the waagent, stop the service and then start it
  ``` bash
  systemctl status waagent.service
  systemctl stop waagent.service
  systemctl start waagent.service
  ```
  <details>
    <summary>Screenshot: checking, stopping and starting a service</summary>

    ![Check for updates.](./.attachments/lab7img14.png =950x)
  </details>

Step 9: How to check if an unit is enabled
- Determine if the waagent service is enabled at system boot
  ``` bash
  systemctl list-unit-files | grep waagent
  ```
  <details>
    <summary>Screenshot: checking if a service is enabled at system boot</summary>

    ![Check for updates.](./.attachments/lab7img15.png =950x)
  </details>

Step 10: Disabling an unit
- Disable waagent from starting at system boot and check using previous command
  ``` bash
  systemctl disable waagent
  systemctl list-unit-files | grep waagent
  ```
  <details>
    <summary>Screenshot: disable a service and check</summary>

    ![Check for updates.](./.attachments/lab7img16.png =950x)
  </details>

Step 11: Enabling an unit
- Enable waagent and verify that the service is up at the next boot
  ``` bash
  systemctl enable waagent
  systemctl list-unit-files | grep waagent
  ```
  <details>
    <summary>Screenshot: enable a service and check</summary>

    ![Check for updates.](./.attachments/lab7img17.png =950x)
  </details>

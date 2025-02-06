[[_TOC_]]

# Module 4 - Linux on Azure Laboratories

##  Lab 9 - Linux on Azure

### About this lab

- These laboratories will take approximately 40 minutes.
- This lab provides hands-on activities.
- After this lab you will be able to
  - Check the status of the waagent
  - Check the version of the waagent
  - Locate and validate the waagent and provisioning logs
  - How the Azure Linux VM agent creates the swap file on top of the ephemeral disk
  - How to validate if the system has swap space available
  - Identify the provisioning agent in a Linux VM
  - Identify important files related to waagent and extensions

### Scenario 1: Provisioning agent in a Linux VM

#### Overview Lab 9 Scenario 1

- In this section, we will see how to identify which component is the provisioning agent
- Since Linux got implemented in Azure, WAAgent was taking care of provisioning, however as part of providing more flexibility while provisioning, cloud-init was introduced and Waagent is getting replaced with cloud-init in recent Linux distributions. Cloud-init allows you to pass custom values to a virtual machine during provisioning apart from the parameters provided by Azure Platform. However, in some distributions the provisioning is still being done with WAAgent

#### Instructions Lab 9 Scenario 1

Step 1: Check the provisioning agent
- Check the WALinux agent look for the provisioning agent by filtering the configuration file, ```/etc/waagent.conf```
  ``` bash
  grep -i provisioning /etc/waagent.conf
  ```
  Inspect the output visually to determine if it is.  If not, cloud-init is probably the provisioning agent.  Here is an example where waagent is not in charge of provisioning 
  <details>
    <summary>Screenshot: waagent.conf file filtered</summary>

    ![Check for updates.](./.attachments/lab9image1.png =950x)
  </details>

Step 2: Check cloud-init log file
- If Step 1 indicates cloud-init is the provisioning agent, check if the log file, it should be create on startup
  ``` bash
  ls -l /var/log/cloud-init*
  ```
  <details>
    <summary>Screenshot: checking cloud-config files, the vm in the example was created in November 13th</summary>

    ![Check for updates.](./.attachments/lab9image2.png =950x)
  </details>

Step 3: Check waagent provisoning logs
- In case the provisioning says "auto", filter using *grep* for provisioning lines in waagent.log
  ``` bash
  grep -i provisioning /var/log/waagent.log
  ```
  <details>
    <summary>Screenshot: checking provisioning in waagent.log</summary>

    ![Check for updates.](./.attachments/lab9image3.png =950x)
  </details>

###  Scenario 2: WAAgent and extensions

#### Overview Lab 9 Scenario 2

- Identify the version of the agent package as well the goal state agent version
- Check the status of the agent SystemD unit
- Check the logs for the linux agent and all extension

#### Instructions Lab 9 Scenario 2

Step 1: Connecting to the VM Lab 9 Scenario 2
- Use the RHEL VM created on lab 1 as well the Ubuntu VM from lab 8 and switch to root account
  ``` bash
  sudo -i 
  ```

Step 2: Check the linux agent version
- Using the command ```waagent``` get the version. Notice the 
  ``` bash
  waagent --version
  ```
  <details>
    <summary>Screenshot: linux agent version in Red Hat Enterprise Linux</summary>

    ![Check for updates.](./.attachments/lab9image4.png =950x)
  </details>
  <details>
    <summary>Screenshot: linux agent version in Ubuntu</summary>

    ![Check for updates.](./.attachments/lab9image5.png =950x)
  </details>

Step 3: Check the linux agent version package
- Check the specific package version installed using the following commands, depending on the distribution
  - Red Hat Enterprise Linux (Alma Linux, Rocky Linux or any Enterprise Linux based distribution)
    ``` bash
    rpm -qa | grep -i walinuxagent
    ```
    <details>
      <summary>Screenshot: check a package in Enterprise Linux based distribution</summary>

      ![Check for updates.](./.attachments/lab9image6.png =950x)
    </details>

  - Ubuntu (Debian or any Debian based distribution)
    ``` bash
    dpkg -l | grep walinuxagent
    ```
    <details>
      <summary>Screenshot: check a package in Debian based distribution</summary>

      ![Check for updates.](./.attachments/lab9image7.png =950x)
    </details>

Step 4: Check the status of the azure linux agent systemd unit
- Red Hat Enterprise Linux (Alma Linux, Rocky Linux or any Enterprise Linux based distribution)
  ``` bash
  systemctl status waagent
  ```
  <details>
    <summary>Screenshot: check a package status in Red Hat Enterprise Linux</summary>

    ![Check for updates.](./.attachments/lab9image8.png =950x)
  </details>

- Ubuntu (Debian or any Debian based distribution)
  ``` bash
  systemctl status walinuxagent
  ```
  <details>
    <summary>Screenshot: check a package status in Debian based distribution</summary>

    ![Check for updates.](./.attachments/lab9image9.png =950x)
  </details>

Step 5: Check the linux agent logs
- Check the *waagent* log file
  ``` bash
  cat /var/log/waagent.log
  ```
  <details>
    <summary>Screenshot: check agent log file (first part of the output)</summary>

    ![Check for updates.](./.attachments/lab9image10.png =950x)
  </details>

Step 6: Check the linux agent configuration file
- Inspect the ```/etc/waagent.conf``` file
  ``` bash
  cat /etc/waagent.conf
  ```
  <details>
    <summary>Screenshot: check agent configuration file (first part of the output)</summary>

    ![Check for updates.](./.attachments/lab9image11.png =950x)
  </details>

Step 7: Password reset using the Azure Portal
- Go to Azure Portal and perform password reset, the operation will be executed using an extension named enablevmAccess
  <details>
    <summary>Screenshot: change the password from the Azure Portal</summary>

    ![Check for updates.](./.attachments/lab9image12.png =950x)
  </details>

Step 8: Check the VMAccess extension
- Verify if the extension is getting deployed(follow image below). Once the operation is completed, login with new password.
  <details>
    <summary>Screenshot: Check the status of the extensions in the Azure Portal</summary>

    ![Check for updates.](./.attachments/lab9image13.png =950x)
  </details>

Step 9: Check the logs for the extension and the linux agent
- The extensions logs are located under ```/var/lib/wagent``` and log files are under ```/var/log/azure```. There is directory for each extension deployed. Validate the logs of each extensions, take a look at each file ```extension.log``` for each directory.
  ``` bash
  cd /var/log/azure
  ls -l
  cat Microsoft.OSTCExtensions.VMAccessForLinux/extension.log
  ```
  <details>
    <summary>Screenshot: check extension log</summary>

    ![Check for updates.](./.attachments/lab9image14.png =950x)
  </details>

###  Scenario 3: WAAgent and extensions

#### Overview Lab 9 Scenario 3

- Check the available in the system
- Enable and disable the swap through the azure linux agent

#### Instructions Lab 9 Scenario 3

Step 1: Connecting to the VM Lab 9 Scenario 3
- Connect to the *lab8ubuntu* VM created previously and switch to root account
  ``` bash
  sudo -i 
  ```
  <details>
    <summary>Screenshot: switch to root account</summary>

    ![Check for updates.](./.attachments/lab9image15.png =950x)
  </details>

Step 2: Check the available memory and swap on the system
- Check the system status related to swap and memory
  ``` bash
  swapon -s
  free -m 
  ```

  **Notes:**
  - Depending on the distribution this setting might be disabled in ```/etc/waagent.conf``` specifically on ```ResourceDisk.EnableSwap``` and ```ResourceDisk.SwapSizeMB``` parameters.
  - The swap space using both swapon and free should match.
  - If nothing is returned by the swapon command, then no swap file exists.  Otherwise it is enabled. 

  <details>
    <summary>Screenshot: check swap</summary>

    ![Check for updates.](./.attachments/lab9image16.png =950x)
  </details>

Step 3: Swap configuration on the Azure Linux Agent
- Within Azure VMs, a temporary (ephemeral) disk, usually located at /dev/disk/azure/resource-part1 (/mnt/resource), is recommended for temporary storage purposes, including swap files created by the WALinuxAgent (waagent).
  Given the checks made on step 3 for both VMs, proceed to disable swap if it is enabled, and enable if it is disabled

  ###### Enable swap
  - To create a swap file in the directory that's defined by the ```ResourceDisk.MountPoint``` parameter, you can update the ```/etc/waagent.conf``` file by setting the following three parameters as showed here
    ``` bash
    vi /etc/waagent.conf
    ```
    Modify the following parameters
    ``` bash
    ResourceDisk.Format=y
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=4096
    ```

    **Note:** Make sure the swap size isn't larger than the maximum space available in the ephemeral disk.  Cross-check the available space using ```df -h``` command.
    ``` bash
    df -h
    ``` 
    <details>
      <summary>Screenshot: check on changed values</summary>

      ![Check for updates.](./.attachments/lab9image17.png =950x)
    </details>

  - Restart the VM agent 
    -  For Debian based distributions 
       ``` bash
         systemctl restart walinuxagent.service
       ```
       <details>
         <summary>Screenshot: restart agent</summary>

         ![Check for updates.](./.attachments/lab9image18a.png =950x)
       </details>
    
    - For Red Hat Enterprise Linux based distributions and SuSE
      ``` bash
      systemctl restart waagent.service
      ```
      <details>
        <summary>Screenshot: restart agent</summary>

        ![Check for updates.](./.attachments/lab9image18b.png =950x)
      </details>

  - Validate that the swap file was created as expected
    ``` bash
    swapon -s
    free -m
    ```
    <details>
      <summary>Screenshot: check swap </summary>

      ![Check for updates.](./.attachments/lab9image19.png =950x)
    </details>

  ######  Disable Swap
  - To disable swap in the directory defined by the ```ResourceDisk.MountPoint``` parameter, update the ```/etc/waagent.conf``` file by setting the following two parameters as shown here
    ``` bash
    vi /etc/waagent.conf
    ```
    Modify the following parameters
    ``` bash
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```
    <details>
      <summary>Screenshot: check swap configuration file </summary>

      ![Check for updates.](./.attachments/lab9image20.png =950x)
    </details>

  - Restart the VM
    ``` bash
    reboot
    ```
    <details>
      <summary>Screenshot: restarting the VM</summary>

      ![Check for updates.](./.attachments/lab9image21.png =950x)
    </details>

  - Connect to the VM, switch to root account and validate the swap got disabled
    ``` bash
    sudo -i 
    swapon -s
    free -m
    ```
    <details>
      <summary>Screenshot: checking swap</summary>

      ![Check for updates.](./.attachments/lab9image22.png =950x)
    </details>

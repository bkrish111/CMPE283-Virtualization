# CMPE 283 Assignment 3

- The assignment is performed on Google Cloud VM supporting nested virtualiztion(`--enable-nested-virtualization`).
- Configuration:
  - Machine Type: **n2-standard-8**
  - CPU platform: **Intel Cascade Lake**

<br />

- Assignment 3 modifies the behavior of the **cpuid** instruction for the following cases:
  - CPUID leaf node(`%eax=0x4ffffffd`)
    - Return the total number of exits for an exit number(passed as parameter) in **%ecx**, return the value in **%eax**.
  - CPUID lead node(`%eax=0x4ffffffc`)
    - Return the total time spent processing the exit number(passed as parameter) in **%ecx**. The value is measured in cycles.
    - Return the high 32 bits of the total time spent processing for an exit number in **%ebx**.
    - Return the low 32 bits of the total time spent processing for an exit number in **%ecx**.

<br />

## Question 1:

- Bhargav Krishna Mullapudi:
    Developed the kernel.
    Installed required kernel modules in the VM.
    Understood and examined the assignment's code.
    Updated the code to incorporate discussed changes.
    Generated and compiled test files.
- Sri Anudeep Velicheti:
    Built the kernel.
    Went through the canvas lecture to grasp the required steps.
    Researched CPUID instructions and CPU leaf nodes.
    Identified the necessary changes to complete the assignment.
    Updated README.md and crafted documentation

<br />


## Question 2: (Steps used to complete the assignment)


1) Download the source code in the VM [official kernel website](https://www.kernel.org/).
    ```
    sudo apt-get install wget
    wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.0.7.tar.xz
    ```


2) If getting errors do apt update, followed by below packages

   apt install libelf-dev make flex bison libssl-dev
    ```

3) Configuring the kernel by copying the existing configuration file to a `.config` file
    ```
   cd Linux
   cp /boot/config-(OS version ie. uname -r) .config
    ```

4) Build the kernel through the following commands:
    
    - `sudo make old config`
    - `sudo make prepare`
    - `sudo make modules`

    - While compiling the kernel, an error may occur saying `No rule to make target 'debian/canonical-certs.pem'`, then execute the below commands:
      - `scripts/config --disable SYSTEM_TRUSTED_KEYS`
      - `scripts/config --disable SYSTEM_REVOCATION_KEYS`
    - `sudo make modules_install`
    - `sudo make install`

5) Bootloader is automatically updated because of the `make install` and now we can reboot the system and check the kernel version
    - `sudo reboot`
    

    ### Please refer images folder

6) Chnage the two files in the linux repo by using the files updated in the repo
   - **vmx.c**: `/linux/arch/x86/kvm/vmx/vmx.c`
   - **cpuid.c**: `/linux/arch/x86/kvm/cpuid.c` 

7) Build and installed modules again
    - `sudo make modules && sudo make modules_install`

8) For loading the newly built modules, perform following commands:
   - `sudo rmmod kvm_intel`
   - `sudo rmmod kvm`
   - `sudo modprobe kvm_intel`
   - `sudo modprobe kvm`


  
9) For testing the functionality, an nested VM was created in the GCP VM. The steps to create an nested VM are as follows:
    - Download the Ubuntu cloud image`.img`(QEMU compatible image) file from this [ubuntu cloud images site] in GCP VM(https://cloud-images.ubuntu.com/).
    ```
    wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
    ```
    - Install the required qemu packages
    ```
    sudo apt update && sudo apt install qemu-kvm -y
    ```
    -Change the password and login into the VM, perform the below commands:
    ```
    1)  sudo apt-get install cloud-image-utils

    2)  cat >user-data <<EOF
        #cloud-config
        password: cmpe283 <<new password here>>
        chpasswd: { expire: False }
        ssh_pwauth: True
        EOF

    3)  cloud-localds user-data.img user-data
    ```
    - Now, to run this ubuntu image, execute this:
    ```
    sudo qemu-system-x86_64 -enable-kvm -hda bionic-server-cloudimg-amd64.img -drive "file=user-data.img,format=raw" -m 512 -curses -nographic
    ```

    - **username**: `ubuntu`
    - **password**: `cmpe283`

    - Now to check the functionality, we need the cpuid utility package, for that
    ```
    sudo apt-get update
    sudo apt-get install cpuid
    ```

  - NOTE: Make sure two terminals are open:
      - the GCP VM terminal1
      - the nested VM terminal2(logged in)

<br />



## Question 3:

Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are theremore exits performed during certain VM operations? Approximately how many exits does a full VM boot entail?

- For exit 10 & 12 the number of exits increases at a stable rates as these exits are called fairly regularly. Please refer the below screenshots

  Output:
   <img width="874" alt="9" src="https://github.com/bkrish111/CMPE283-Virtualization/assets/147780244/1d418ddc-e6c2-4497-a60a-069aa3151a0b">

   ![10](https://github.com/bkrish111/CMPE283-Virtualization/assets/147780244/aacec653-121b-41ef-9799-b5967866bab4)


 

<br />

## Question 4:

- Following are some of the most frequently called exits:
  - Exit #49 - EPT_MISCONFIG
  - Exit #30 - IO_INSTRUCTION
  - Exit #28 - CR_ACCESS
  -
- Following are some of the least frequently called exits(>0):
  - Exit #54 - WBINVD
  - Exit #29 - DR_ACCESS
  - Exit #0 - EXCEPTION_NMI

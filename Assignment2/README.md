# CMPE 283 Assignment 2 

- The assignment is performed on Google Cloud VM supporting nested virtualiztion(`--enable-nested-virtualization`).
- Configuration: 
  - Machine Type: **n2-standard-8**
  - CPU platform: **Intel Cascade Lake**

<br />

- Assignment 2 changes the behavior of the **cpuid** instruction for the following cases:
  - CPUID leaf node(`%eax=0x4fffffff`)
    - Return the total number of exits for all types in **%eax**.
  - CPUID lead node(`%eax=0x4ffffffe`) 
    - Return the high 32 bits of the total time spent processing all exits in **%ebx**.
    - Return the low 32 bits of the total time spent processing all exits in **%ecx**.
    - **%ebx** and **%ecx** return values are measured in processor cycles, across all vCPUs.

<br />

## Question 1:
  - Sri Anudeep Velicheti and Bhargav Krishna Mullapudi

  - Anudeep

  Built the kernel and installed essential kernel modules in the VM. 
  Studied and explored the assignment's code, making necessary changes as discussed. 
  Updated the code to reflect these modifications and compiled test files.

  - Bhargav

  Built the kernel, went through the canvas lecture to understand the necessary steps, 
  conducted research on CPUID instructions and CPU leaf nodes, figured out where and what 
  changes were needed to finish the assignment, and updated README.md while creating
  documentation.

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

10) Testing the CPUID functionality for %eax=0x4fffffff
    T2: sudo cpuid -l 0x4fffffff
    T1: sudo dmesg

11) Testing the CPUID functionality for %eax=0x4ffffffe
    T2: sudo cpuid -l 0x4ffffffe
    T1: sudo dmesg
    Please refer images folder

  

# CMPE 283 - Assignment I

1. Create GCP account and create project
2. Once a Project is created enable “Compute Engine API” for that project to create and manage Virtual Machines.
3. Once we enable Compute Engine API, Create Virtual Machine Instance using the below command. (also enable nested-virtualization)

   gcloud compute instances create vm-283 --project=vm-assignment-408008 --zone=us-central1-a --machine-type=n2-standard-8 --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=5155186242-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform --tags=http-server,https-server --min-cpu-platform=Intel\ Cascade\ Lake --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/ubuntu-os-cloud/global/images/ubuntu-1804-bionic-v20221018,mode=rw,size=100,type=projects/vm-assignment-408008/zones/us-central1-a/diskTypes/pd-ssd --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any --enable-nested-virtualization --metadata=ssh-keys=user:key

4. Once an instance is created, open the instance using “gcloud compute ssh <<instance_name>>”.
  
5. Once we get into the instance, clone the git repo after changing the “cmpe283-1.c” file. For all the MSRs you can find the code here.
6. File cmpe-281 includes:
   1. Primary Process based controls (IA32_VMX_PROCBASED_CTLS)
   2. Secondary process based controls (IA32_VMX_PROCBASED_CTLS2)
   3. Secondary process based controls (IA32_VMX_PROCBASED_CTLS2)
   4. Entry controls (IA32_VMX_ENTRY_CTLS)
   5. Cannot include tertiary controls as it has Can set=No in primary controls
7. Install necessary packages (mainly make) in the root user by using the “apt install gcc make” command.

   If getting errors do apt update, followed by below packages
   apt install libelf-dev make flex bison libssl-dev 

8. Check the kernel version and install that version using the below commands in the screenshot.
   <!-- <img width="620" alt="Screen Shot 2022-11-07 at 2 35 50 AM" src="https://user-images.githubusercontent.com/30898620/200289837-d5c06035-3856-4a1d-b2c1-505934d5ffe9.png"> -->
9. Execute the “make” command to create the kernel object.
10. Execute sudo “insmod cmpe283-1.ko” command to load the kernel object.
11. Now, To view the output use the “dmesg” command.
    <!-- <img width="621" alt="Screen Shot 2022-11-07 at 2 36 10 AM" src="https://user-images.githubusercontent.com/30898620/200289972-6860e85e-fe97-4d8d-a7ac-24e91bc32b7d.png">
    <img width="622" alt="Screen Shot 2022-11-07 at 2 36 27 AM" src="https://user-images.githubusercontent.com/30898620/200289992-110fcad0-a435-4396-b0e0-d0274c32b8a5.png">
    <img width="617" alt="Screen Shot 2022-11-07 at 2 36 43 AM" src="https://user-images.githubusercontent.com/30898620/200290004-549f5356-3644-4075-a476-5a81b829b7d5.png"> -->

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


created the kernel and reviewed the lectures and Intel SDM to gather information on other VM-Execution Controls. 
Also, explored GCP VM setup and configured a GCP VM for completing assignments.

- Bhargav


created the kernel and reviewed the lectures, made changes to the provided cmpe283-1.c file, 
and compiled the C file. Additionally, explored GCP Cloud, 
examined various VM options for selection, and initiated the setup of VMs.

<br />

## Question 2: (Steps used to complete the assignment)

 ### Please refer above
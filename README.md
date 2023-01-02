#   Protecting Container Apps running on VMware Platform using vSphere Replication

##  1.	Overview

This document describes the process of installing and configuring the VMware vSphere replication appliance in VMware vSphere Cluster and configuring the replication of Red Hat OpenShift Cluster that hosts the Container application.

vSphere Replication does not have a separate license as it is a feature of certain vSphere license editions.

-   vSphere Essentials Plus
-   vSphere Standard
-   vSphere Enterprise
-   vSphere Enterprise Plus

If you have the correct vSphere license, there is no limit on the number of virtual machines that you can replicate by using vSphere Replication.

VMware vSphere Replication can replicate the Virtual Machine on same vSphere Cluster, between two vSphere cluster within the same site and between two different sites as well.

This document provides the steps for replicating the Red Hat OpenShift Virtual Machine within the same site. 

For this scenario, we use the following versions.

-   vSphere Replication 8.2
-   vCenter Server 6.7.0.31100 Build 13843380
-   vSphere 6.7. 201906002 build 139812

##  2.  High-Level Architecture Diagram

##  3.	Install Requirements

1.  Download vSphere Replication appliance version 8.2  from VMware 

    vSphere Replication can be downloaded from your VMware account [HERE](https://customerconnect.vmware.com/downloads/details?productId=742&rPId=33979&downloadGroup=VR820)

    The file is an ISO (no boot), so you can mount in a VM, or you can extract the ISO. Then deploy as an OVF in your vCenter Server

2.  Supports vCenter Server

    Version 6.7 U2, 6.7 U1, 6.7.0, 6.5 U3, 6.5 U2, 6.5 U1, 6.5.0, 6.0 U3

3.	Supports vSphere ESXi

    Version 6.7 U2, 6.7 U1, 6.7.0, 6.5 U2, 6.5 U1, 6.5.0, 6.0 U3 Supports vSAN 6.7 U2, 6.7 U1, 6.7, 6.6.1 U3, 6.6.1 U2, 6.6.1, 6.6, 6.5

    For unsupported vCenter and ESXi hosts, Check vSphere Replication matrix [HERE](https://interopmatrix.vmware.com/#interop&509=2929) and download the supported version of vSphere Replication appliance. The installation and configuration steps given below remains same

##  4.  Installation Steps

Only one vSphere replication appliance can be registered with same vCenter server. As we are going to protect Red Hat OpenShift Virtual Machine within the same site, we just need to deploy only one vSphere Replication Appliance.

###  Deploy the vSphere Replication Virtual Appliance on Source Site

1.	Log in to the vSphere Client on the source site
    If you use the HTML5-based vSphere Client to deploy the OVF virtual appliance, on vSphere prior to vSphere 6.7 Update 1, the deployment succeeds, but the vSphere Replication fails to start.

2.	On the home page, select Hosts and Clusters

3.	**Right-click** a host and select **Deploy OVF template**

4.	Provide the location of the OVF file from which to deploy the vSphere Replication appliance, and click Next. 

    -   Select URL and provide the URL to deploy the appliance from an online URL. 
    -   If you downloaded and mounted the vSphere Replication ISO image on a system in your environment, select Local file > Browse and navigate to the \bin directory in the ISO image, and

    To install vSphere Replication appliance (that includes Manager and Server), 

    Select files vSphere_Replication_OVF10.ovf,  vSphere_Replication_OVF10.cert, vSphere_Replication_OVF10.mf, vSphere_Replication-system.vmdk, and vSphere_Replication-support.vmdk files

5.	Accept the name, select or search for a destination folder or datacenter for the virtual appliance, and click **Next**

    You can enter a new name for the virtual appliance. The name must be unique within each vCenter Server virtual machine folder. 

6.	Select a cluster, host, or resource pool where you want to run the deployed template, and click **Next**

7.	Review the virtual appliance details and click **Next**

8.	Accept the end-user license agreements (EULA) and click **Next**

9.	Select the number of vCPUs for the virtual appliance and click **Next**

    **Smaller**: VR with **2vCPU** with **8Gb Memory** and **26Gb of Virtual Disk** is sufficient for small environment


10.	Select a destination datastore and disk format for the virtual appliance and click Next

    Encrypting the vSphere Replication appliance VM is not necessary to replicate encrypted VMs with vSphere Replication. 

11.	Select a network from the list of available networks, set the IP protocol and IP allocation, and click **Next**. 

    vSphere Replication supports both DHCP and static IP addresses. You can also change network settings by using the virtual appliance management interface (VAMI) after installation. 

12.	On the Customize template page, enter one or more NTP server host names or IP addresse


13.	Set the password for the root account that is at least eight characters long and enter the hostname or IP address of at least one NTP server

14.	(Optional) Disable the VCTA service. If you do not want to use vSphere Replication for Disaster Recovery to Cloud, you can reduce the memory consumption by disabling the VCTA service

15.	Click **Next**

16.	 Review the binding to the vCenter Extension vService and click **Next**

17.	Power on the vSphere Replication appliance. Take a note of the IP address of the appliance and log out of the vSphere Client

18.	 (Optional) To deploy vSphere Replication on the target site, repeat the same above procedure. This steps is not required for this tutorial but will require for replicating virtual machine between two different site.

##  5.	Register vSphere Replication Appliance with vCenter Server

To register vSphere Replication appliance in vCenter, Yo need to enter Appliance VAMI using https://IP-Address:5480

1.	Enter the VAMI with the credentials

    **User**: root, **Pass**: the password that was set in the deployment (step 4)

2.	Go to tab Configuration and add all the information of  vCenter Server

3.	After that confirm the SSL Certificate from vCenter (meaning that was able to connect to vCenter) to continue

4.	After VRM service is running and VR is now registered in the vCenter

5.	Next, go back to your vCenter. If you are already logged in, just log out and log in again. Then you should see the information regarding the Plugin for vSphere Replication that was deployed. Refresh your browser so that Site Recovery shortcut is available

##  6.	Assign VRM Administrator Role to User

vSphere Replication includes a set of roles. Each role includes a set of privileges, which enable users with those roles to complete different actions. VRM Administrator is one of such roles which incorporate all vSphere replication privileges.

It is a good practice to create a new user for vSphere Replication administrator and assign the VRM administrator role to that user.   

1.  Create a user in vCenter 

    **Logon to** vCenter Server > Click on Menu > **Click** Administration > **Click** Users and Groups from Single Sign On > **Click** ADD

    Enter **Username, Password**, First Name and Last Name in the Add User window and then **Click ADD**

2.  Assign VRM Administrator role to user in vCenter  

    Select **vCenter** > **Permissions** > Click **ADD** >search for **vrmadmin** (created in Step 1)

    Select **VRM Administrator** role from the list > Check mark on **“Propagate to children”** option and then Click **OK**.

##  7.	Create a vSphere Replication for Virtual Machine (Red Hat OpenShift Cluster)

By default, vCenter Administrator user do not have the privileged to configure VM replication so it is required to create a VRM Administrator user and assign the priviledge.
 
1.	Login to vCenter web client using VRM Administrator user “vrmadmin”
2.	Launch vSphere Replication on the vCenter source by clicking in the shortcut Site Recovery, then click Open Site Recovery:

    It will open vSphere Replication from vCenter

3.	Next, click the **Replications** tab to open replication area

4.	Next, click the Replications tab to open replication area and Click New button to add the new VM into Replication

5.	As we are going to replicate the VM in same site, Target site is the same as source site so just Click Next 

6.	Select Red Hat OpenShift Cluster VM and  Click Next 

7.	Select  the datastore where replica VMs will be saved and click Next

8.	Next is one of the essential sections on our replication process. Here is where we set our **RTO** and **RPO** for SLAs and Disaster Recovery plan.

9.	Review the configuration and click Finish.

10.	Red Hat OpenShift VM will appear under replications tab now and replication starts immediately.

##  8.	Performing Recovery of Virtual Machine (Red Hat OpenShift Cluster)

vSphere Replication performs a sequence of steps to recover replicated virtual machines:
-   vSphere Replication prepares for the recovery operation.
    -   If you perform a synchronization of the latest changes, vSphere Replication checks that the source site is available and source virtual machine is powered off before recovering the virtual machine on the target site. Then vSphere Replication synchronizes the changes from the source to the target site. 

    -   If you skip the synchronization and recover with the latest data available, for example, if the source site is not available, vSphere Replication uses the latest available data at the target site. 
  
-   vSphere Replication rebuilds the replicated .vmdk files. 
-   vSphere Replication reconfigures the newly replicated virtual machine with the correct disk paths. 
-   vSphere Replication registers the virtual machine with vCenter Server at the target site

**Note: Both Source and Target sites are same as VM’s replication is configured for same site.**

**1.  Recover Virtual Machines with vSphere Replication**

With vSphere Replication, you can recover virtual machines that were successfully replicated at the target site. You can recover one virtual machine at a time.

**Prerequisites:**

Verify that the virtual machine at the source site is powered off. If the virtual machine is powered on, an error message reminds you to power it off.

**Procedure:**

-   Log in vSphere Web Client or vSphere Client with  **vrmadmin** user 
-   On the home page, click **Site Recovery** and click **Open Site Recovery**. 
-   On the Site Recovery home page,  click **View Details**. 
-   Click the **Replications tab** and select a **VM** that you want to recover
-   Click the **Recover** icon from top
-   Select whether to recover the virtual machine with all the latest data, or to recover the virtual machine with the most recent data available  and then Click **Next**
-   (Optional) Select the Power on the virtual machine after recovery check box.
-   Click **Next**
-   Select the **recovery folder** and click **Next**
-   Select the **target compute resource** and click **Next**
-   Click **Finish**
-   Once VM will be restored, its status will change to **Recovered**  
-   Login to **vCenter** with **Administrator** user > **Right Click** on **Recovered VM** (Red Hat OpenShift Cluster) > **Edit Settings** and **click on “Connect”** from Network Adapter 1” and click **OK**
-   Click on **Snapshots TAB** > Select the desire snapshot (point in time) and then click **REVERT** button
-   Power ON the VM
-   Wait for 5 mins and then try to access the Red Hat OpenShift Console

#   Conclusion:
This tutorial walked you through Installation of VMware vSphere Replication Appliance, Virtual Machine replication configuration for Red Hat OpenShift Cluster VM within the same site and recovering the Red Hat OpenShift Cluster VM from vSphere replication console.


    







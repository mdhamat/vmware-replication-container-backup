# Installing VMware vSphere Replication to back up and restore Kubernetes workloads
##  Learn the end-to-end process for installing this backup-and-restore solution

VMware vSphere® Replication™ is a hypervisor-based, asynchronous
replication solution for vSphere that you can use for disaster recovery
and to protect the data on your virtual machines.

This tutorial shows you the steps to of install and configure vSphere
Replication in a VMware vSphere Cluster. You will also configure the
replication of a Red Hat OpenShift cluster that hosts your container
application.

## Pros and Cons:

-   Organization can protect their container workload against the
    infrastructure failure.

-   No Additional license cost or third-party tool is required if you
    have VMware vSphere infrastructure.

-   VMware vSphere Replication based approach is primarily used for
    disaster recovery but can also be used for immediate backup for an
    interim and give some time organization to think and plan for robust
    container backup solution.

-   VMware vSphere replication doesn't guarantee the consistency of the
    Red Hat OpenShift cluster and in-memory transactional data as it
    doesn't have ability on flush the in-memory transactional data onto
    disk, so container native backup solution is recommended for such
    requirement.

## Prerequisites

In this tutorial, we use the following software:

-   VMware vSphere Replication 8.3 and above
-   VMware vCenter Server 6.7 and above
-   VMware vSphere ESXi 6.7 and above

VMware vSphere Replication does not require a separate license, as it is
included with different vSphere licenses:

-   vSphere Essentials Plus
-   vSphere Standard
-   vSphere Enterprise
-   vSphere Enterprise Plus

If you have an appropriate vSphere license, there is no limit to the
number of virtual machines that you can replicate using vSphere
Replication. VMware vSphere Replication can replicate the virtual
machine on the same vSphere cluster, between two vSphere clusters within
the same site, and between two different sites.

You can download vSphere Replication 8.3 from VMware. The file is an ISO
image (that is, no boot is required), so you can mount it in a virtual
machine or extract it and deploy as a . Then deploy as an OVF file in
your vCenter Server.

**Note**: If you have an unsupported vCenter or ESXi host, check the
[vSphere Replication
matrix](https://interopmatrix.vmware.com/#interop&509=2929) and download
a supported version.

## High-level architecture

![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/d13f7224aa0b425c3a6484a6978de974014a3ff7/images/Figure1.png)

## Steps

Only one vSphere Replication appliance can be registered with the same
vCenter server. In this tutorial, we will protect a Red Hat OpenShift
virtual machine (VM) within the same site, meaning we only need to
deploy one vSphere Replication Virtual Appliance.

Follow the public article for the vSphere replication appliance
installation and configuration in detail -
[[https://www.bdrsuite.com/blog/vmware-vsphere-replication-8-2-step-by-step/]{.underline}](https://www.bdrsuite.com/blog/vmware-vsphere-replication-8-2-step-by-step/)

Thanks to Author (Luciano Patrao) for publishing this article.

Below is the list of steps for vSphere replication appliance
installation and configuration:

### Step 1: Download the vSphere Replication Appliance ([OVF File)](https://customerconnect.vmware.com/en/downloads/get-download?downloadGroup=VR8315)

### Step 2: Deploy the vSphere Replication Virtual Appliance on the source site.

### Step 3: Register the vSphere Replication Appliance with vCenter Server.

### Step 4: Assign VRM Administrator Role to User

vSphere Replication includes a set of roles, which include privileges that enable users to complete different actions. VRM Administrator is the role that includes all vSphere replication privileges.
It’s a good practice to create a new user to act as the vSphere Replication administrator and then assign the VRM Administrator role to that user.

1.  To create a user in vCenter, log in to vCenter Server. In the left
    menu, click **Administration**. The Administration menu opens.

2.  In the **Single Sign On** section of the menu, click **Users and
    Groups**. In the **Users and Groups** panel, click **ADD**.
    ![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure21.png)

    1.  In the **Add User** window, enter a username and password and
        first and last name for your administrator. You can optionally
        add an email address and description. Click **ADD**.
        ![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure22.png)
    3.  To assign the VRM Administrator role to a user in vCenter, in
        the left menu, select your vCenter and click the **Permissions**
        tab.
    4.  Click **ADD** and search for the **vrmadmin** user that you
        created previously. ![alt
        text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure23.png)
    5.  Click the **VRM Administrator** role from the list and select
        the **Propagate to children** check box. Click **OK**. ![alt
        text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure24.png)

You have now created a user and assigned them the VRM Administrator
role.

### Step 5. Create a vSphere Replication for a virtual machine (Red Hat OpenShift Cluster)

The Red Hat OpenShift VM will now appear on the **Replications** tab and
replications will begin immediately.

![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure31.png)


![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure32.png)


### Step 6. Performing a recovery of a virtual machine

vSphere Replication performs the following sequence of steps to recover
replicated virtual machines:

1.  vSphere Replication prepares for the recovery operation.
    -   If you perform a synchronization of the latest changes, vSphere
        Replication checks that the source site is available and source
        virtual machine is powered off before recovering the virtual
        machine on the target site. Then vSphere Replication
        synchronizes the changes from the source to the target site.
    -   If you skip the synchronization and recover with the latest data
        available, for example, if the source site is not available,
        vSphere Replication uses the latest available data at the target
        site.
2.  vSphere Replication rebuilds the replicated .vmdk files.
3.  vSphere Replication reconfigures the newly replicated virtual
    machine with the correct disk paths.
4.  vSphere Replication registers the virtual machine with vCenter
    Server at the target site. **Note**: Both the source and target
    sites are the same since the VM replication is configured for the
    same site.

#### Recover a virtual machine with vSphere Replication

With vSphere Replication, you can recover virtual machines that were
successfully replicated at the target site. You can recover one virtual
machine at a time.

Before you begin this step, ensure that the virtual machine at the
source site is powered off. If the virtual machine is powered on, an
error message will remind you to power it off.

1.  Log in to the vSphere web client or vSphere client as `vrmadmin`.

2.  On the home page, click **Site Recovery** and then click **Open Site
    Recovery**.

3.  On the Site Recovery page, click **View Details**.

4.  Click the **Replications** tab and select the VM that you want to
    recover. ![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure33.png)

5.  Click **Recover** from the horizontal menu and select whether to
    recover the virtual machine with all the latest data with the most
    recent data available. Click **Next**.

6.  **Optional**: Select the **Power on the virtual machine after
    recovery** check box.

7.  Click **Next**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure34.png)

8.  In the Folder window, select a folder for the VM at the recovery
    site. Click **Next**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure35.png)

9.  In the Resource window, select the target compute resource at the
    recovery site. Click **Next**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure36.png)
10. On the "Ready to complete" page, review the recovery information
    that you have entered. If you are satisfied with the settings, click
    **Finish**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure37.png)

11. Review the status of the virtual machine. When it is fully restored,
    its status will change to **Recovered**.\
    ![alt text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure38.png)

12. Log in to the vCenter as the administrator. Right-click on the
    recovered VM and click **Edit Settings**.\
    In the Edit Settings window, in the **Network adapter 1** row,
    select the **Connect** check box and click **OK**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure39.png)

13. Click the **Snapshots** tab in the horizontal menu, select your
    desired snapshot (that is, the point in time that you want to revert
    to) and then click **REVERT**. ![alt
    text](https://github.com/mdhamat/vmware-replication-container-backup/blob/f89c713bb5b6a6ef987e0e963c49c888fdd09e8a/images/Figure40.png)

14. Power on the virtual machine.

15. Wait for 5 mins and then access the Red Hat OpenShift Console.

## Summary

This tutorial walked you through the steps to install VMware vSphere
Replication Appliance. You configured a virtual machine replication for
Red Hat OpenShift Cluster within the same site and recovered the Red Hat
OpenShift Cluster VM from the vSphere Replication console.

To learn more about recovering your workloads ... \[NEED
CTA/DEMO/PRODUCT PAGE\]

<properties
	pageTitle="Protect ARM VMs with Azure Backup | Microsoft Azure"
	description="Protect ARM VMs with Azure Backup service. Use backups of ARM VMs to protect your data. Create and register a Recovery Services vault. Register VMs, create policy, and protect VMs in Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keyword="backups; vm backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/31/2016"
	ms.author="markgal; jimpark"/>


# First look: Back up ARM VMs to a Recovery Services vault

> [AZURE.SELECTOR]
- [Back up ARM VMs](backup-azure-vms-first-look-arm.md)
- [Back up Classic mode VMs](backup-azure-vms-first-look.md)

This tutorial takes you through the set of steps for creating a Recovery Services vault and backing up an Azure virtual machine (VM). This tutorial is for use with Recovery Services vaults which can be used to protect IaaS v.2 or Azure Resource Manager (ARM)-based VMs.

>[AZURE.NOTE] This tutorial assumes you already have a VM in your Azure subscription and that you have taken measures to allow the backup service to access the VM. Azure has two deployment models for creating and working with resources: [Resource Manager and classic](../resource-manager-deployment-model.md). This article is for use with Resource Manager and ARM-based VMs.

At a high level, here are the steps that you will complete.  

1. Create a Recovery Services vault for a VM.
2. Use the Azure portal to select a Scenario, set Policy, and identify items to protect.
3. Run the initial backup.



## Step 1 - Create a Recovery Services vault for a VM

A Recovery Services vault is an entity that stores all the backups and recovery points that have been created over time. The Recovery Services vault also contains the backup policy applied to the protected VMs.

>[AZURE.NOTE] Backing up VMs is a local process. You cannot back up VMs from one location to a Recovery Services vault in another location. So, for every Azure location that has VMs to be backed up, at least one Recovery Services vault must exist in that location.


To create a Recovery Services vault:

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. On the Hub menu, click **Browse** and in the list of resources, type **Recovery Services**. As you begin typing, the list will filter based on your input. Click **Recovery Services vault**.

    ![Create Recovery Services Vault step 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    The list of Recovery Services vaults are displayed.

3. On the **Recovery Services vaults** menu, click **Add**.

    ![Create Recovery Services Vault step 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    The Recovery Services vault blade opens, prompting you to provide a **Name**, **Subscription**, **Resource group**, and **Location**.

    ![Create Recovery Services vault step 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. For **Name**, enter a friendly name to identify the vault. The name needs to be unique for the Azure subscription. Type a name that contains between 2 and 50 characters. It must start with a letter, and can contain only letters, numbers, and hyphens.

5. Click **Subscription** to see the available list of subscriptions. If you are not sure which subscription to use, use the default (or suggested) subscription. There will be multiple choices only if your organizational account is associated with multiple Azure subscriptions.

6. Click **Resource group** to see the available list of Resource groups, or click **New** to create a new Resource group. For complete information on Resource groups, see [Using the Azure Portal to deploy and manage your Azure resources](../azure-portal/resource-group-portal.md)

7. Click **Location** to select the geographic region for the vault. The vault **must** be in the same region as the virtual machines that you want to protect.

    >[AZURE.IMPORTANT] If you are unsure of the location in which your VM exists, close out of the vault creation dialog, and go to the list of Virtual Machines in the portal. If you have virtual machines in multiple regions, you will need to create a Recovery Services vault in each region. Create the vault in the first location before going to the next location. There is no need to specify storage accounts to store the backup data--the Recovery Services vault and the Azure Backup service handle this automatically.

8. Click **Create**. It can take a while for the Recovery Services vault to be created. Monitor the status notifications in the upper right-hand area in the portal.
Once your vault is created, it opens in the portal.

9. In your vault, click **All settings** > **Backup Configuration** to view the **Storage replication type**. Choose the storage replication option for your vault.

    ![List of backup vaults](./media/backup-azure-vms-first-look-arm/choose-storage-configuration.png)

    By default, your vault has geo-redundant storage. If you are using Azure as a primary backup storage endpoint, it is recommended that you continue using geo-redundant storage. If you are using Azure as  non-primary backup storage endpoint, then you can consider choosing locally redundant storage, which will reduce the cost of storing data in Azure. Read more about [geo-redundant](../storage/storage-redundancy.md#geo-redundant-storage) and [locally redundant](../storage/storage-redundancy.md#locally-redundant-storage) storage options in this [overview](../storage/storage-redundancy.md).

    After choosing the storage option for your vault, you are ready to associate the VM with the vault. To begin the association, you should discover and register the Azure virtual machines.

## Step 2 - Select scenario set policy and define items to protect
Before registering a VM with a vault, run the discovery process to ensure that any new virtual machines that have been added to the subscription are identified. The process queries Azure for the list of virtual machines in the subscription, along with additional information like the cloud service name and the region.

1. If you already have a Recovery Services vault open, proceed to step 2. If you do not have a Recovery Services vault open, but are in the Azure portal,
on the Hub menu, click **Browse**.

    - In the list of resources, type **Recovery Services**.
    - As you begin typing, the list will filter based on your input. When you see **Recovery Services vaults**, click it.

    ![Create Recovery Services Vault step 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    The list of Recovery Services vaults appears.

    - From the list of Recovery Services vaults, select a vault.

    The selected vault dashboard opens.

    ![Open vault blade](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. From the vault dashboard menu click **Backup** to open the Backup blade.

    ![Open Backup blade](./media/backup-azure-vms-first-look-arm/backup-button.png)

    When the blade opens, the Backup service searches for any new VMs in the subscription.

    ![Discover VMs](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. On the Backup blade, click **Scenario** to open the Scenario blade.

    ![Open Scenario blade](./media/backup-azure-vms-first-look-arm/select-backup-scenario-one.png)

4. On the Scenario blade, from the **Backup Type** menu, select **Azure virtual machine backup** and click **OK**.

    ![Open Scenario blade](./media/backup-azure-vms-first-look-arm/select-rs-backup-scenario-two.png)

    The Scenario blade closes and the Backup Policy blade opens.

5. On the Backup blade, select the backup policy you want to apply to the vault and click **OK**.

    ![Select backup policy](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy.png)

    The default policy is listed in the details. If you want to create a new policy, select **Create New**. For instructions on defining a backup policy, see [Defining a backup policy](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Once you click OK, the backup policy is associated with the vault. Next choose the VMs to associate with the vault.

6. Choose the virtual machines to associate with the specified policy and click **Select**.

    ![Select workload](./media/backup-azure-vms-first-look-arm/select-vms-to-backup.png)

    If you do not see the desired VM in the list, click **Refresh**. If you still do not see the desired VM, check that it exists in the same Azure location as the Recovery Services vault.

7. Now that you have defined all settings for the vault, in the Backup blade click **Enable Backup** at the bottom of the page. This deploys the policy to the vault and the VMs.

    ![Enable Backup](./media/backup-azure-vms-first-look-arm/enable-backup-settings.png)


## Step 3 - Initial backup

Once a backup policy has been deployed on the virtual machine, that does not mean the data has been backed up. By default, the first scheduled backup (as defined in the backup policy) is the initial backup. Until the initial backup occurs, the Last Backup Status on the **Backup Jobs** blade shows as **Warning(initial backup pending)**.

![Backup pending](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Unless your initial backup is due to begin very soon, it is recommended that you run **Back up Now**.

To run **Back up Now**:

1. On the vault dashboard, on the **Backup** tile, click **Azure Virtual Machines** <br/>
    ![Settings icon](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    The **Backup Items** blade opens.

2. On the **Backup Items** blade, right-click the vault you want to back up, and click **Backup now**.

    ![Settings icon](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    The Backup job is triggered. <br/>

    ![Backup job triggered](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. To view that your initial backup has completed, on the vault dashboard, on the **Backup Jobs** tile, click **Azure virtual machines**.

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    The Backup Jobs blade opens.

4. In the Backup jobs blade, you can see the status of all jobs.

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] As a part of the backup operation, the Azure Backup service issues a command to the backup extension in each virtual machine to flush all writes and take a consistent snapshot.

    When the backup job is finished, the status is *Completed*.

## Defining a backup policy

A backup policy defines a matrix of when the data snapshots are taken, and how long those snapshots are retained. When defining a policy for backing up a VM, you can trigger a backup job *once a day*. When you create a new policy, it is applied to the vault. The backup policy interface looks like this:

![Backup policy](./media/backup-azure-vms-first-look-arm/backup-policy-daily-raw.png)

To create a policy:

1. For **Policy Name** give the Policy a name.

2. Snapshots of your data can be taken at Daily or Weekly intervals. Use the **Backup Frequency** drop-down menu to choose whether data snapshots are taken Daily or Weekly.

    - If you choose a Daily interval, use the highlighted control to select the time of the day for the snapshot. To change the hour, de-select the hour, and select the new hour.

    ![Daily backup policy](./media/backup-azure-vms-first-look-arm/backup-policy-daily.png) <br/>

    - If you choose a Weekly interval, use the highlighted controls to select the day(s) of the week, and the time of day to take the snapshot. In the day menu, select one or multiple days. In the hour menu, select one hour. To change the hour, de-select the selected hour, and select the new hour.

    ![Weekly backup policy](./media/backup-azure-vms-first-look-arm/backup-policy-weekly.png)

3. By default, all **Retention Range** options are selected. Uncheck any retention range limit you do not want to use.

    >[AZURE.NOTE] When protecting a VM, a backup job runs once a day. The time when the backup runs is the same for each retention range.

    In the corresponding controls, specify the interval(s) to use. Monthly and Yearly retention ranges allow you to specify the snapshots based on a weekly or daily increment.

4. After setting all options for the policy, at the bottom of the blade click **OK**.

    The new policy is set to be applied to the vault once the Recovery Services vault settings are completed. Return to step 6 of the section, [Select scenario set policy and define items to protect](backup-azure-vms-first-look-arm.md#step-2---select-scenario-set-policy-and-define-items-to-protect)

## Install the VM Agent on the virtual machine

This information is provided in case it is needed. The Azure VM Agent must be installed on the Azure virtual machine for the Backup extension to work. However, if your VM was created from the Azure gallery, then the VM Agent is already present on the virtual machine. VMs that are migrated from on-premises datacenters would not have the VM Agent installed. In such a case, the VM Agent needs to be installed. If you have problems backing up the Azure VM, check that the Azure VM Agent is correctly installed on the virtual machine (see the table below). If you creating a custom VM, [ensure that the **Install the VM Agent** check box is selected](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) before the virtual machine is provisioned.

Learn about the [VM Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) and [how to install it](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

The following table provides additional information about the VM Agent for Windows and Linux VMs.

| **Operation** | **Windows** | **Linux** |
| --- | --- | --- |
| Installing the VM Agent | <li>Download and install the [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). You will need Administrator privileges to complete the installation. <li>[Update the VM property](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) to indicate that the agent is installed. | <li> Install the latest [Linux agent](https://github.com/Azure/WALinuxAgent) from GitHub. You will need Administrator privileges to complete the installation. <li> [Update the VM property](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) to indicate that the agent is installed. |
| Updating the VM Agent | Updating the VM Agent is as simple as reinstalling the [VM Agent binaries](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Ensure that no backup operation is running while the VM agent is being updated. | Follow the instructions on [updating the Linux VM Agent ](../virtual-machines-linux-update-agent.md). <br>Ensure that no backup operation is running while the VM Agent is being updated. |
| Validating the VM Agent installation | <li>Navigate to the *C:\WindowsAzure\Packages* folder in the Azure VM. <li>You should find the WaAppAgent.exe file present.<li> Right-click the file, go to **Properties**, and then select the **Details** tab. The Product Version field should be 2.6.1198.718 or higher. | N/A |


### Backup extension

Once the VM Agent is installed on the virtual machine, the Azure Backup service installs the backup extension to the VM Agent. The Azure Backup service seamlessly upgrades and patches the backup extension without additional user intervention.

The backup extension is installed by the Backup service whether or not the VM is running. A running VM provides the greatest chance of getting an application-consistent recovery point. However, the Azure Backup service will continue to back up the VM even if it is turned off, and the extension could not be installed. This is known as Offline VM. In this case, the recovery point will be *crash consistent*.



## Troubleshooting information
If you have issues accomplishing some of the tasks in this article, please consult the
[Troubleshooting guidance](backup-azure-vms-troubleshoot.md).


## Questions?
If you have questions, or if there is any feature that you would like to see included, [send us feedback](http://aka.ms/azurebackup_feedback).

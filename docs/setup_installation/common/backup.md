# How to take, restore and manage backups in managed.hopsworks.ai

### Introduction
[Managed.hopsworks.ai](https://managed.hopsworks.ai) is our managed platform for running Hopsworks in the cloud. When managing a cluster it is important to be able to take and restore backups to handle any failure eventuality. In this tutorial you will learn how to [take](## Taking a backup), [restore](## Restoring a backup) and [manage](## Managing your backups) backups in [managed.hopsworks.ai](https://managed.hopsworks.ai)

!!! Note
    The information below is only accurate for version 3.1.0 and above. If your cluster is of an older version, refer to the appropriate version of the documentation.

!!! Note
    Clusters deployed on GCP are still running the 3.0.0 version of the backup. Refer to this version of the documentation to get more information.

## Prerequisites
To follow this tutorial you need to create a cluster in [Managed.hopsworks.ai](https://managed.hopsworks.ai). During the [cluster creation](../aws/cluster_creation.md) you need to set a positive number for the maximum retention period for your backups. This is done in the [backups step](../aws/cluster_creation.md#step-6-set-the-backup-retention-policy) of the cluster creation by setting the wanted retention period in _Validity of cluster backup images_. This step is needed because the backup process relies on the cloud bucket retention policy and needs it to be configured before any backup is taken.

!!! Warning 
    This value cannot be edited later on so make sure to set the proper one.

!!! Note
    To be able to take backup the cluster instances need special permissions. These permissions are cloud provider specific and indicated on the cluster creation page.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/aws/connect-aws-backup.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/aws/connect-aws-backup.png" alt="Choose the backup retention policy">
    </a>
    <figcaption>Choose the backup retention policy</figcaption>
  </figure>
</p>

## Important to keep in mind
!!! Warning
    When you take a backup it is stored in the bucket and the container registry associated with the cluster. If you, at any point delete them you will not be able to restore your backup.

## Backup guaranties

Taking a backup will save:

  - The content of the online feature store.
  - The content of the offline feature store.
  - Any file stored in the distributed file system.
  - The python environments.
  - Any setting configured through Hopsworks.


Anything not listed above will not be saved by the backup and will need to be recreated after the backup restoration. Here is a nonexostive list of examples of things that are not saved by the backup:

  - Kafka schemas and topics.
  - OpenSearch content.
  - Cluster metrics.
  - Custom configuration modifications done directly on the instance.

## Taking a backup
To take a backup go to the backup tab of your cluster (1) and click on _Create backup_ (2). If you wish to give a name to your backup edit the value in _New backup name_ (3) before clicking on _Create backup_.

!!! Note 
    The cluster needs to be running to take a backup.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/create_backup.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/create_backup.png" alt="Create a backup">
    </a>
    <figcaption>Create a backup</figcaption>
  </figure>
</p>

While the backup is being created some operations will not be possible on the cluster. To avoid, temporarily, limiting your cluster capabilities by accident you need to confirm that you want to take a backup. To confirm check the check box and click on the  _Backup_ button.

!!! Note
    The current list of operations being limited by the backup procedure is:
     
     - python library installation.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/confirm_backup.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/confirm_backup.png" alt="Confirm backup creation">
    </a>
    <figcaption>Confirm backup creation</figcaption>
  </figure>
</p>

You can then wait until the backup is complete. The backup process being underway is indicated next to the cluster status.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/ongoing_backup.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/ongoing_backup.png" alt="Backup ongoing">
    </a>
    <figcaption>Backup ongoing</figcaption>
  </figure>
</p>

## Restoring a backup
Go to the Backup tab of the dashboard (left menu (1)) to the list of all the backups. This list is organized by cluster. For each of the clusters, you can see the state of the cluster (2) and the list of backups for this cluster (3). 

To be able to restore a backup the corresponding cluster needs to be [terminated](./dashboard.md#terminate-the-cluster). If your cluster is not terminated go and terminate it. Once the cluster is terminated you restore a backup by clicking on the _Restore_ button (4) of the backup you want to restore.

!!! Warning
    The cluster needs to be terminated, but the bucket and docker repositories need to be kept as they are.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_list.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_list.png" alt="List of backups">
    </a>
    <figcaption>List of backups</figcaption>
  </figure>
</p>


!!! Warning
    Restoring a backup put back the bucket in the state it was at the time of the backup. This makes it impossible to then restore a backup taken more recently. 

    If you try to restore a backup that is not the latest backup you will be asked to confirm that you want to restore and thus delete any more recent backup.

    <p align="center">
      <figure>
        <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_delete_succeding.png">
          <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_delete_succeding.png" alt="Delete succeding backups">
        </a>
        <figcaption>Delete succeding backups</figcaption>
      </figure>
    </p>

Once you have clicked on _Restore_ you will be brought to the [cluster creation](../aws/cluster_creation.md) menu. All the entries should be prefilled with the values corresponding to your cluster configuration. You can go through all the cluster configurations to verify (recommended) or directly click on _Review_ in the left menu and click on the _Create_ button.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_review_and_create.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_review_and_create.png" alt="Review and restore backup">
    </a>
    <figcaption>Review and restore backup</figcaption>
  </figure>
</p>

A new cluster will be created and set in the state your cluster was in at the time of the backup.

!!! Note
    Restoring a backup does not recreate the workers for this cluster. You need to [add the workers](./adding_removing_workers.md) back once the cluster is created.

## Managing your backups
To manage your backups either go to the Backup tab of your cluster (1) or go to the backup tab of the dashboard (2).

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_tabs.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_tabs.png" alt="Backup tabs">
    </a>
    <figcaption>Backup tabs</figcaption>
  </figure>
</p>

If you go to the backup tab of your cluster you will see the list of backups associated with this cluster. For each of the backups, you will see their name (1), id (2), date of creation (3), and status (4). The status can be:

- **Completed**: the backup has been created and is ready to be restored.
- **Expired**: the backup is older than the maximum retention time set during [cluster creation](../aws/cluster_creation.md#step-6-set-the-backup-retention-policy), it will not be possible to restore it.
- **Failed**: the backup failed during its creation.
- **Running**: the backup is currently being created.
- **Deleting**: the backup is being deleted.


<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_info.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_info.png" alt="Backup info">
    </a>
    <figcaption>Backup info</figcaption>
  </figure>
</p>

If you go to the dashboard backup tab you will get a view of all the backups of all the clusters. For each of the backups, you get the same information as above.

To delete a backup click on the _Delete_ button on the same line as the backup name.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_delete.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_delete.png" alt="Delete backup">
    </a>
    <figcaption>Delete backup</figcaption>
  </figure>
</p>

Once you have clicked on the _Delete_ button, you will be asked to confirm that you want to delete it. Check the check box and click _Delete_ to confirm.

<p align="center">
  <figure>
    <a  href="../../../assets/images/setup_installation/managed/common/backup/backup_delete_confirm.png">
      <img style="border: 1px solid #000" src="../../../assets/images/setup_installation/managed/common/backup/backup_delete_confirm.png" alt="Confirm backup deletion">
    </a>
    <figcaption>Confirm backup deletion</figcaption>
  </figure>
</p>

The backup will then be deleted.

## Conclusion
During this tutorial, you have created a backup, restored a cluster from this backup, check the information about this backup, and finally deleted the backup.

Now that you have restored a cluster you can [add workers](./adding_removing_workers.md) or set up [autoscale](./autoscaling.md) on it.
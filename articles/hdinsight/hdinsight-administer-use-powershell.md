---
title: Manage Apache Hadoop clusters with PowerShell - Azure HDInsight
description: Learn how to perform administrative tasks for the Apache Hadoop clusters in HDInsight using Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
---

# Manage Apache Hadoop clusters in HDInsight by using Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell can be used to control and automate the deployment and management of your workloads in Azure. In this article, you learn how to manage [Apache Hadoop](https://hadoop.apache.org/) clusters in Azure HDInsight by using the Azure PowerShell Az module. For the list of the HDInsight PowerShell cmdlets, see the [Az.HDInsight reference](https://docs.microsoft.com/powershell/module/az.hdinsight).

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

The PowerShell [Az Module](https://docs.microsoft.com/powershell/azure/overview) installed.

## Create clusters

See [Create Linux-based clusters in HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## List clusters

Use the following command to list all clusters in the current subscription:

```powershell
Get-AzHDInsightCluster
```

## Show cluster

Use the following command to show details of a specific cluster in the current subscription:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## Delete clusters

Use the following command to delete a cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

You can also delete a cluster by removing the resource group that contains the cluster. Deleting a resource group deletes all the resources in the group including the default storage account.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## Scale clusters

The cluster scaling feature allows you to change the number of worker nodes used by a cluster that is running in Azure HDInsight without having to re-create the cluster. To change the Hadoop cluster size by using Azure PowerShell, run the following command from a client machine:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 For more information about scaling clusters, see [Scale HDInsight clusters](./hdinsight-scaling-best-practices.md).

## Grant/revoke access

HDInsight clusters have the following HTTP web services (all of these services have RESTful endpoints):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

By default, these services are granted for access. You can revoke/grant the access. To revoke:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

To grant:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> By granting/revoking the access, you reset the cluster user name and password.

Granting and revoking access can also be done via the portal. See [Manage Apache Hadoop clusters in HDInsight by using the Azure portal](hdinsight-administer-use-portal-linux.md).

## Update HTTP user credentials

It's the same procedure as Grant/revoke HTTP access. If the cluster has been granted the HTTP access, you must first revoke it.  And then grant the access with new HTTP user credentials.

## Find the default storage account

The following PowerShell script demonstrates how to get the default storage account name and the related information:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## Find the resource group

In the Resource Manager mode, each HDInsight cluster belongs to an Azure resource group.  To find the resource group:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## Submit jobs

**To submit MapReduce jobs**

See [Run the MapReduce examples included in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**To submit Apache Hive jobs**

See [Run Apache Hive queries using PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**To submit Apache Sqoop jobs**

See [Use Apache Sqoop with HDInsight](hadoop/hdinsight-use-sqoop.md).

**To submit Apache Oozie jobs**

See [Use Apache Oozie with Apache Hadoop to define and run a workflow in HDInsight](hdinsight-use-oozie-linux-mac.md).

## Upload data to Azure Blob storage

See [Upload data to HDInsight](hdinsight-upload-data.md).

## See Also

* [Az.HDInsight cmdlets](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Manage Apache Hadoop clusters in HDInsight by using the Azure portal](hdinsight-administer-use-portal-linux.md)
* [Administer HDInsight using a command-line interface](hdinsight-administer-use-command-line.md)
* [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)
* [Submit Apache Hadoop jobs programmatically](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Get started with Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)

---
title: De HDFS CLI gebruiken met Azure Data Lake Storage Gen2
description: Gebruik de Hadoop Distributed File System (HDFS) CLI voor Azure Data Lake Storage Gen2. Een container maken, een lijst met bestanden of mappen ophalen, en meer.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: b73e79c2f7bbd37b640d3e83d98be68d85ed3b5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735366"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>De HDFS CLI gebruiken met Data Lake Storage Gen2

U kunt toegang krijgen tot en beheren van de gegevens in uw opslag account met behulp van een opdracht regel interface, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Dit artikel bevat enkele voor beelden die u kunnen helpen om aan de slag te gaan.

HDInsight biedt toegang tot de gedistribueerde container die lokaal is gekoppeld aan de reken knooppunten. U kunt deze container openen met behulp van de shell die rechtstreeks samenwerkt met de HDFS en de andere bestands systemen die door Hadoop worden ondersteund.

Zie de [officiële documentatie](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) en de [machtigingen gids voor hdfs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) voor meer informatie over HDFS cli.

>[!NOTE]
>Als u Azure Databricks in plaats van HDInsight gebruikt en u met uw gegevens wilt communiceren met behulp van een opdracht regel interface, kunt u de Databricks CLI gebruiken om te communiceren met het Databricks-bestands systeem. Zie [DATABRICKS cli](/azure/databricks/dev-tools/cli/)(Engelstalig).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>De HDFS CLI gebruiken met een HDInsight Hadoop-cluster in Linux

Maak eerst [externe toegang tot services](../../hdinsight/hdinsight-hadoop-linux-information.md#remote-access-to-services). Als u [SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) selecteert, ziet de Power shell-voorbeeld code er als volgt uit:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
U kunt de connection string vinden in het gedeelte SSH-cluster aanmelden van de Blade HDInsight-cluster in Azure Portal. Er zijn SSH-referenties opgegeven op het moment dat het cluster wordt gemaakt.

>[!IMPORTANT]
>Facturering van HDInsight-cluster begint nadat een cluster is gemaakt en stopt wanneer het cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. Zie ons [artikel in het onderwerp](../../hdinsight/hdinsight-delete-cluster.md)voor meer informatie over het verwijderen van een cluster. Gegevens die zijn opgeslagen in een opslag account met Data Lake Storage Gen2 ingeschakeld blijven, zelfs nadat een HDInsight-cluster is verwijderd.

## <a name="create-a-container"></a>Een container maken

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* Vervang de tijdelijke aanduiding `<container-name>` door de naam die u aan uw container wilt geven.

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

## <a name="get-a-list-of-files-or-directories"></a>Een lijst met bestanden of mappen ophalen

`hdfs dfs -ls <path>`

Vervang de `<path>` tijdelijke aanduiding door de URI van de container of container.

Bijvoorbeeld: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Een map maken

`hdfs dfs -mkdir [-p] <path>`

Vervang de `<path>` tijdelijke aanduiding door de naam van de hoofd container of een map in de container.

Bijvoorbeeld: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Een bestand of map verwijderen

`hdfs dfs -rm <path>`

Vervang de `<path>` tijdelijke aanduiding door de URI van het bestand of de map die u wilt verwijderen.

Bijvoorbeeld: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>De Access Control lijsten (Acl's) van bestanden en mappen weer geven

`hdfs dfs -getfacl [-R] <path>`

Voorbeeld:

`hdfs dfs -getfacl -R /dir`

Zie [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Acl's van bestanden en mappen instellen

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Voorbeeld:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zie [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>De eigenaar van bestanden wijzigen

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Zie [eigendom](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Groeps koppeling van bestanden wijzigen

`hdfs dfs -chgrp [-R] <group> <URI>`

Zie [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>De machtigingen van bestanden wijzigen

`hdfs dfs -chmod [-R] <mode> <URI>`

Zie [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

U kunt de volledige lijst met opdrachten bekijken op de website van [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) .

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Data Lake Storage Gen2-account gebruiken in Azure Databricks](./data-lake-storage-use-databricks-spark.md)

* [Meer informatie over toegangs beheer lijsten voor bestanden en mappen](./data-lake-storage-access-control.md)
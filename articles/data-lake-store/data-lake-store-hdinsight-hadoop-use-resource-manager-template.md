---
title: Sjabloon-HDInsight-cluster met Data Lake Storage Gen1
description: Gebruik Azure Resource Manager sjablonen voor het maken en gebruiken van Azure HDInsight-clusters met Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a7283ad4c4c61ecc293a55ffc4cb9626bb28d630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108725"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Een HDInsight-cluster met Azure Data Lake Storage Gen1 maken met behulp van Azure Resource Manager sjabloon
> [!div class="op_single_selector"]
> * [Portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Power shell gebruiken (voor standaard opslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Power shell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van Azure PowerShell voor het configureren van een HDInsight-cluster met Azure Data Lake Storage Gen1, **als extra opslag**.

Data Lake Storage Gen1 kan worden gebruikt als standaard opslag of als een extra opslag account voor ondersteunde cluster typen. Als Data Lake Storage Gen1 als extra opslag wordt gebruikt, is het standaard opslag account voor de clusters nog steeds Azure Blob Storage (WASB) en de aan het cluster gerelateerde bestanden (zoals Logboeken, enzovoort) naar de standaard opslag geschreven, terwijl de gegevens die u wilt verwerken, kunnen worden opgeslagen in een Data Lake Storage Gen1-account. Het gebruik van Data Lake Storage Gen1 als een extra opslag account heeft geen invloed op de prestaties of de mogelijkheid om te lezen/schrijven naar de opslag van het cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 voor HDInsight-cluster opslag gebruiken

Hier volgen enkele belang rijke aandachtspunten voor het gebruik van HDInsight met Data Lake Storage Gen1:

* Optie voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1 als standaard opslag is beschikbaar voor HDInsight-versie 3,5 en 3,6.

* Optie voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1 als extra opslag ruimte beschikbaar is voor HDInsight-versies 3,2, 3,4, 3,5 en 3,6.

In dit artikel wordt een Hadoop-cluster met Data Lake Storage Gen1 als extra opslag ingericht. Zie [een HDInsight-data Lake Storage gen1 cluster met behulp van Azure portal maken](data-lake-store-hdinsight-hadoop-use-portal.md)voor instructies over het maken van een Hadoop-cluster met data Lake Storage gen1 als standaard opslag.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/).
* **Azure Active Directory Service-Principal**. De stappen in deze zelf studie bevatten instructies over het maken van een Service-Principal in azure AD. U moet echter een Azure AD-beheerder zijn om een service-principal te kunnen maken. Als u een Azure AD-beheerder bent, kunt u deze vereiste overs Laan en door gaan met de zelf studie.

    **Als u geen Azure AD-beheerder bent**, kunt u de stappen die nodig zijn om een service-principal te maken, niet uitvoeren. In dat geval moet uw Azure AD-beheerder eerst een service-principal maken voordat u met Data Lake Storage Gen1 een HDInsight-cluster kunt maken. Daarnaast moet de service-principal worden gemaakt met behulp van een certificaat, zoals wordt beschreven in [een service-principal maken met een certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Een HDInsight-cluster met Data Lake Storage Gen1 maken
De Resource Manager-sjabloon en de vereisten voor het gebruik van de sjabloon, zijn beschikbaar op GitHub bij [het implementeren van een HDInsight Linux-cluster met nieuwe data Lake Storage gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Volg de instructies op deze koppeling voor het maken van een HDInsight-cluster met Data Lake Storage Gen1 als de extra opslag ruimte.

Voor de instructies op de hierboven genoemde koppeling is Power shell vereist. Voordat u met deze instructies begint, moet u zich aanmelden bij uw Azure-account. Open op het bureau blad een nieuw Azure PowerShell venster en voer de volgende fragmenten in. Wanneer u wordt gevraagd om u aan te melden, moet u zich aanmelden als een van de abonnements beheerders/-eigenaar:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

De sjabloon implementeert deze resource typen:

* [Micro soft. data Lake Store/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Micro soft. HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Voorbeeld gegevens uploaden naar Data Lake Storage Gen1
De Resource Manager-sjabloon maakt een nieuw opslag account met Data Lake Storage Gen1 en koppelt het aan het HDInsight-cluster. U moet nu enkele voorbeeld gegevens uploaden naar Data Lake Storage Gen1. U hebt deze gegevens later in de zelf studie nodig om taken uit te voeren vanuit een HDInsight-cluster die toegang hebben tot gegevens in het opslag account met Data Lake Storage Gen1. Zie [een bestand uploaden naar Data Lake Storage gen1](data-lake-store-get-started-portal.md#uploaddata)voor instructies over het uploaden van gegevens. Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

## <a name="set-relevant-acls-on-the-sample-data"></a>Relevante Acl's instellen voor de voorbeeld gegevens
Om ervoor te zorgen dat de gegevens die u uploadt, toegankelijk zijn vanuit het HDInsight-cluster, moet u ervoor zorgen dat de Azure AD-toepassing die wordt gebruikt voor het instellen van de identiteit tussen het HDInsight-cluster en Data Lake Storage Gen1 toegang heeft tot het bestand of de map waartoe u toegang wilt krijgen. Volg de volgende stappen om dit te doen.

1. Zoek de naam van de Azure AD-toepassing die aan het HDInsight-cluster en het opslag account met Data Lake Storage Gen1 is gekoppeld. U kunt de naam ook controleren door de Blade HDInsight-cluster te openen die u hebt gemaakt met behulp van de Resource Manager-sjabloon, op het tabblad **cluster Azure AD-identiteit** te klikken en de waarde van de naam van de **Service-Principal weer te geven**.
2. Geef nu toegang tot deze Azure AD-toepassing op het bestand of de map waartoe u toegang wilt krijgen vanuit het HDInsight-cluster. Zie [gegevens beveiligen in data Lake Storage gen1](data-lake-store-secure-data.md#filepermissions)om de juiste acl's in te stellen voor het bestand of de map in data Lake Storage gen1.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Test taken uitvoeren op het HDInsight-cluster om Data Lake Storage Gen1 te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, kunt u test taken uitvoeren op het cluster om te testen of het HDInsight-cluster toegang heeft tot Data Lake Storage Gen1. Hiervoor wordt een voor beeld van een Hive-taak uitgevoerd waarmee een tabel wordt gemaakt met behulp van de voorbeeld gegevens die u eerder hebt geüpload naar uw opslag account met Data Lake Storage Gen1.

In deze sectie gaat u SSH naar een HDInsight Linux-cluster en voert u de voor beeld-Hive-query uit. Als u een Windows-client gebruikt, kunt u het beste **putty** gebruiken. deze kan worden gedownload van [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van putty.

1. Nadat de verbinding tot stand is gebracht, start u de Hive-CLI met behulp van de volgende opdracht:

   ```
   hive
   ```
2. Voer met behulp van de CLI de volgende instructies in om een nieuwe tabel met de naam **Voer tuigen** te maken met behulp van de voorbeeld gegevens in data Lake Storage gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   De uitvoer ziet er als volgt uit:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Toegang tot Data Lake Storage Gen1 met HDFS-opdrachten
Zodra u het HDInsight-cluster hebt geconfigureerd voor het gebruik van Data Lake Storage Gen1, kunt u de HDFS-shell opdrachten gebruiken om toegang te krijgen tot de Store.

In deze sectie gaat u SSH naar een HDInsight Linux-cluster en voert u de HDFS-opdrachten uit. Als u een Windows-client gebruikt, kunt u het beste **putty** gebruiken. deze kan worden gedownload van [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van putty.

Als de verbinding tot stand is gebracht, gebruikt u de volgende HDFS-bestands systeem opdracht om de bestanden in het opslag account met Data Lake Storage Gen1 weer te geven.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Hiermee wordt het bestand vermeld dat u eerder hebt geüpload naar Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

U kunt ook de `hdfs dfs -put` opdracht gebruiken om bestanden te uploaden naar Data Lake Storage gen1 en vervolgens gebruiken `hdfs dfs -ls` om te controleren of de bestanden zijn geüpload.


## <a name="next-steps"></a>Volgende stappen
* [Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
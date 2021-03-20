---
title: Power shell-HDInsight met Data Lake Storage Gen1-invoeg toepassings opslag-Azure
description: Meer informatie over het gebruik van Azure PowerShell voor het configureren van een HDInsight-cluster met Azure Data Lake Storage Gen1 als extra opslag.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b7cb9d5c5c2ca850678d3f3194a9af8de526ada4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103387"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Azure PowerShell gebruiken om een HDInsight-cluster te maken met Azure Data Lake Storage Gen1 (als extra opslag)

> [!div class="op_single_selector"]
> * [Portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Power shell gebruiken (voor standaard opslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Power shell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van Azure PowerShell voor het configureren van een HDInsight-cluster met Azure Data Lake Storage Gen1, **als extra opslag**. Zie [een hdinsight-cluster met data Lake Storage gen1 maken als standaard opslag](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)voor instructies over het maken van een hdinsight-cluster met data Lake Storage gen1 als standaard opslag.

> [!NOTE]
> Als u Data Lake Storage Gen1 als extra opslag voor HDInsight-cluster gaat gebruiken, raden we u ten zeerste aan dit te doen tijdens het maken van het cluster zoals beschreven in dit artikel. Het toevoegen van Data Lake Storage Gen1 als extra opslag aan een bestaand HDInsight-cluster is een ingewikkeld proces en gevoelig voor fouten.
>

Voor ondersteunde cluster typen kan Data Lake Storage Gen1 worden gebruikt als een standaard opslag of extra opslag account. Als Data Lake Storage Gen1 als extra opslag wordt gebruikt, is het standaard opslag account voor de clusters nog steeds Azure Blob Storage (WASB) en de aan het cluster gerelateerde bestanden (zoals Logboeken, enzovoort) naar de standaard opslag geschreven, terwijl de gegevens die u wilt verwerken, kunnen worden opgeslagen in een Data Lake Storage Gen1. Het gebruik van Data Lake Storage Gen1 als een extra opslag account heeft geen invloed op de prestaties of de mogelijkheid om te lezen/schrijven naar de opslag van het cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 voor HDInsight-cluster opslag gebruiken

Hier volgen enkele belang rijke aandachtspunten voor het gebruik van HDInsight met Data Lake Storage Gen1:

* Optie voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1 als extra opslag ruimte beschikbaar is voor HDInsight-versies 3,2, 3,4, 3,5 en 3,6.

Het configureren van HDInsight voor het werken met Data Lake Storage Gen1 met Power shell omvat de volgende stappen:

* Een Data Lake Storage Gen1-account maken 
* Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1
* Een HDInsight-cluster met verificatie voor het Data Lake Storage Gen1 maken
* Een test taak uitvoeren op het cluster

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/).
* **Windows SDK**. Deze kunt u [hier](https://dev.windows.com/en-us/downloads) downloaden. U kunt dit gebruiken om een beveiligings certificaat te maken.
* **Azure Active Directory Service-Principal**. De stappen in deze zelf studie bevatten instructies over het maken van een Service-Principal in azure AD. U moet echter een Azure AD-beheerder zijn om een service-principal te kunnen maken. Als u een Azure AD-beheerder bent, kunt u deze vereiste overs Laan en door gaan met de zelf studie.

    **Als u geen Azure AD-beheerder bent**, kunt u de stappen die nodig zijn om een service-principal te maken, niet uitvoeren. In dat geval moet uw Azure AD-beheerder eerst een service-principal maken voordat u met Data Lake Storage Gen1 een HDInsight-cluster kunt maken. Daarnaast moet de service-principal worden gemaakt met behulp van een certificaat, zoals wordt beschreven in [een service-principal maken met een certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken 
Volg deze stappen om een Data Lake Storage Gen1-account te maken.

1. Open op het bureau blad een nieuw Azure PowerShell venster en voer het volgende code fragment in. Wanneer u wordt gevraagd om u aan te melden, moet u zich aanmelden als een van de abonnements beheerder/eigenaar:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` het registreren van de data Lake Storage gen1 resource provider, is het mogelijk dat uw abonnement niet is goedgekeurd voor data Lake Storage gen1. Zorg ervoor dat u uw Azure-abonnement voor Data Lake Storage Gen1 inschakelt door deze [instructies](data-lake-store-get-started-portal.md)te volgen.
   >
   >
2. Een opslag account met Data Lake Storage Gen1 is gekoppeld aan een Azure-resource groep. Maak daarom eerst een Azure-resourcegroep.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    De uitvoer ziet er als volgt uit:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Maak een opslag account met Data Lake Storage Gen1. De account naam die u opgeeft, mag alleen kleine letters en cijfers bevatten.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Upload enkele voorbeeld gegevens naar Data Lake Storage Gen1. Dit wordt verderop in dit artikel gebruikt om te controleren of de gegevens toegankelijk zijn vanuit een HDInsight-cluster. Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1

Elk Azure-abonnement is gekoppeld aan een Azure Active Directory. Gebruikers en services die toegang hebben tot bronnen van het abonnement met behulp van de Azure Portal of Azure Resource Manager API moeten eerst worden geverifieerd met die Azure Active Directory. Toegang wordt verleend aan Azure-abonnementen en-services door hen de juiste rol toe te wijzen aan een Azure-resource.  Voor services identificeert een service-principal de service in de Azure Active Directory (Azure AD). In deze sectie wordt beschreven hoe u een toepassings service, zoals HDInsight, toegang verleent tot een Azure-resource (het opslag account met Data Lake Storage Gen1 die u eerder hebt gemaakt) door een service-principal te maken voor de toepassing en rollen toe te wijzen aan die via Azure PowerShell.

Als u Active Directory verificatie wilt instellen voor Data Lake Storage Gen1, moet u de volgende taken uitvoeren.

* Een zelfondertekend certificaat maken
* Een toepassing maken in Azure Active Directory en een Service-Principal

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Zorg ervoor dat [Windows SDK](https://dev.windows.com/en-us/downloads) is geïnstalleerd voordat u verdergaat met de stappen in deze sectie. U moet ook een map hebben gemaakt, zoals **C:\mycertdir**, waar het certificaat wordt gemaakt.

1. Ga in het Power shell-venster naar de locatie waar u Windows SDK hebt geïnstalleerd (doorgaans `C:\Program Files (x86)\Windows Kits\10\bin\x86` en gebruik het hulp programma [makecert][makecert] om een zelfondertekend certificaat en een persoonlijke sleutel te maken. Gebruik de volgende opdrachten.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    U wordt gevraagd om het wacht woord voor de persoonlijke sleutel in te voeren. Nadat de opdracht is uitgevoerd, ziet u een **CertBestand. CER** en **mykey. PVK** in de certificaat Directory die u hebt opgegeven.
2. Gebruik het hulp programma [Pvk2Pfx][pvk2pfx] om de PVK-en CER-bestanden te converteren die makecert gemaakt naar een pfx-bestand. Voer de volgende opdracht uit.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Geef het wacht woord voor de persoonlijke sleutel op dat u eerder hebt opgegeven. De waarde die u opgeeft voor de para meter **-io** is het wacht woord dat is gekoppeld aan het pfx-bestand. Nadat de opdracht is voltooid, ziet u ook een CertBestand. pfx in de door u opgegeven certificaat Directory.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Een Azure Active Directory en een service-principal maken

In deze sectie voert u de stappen uit voor het maken van een service-principal voor een Azure Active Directory toepassing, het toewijzen van een rol aan de Service-Principal en het verifiëren van de Service-Principal door een certificaat op te geven. Voer de volgende opdrachten uit om een toepassing te maken in Azure Active Directory.

1. Plak de volgende cmdlets in het Power shell-console venster. Zorg ervoor dat de waarde die u opgeeft voor de eigenschap **-DisplayName** uniek is. De waarden voor **-Home page** en **-IdentiferUris** zijn ook waarden van tijdelijke aanduidingen en worden niet geverifieerd.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Maak een service-principal met behulp van de toepassings-ID.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Verleen de Service-Principal toegang tot de Data Lake Storage Gen1 map en het bestand dat u vanuit het HDInsight-cluster gaat gebruiken. Het volgende code fragment biedt toegang tot de hoofdmap van het opslag account met Data Lake Storage Gen1 (waar u het voorbeeld gegevensbestand hebt gekopieerd) en het bestand zelf.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Een HDInsight Linux-cluster met Data Lake Storage Gen1 als extra opslag maken

In deze sectie maken we een HDInsight Hadoop Linux-cluster met Data Lake Storage Gen1 als extra opslag. Voor deze versie moeten het HDInsight-cluster en het opslag account met Data Lake Storage Gen1 op dezelfde locatie zijn.

1. Begin met het ophalen van de Tenant-ID van het abonnement. U hebt dit later nodig.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Voor deze release voor een Hadoop-cluster Data Lake Storage Gen1 kan alleen worden gebruikt als extra opslag ruimte voor het cluster. De standaard opslag is nog steeds de Azure Blob Storage (WASB). Daarom maken we eerst het opslag account en de opslag containers die vereist zijn voor het cluster.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. Maak het HDInsight-cluster. Gebruik de volgende cmdlets.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    Nadat de cmdlet is voltooid, ziet u een uitvoer waarin de cluster Details worden weer gegeven.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Test taken uitvoeren op het HDInsight-cluster om de Data Lake Storage Gen1 te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, kunt u test taken uitvoeren op het cluster om te testen of het HDInsight-cluster toegang heeft tot Data Lake Storage Gen1. Hiervoor wordt een voor beeld van een Hive-taak uitgevoerd waarmee een tabel wordt gemaakt met behulp van de voorbeeld gegevens die u eerder hebt geüpload naar uw opslag account met Data Lake Storage Gen1.

In deze sectie gaat u SSH naar het HDInsight Linux-cluster dat u hebt gemaakt en voert u de query een voor beeld-Hive uit.

* Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)als u een Windows-client gebruikt om in het cluster te SSHen.
* Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) als u een Linux-client gebruikt om in het cluster te SSHen.

1. Nadat de verbinding tot stand is gebracht, start u de Hive-CLI met behulp van de volgende opdracht:

    ```azurepowershell
    hive
    ```

2. Voer met behulp van de CLI de volgende instructies in om een nieuwe tabel met de naam **Voer tuigen** te maken met behulp van de voorbeeld gegevens in data Lake Storage gen1:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    ```output
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

In deze sectie gaat u SSH naar het HDInsight Linux-cluster dat u hebt gemaakt en voert u de HDFS-opdrachten uit.

* Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)als u een Windows-client gebruikt om in het cluster te SSHen.
* Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) als u een Linux-client gebruikt om in het cluster te SSHen.

Als de verbinding tot stand is gebracht, gebruikt u de volgende HDFS-bestands systeem opdracht om de bestanden in het opslag account met Data Lake Storage Gen1 weer te geven.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Hiermee wordt het bestand vermeld dat u eerder hebt geüpload naar Data Lake Storage Gen1.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

U kunt ook de `hdfs dfs -put` opdracht gebruiken om bestanden te uploaden naar Data Lake Storage gen1 en vervolgens gebruiken `hdfs dfs -ls` om te controleren of de bestanden zijn geüpload.

## <a name="see-also"></a>Zie ook
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal: een HDInsight-cluster maken voor het gebruik van Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx
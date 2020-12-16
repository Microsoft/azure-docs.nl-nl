---
title: Power shell-HDInsight-cluster met Data Lake Storage Gen1-Azure
description: Gebruik Azure PowerShell om Azure HDInsight-clusters te maken en te gebruiken met Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606773"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>HDInsight-clusters met Azure Data Lake Storage Gen1 als standaard opslag maken met behulp van Power shell

> [!div class="op_single_selector"]
> * [Azure Portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Power shell gebruiken (voor standaard opslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Power shell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Meer informatie over het gebruik van Azure PowerShell voor het configureren van Azure HDInsight-clusters met Azure Data Lake Storage Gen1 als standaard opslag. Zie [een hdinsight-cluster met data Lake Storage gen1 als extra opslag maken](data-lake-store-hdinsight-hadoop-use-powershell.md)voor instructies over het maken van een hdinsight-cluster met data Lake Storage gen1 als extra opslag.

Hier volgen enkele belang rijke aandachtspunten voor het gebruik van HDInsight met Data Lake Storage Gen1:

* De optie voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1 als standaard opslag is beschikbaar voor HDInsight-versie 3,5 en 3,6.

* De optie voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1 als standaard opslag is *niet beschikbaar* voor HDInsight Premium-clusters.

Volg de instructies in de volgende vijf secties om HDInsight te configureren voor gebruik met Data Lake Storage Gen1 met behulp van Power shell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelf studie begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

* **Een Azure-abonnement**: Ga naar [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 of hoger**: Zie [Power Shell installeren en configureren](/powershell/azure/).
* **Windows Software Development Kit (SDK)**: Ga naar [down loads en Hulpprogram ma's voor windows 10](https://dev.windows.com/downloads)om Windows SDK te installeren. De SDK wordt gebruikt voor het maken van een beveiligings certificaat.
* **Azure Active Directory Service-Principal**: in deze zelf studie wordt beschreven hoe u een Service-Principal maakt in azure Active Directory (Azure AD). Als u een Service-Principal wilt maken, moet u echter een Azure AD-beheerder zijn. Als u een beheerder bent, kunt u deze vereiste overs Laan en door gaan met de zelf studie.

    >[!NOTE]
    >U kunt alleen een service-principal maken als u een Azure AD-beheerder bent. Uw Azure AD-beheerder moet een service-principal maken voordat u met Data Lake Storage Gen1 een HDInsight-cluster kunt maken. De Service-Principal moet worden gemaakt met een certificaat, zoals beschreven in [Create a Service Principal with Certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Een Azure Data Lake Storage Gen1-account maken

Ga als volgt te werk om een Data Lake Storage Gen1-account te maken:

1. Open vanaf uw bureau blad een Power shell-venster en voer de onderstaande fragmenten in. Wanneer u wordt gevraagd om u aan te melden, meldt u zich aan als een van de abonnements beheerders of-eigen aren. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Als u de resource provider van Data Lake Storage Gen1 registreert en er een fout melding ziet die vergelijkbaar `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` is met, is uw abonnement mogelijk niet goedgekeurd voor data Lake Storage gen1. Als u uw Azure-abonnement voor Data Lake Storage Gen1 wilt inschakelen, volgt u de instructies in [aan de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md).
    >

2. Een Data Lake Storage Gen1-account is gekoppeld aan een Azure-resource groep. Begin met het maken van een resource groep.

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

3. Maak een Data Lake Storage Gen1-account. De account naam die u opgeeft, mag alleen kleine letters en cijfers bevatten.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Als u Data Lake Storage Gen1 als standaard opslag wilt gebruiken, moet u een basispad opgeven waarnaar de cluster-specifieke bestanden worden gekopieerd tijdens het maken van het cluster. Gebruik de volgende cmdlets om een pad naar een hoofdmap te maken, dat **/clusters/hdiadlcluster** is in het fragment:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Verificatie instellen voor op rollen gebaseerde toegang tot Data Lake Storage Gen1
Elk Azure-abonnement is gekoppeld aan een Azure AD-entiteit. Gebruikers en services die toegang hebben tot de resources van het abonnement met behulp van de Azure Portal of de Azure Resource Manager-API moeten eerst worden geverifieerd met Azure AD. Toegang wordt verleend aan Azure-abonnementen en-services door hen de juiste rol toe te wijzen aan een Azure-resource. Voor services identificeert een service-principal de service in azure AD.

In deze sectie ziet u hoe u een toepassings service kunt verlenen, zoals HDInsight, toegang tot een Azure-resource (het Data Lake Storage Gen1-account dat u eerder hebt gemaakt). Hiervoor maakt u een service-principal voor de toepassing en wijst u rollen toe via Power shell.

Voer de taken uit in de volgende twee secties om Active Directory verificatie voor Data Lake Storage Gen1 in te stellen.

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken
Zorg ervoor dat [Windows SDK](https://dev.windows.com/en-us/downloads) is geïnstalleerd voordat u verdergaat met de stappen in deze sectie. U moet ook een map hebben gemaakt, zoals *C:\mycertdir*, waar u het certificaat maakt.

1. Ga in het Power shell-venster naar de locatie waar u Windows SDK hebt geïnstalleerd (doorgaans *C:\Program Files (x86) \Windows Kits\10\bin\x86*) en gebruik het hulp programma [makecert][makecert] om een zelfondertekend certificaat en een persoonlijke sleutel te maken. Gebruik de volgende opdrachten:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    U wordt gevraagd om het wacht woord voor de persoonlijke sleutel in te voeren. Nadat de opdracht is uitgevoerd, ziet u **CertBestand. CER** en **mykey. PVK** in de door u opgegeven certificaat Directory.
2. Gebruik het hulp programma [Pvk2Pfx][pvk2pfx] om de PVK-en CER-bestanden te converteren die makecert gemaakt naar een pfx-bestand. Voer de volgende opdracht uit:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Wanneer u hierom wordt gevraagd, voert u het wacht woord voor de persoonlijke sleutel in dat u eerder hebt opgegeven. De waarde die u opgeeft voor de para meter **-io** is het wacht woord dat is gekoppeld aan het pfx-bestand. Nadat de opdracht is voltooid, ziet u ook een **CertBestand. pfx** in de door u opgegeven certificaat Directory.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Een Azure AD-en een service-principal maken
In deze sectie maakt u een service-principal voor een Azure AD-toepassing, wijst u een rol toe aan de Service-Principal en verifieert u als Service-Principal door een certificaat op te geven. Als u een toepassing in azure AD wilt maken, voert u de volgende opdrachten uit:

1. Plak de volgende cmdlets in het Power shell-console venster. Zorg ervoor dat de waarde die u opgeeft voor de eigenschap **-DisplayName** uniek is. De waarden voor **-Home page** en **-IdentiferUris** zijn waarden van tijdelijke aanduidingen en worden niet geverifieerd.

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

2. Een service-principal maken met behulp van de toepassings-ID.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Verleen de Service-Principal toegang tot de Data Lake Storage Gen1 root en alle mappen in het hoofdpad dat u eerder hebt opgegeven. Gebruik de volgende cmdlets:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Een HDInsight Linux-cluster met Data Lake Storage Gen1 als standaard opslag maken

In deze sectie maakt u een HDInsight Hadoop Linux-cluster met Data Lake Storage Gen1 als standaard opslag. Voor deze release moet het HDInsight-cluster en het data Lake Storage gen1 zich op dezelfde locatie bestaan.

1. Haal de Tenant-ID van het abonnement op en sla deze op om later te gebruiken.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Maak het HDInsight-cluster met behulp van de volgende cmdlets:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Nadat de cmdlet is voltooid, ziet u een uitvoer waarin de cluster Details worden weer gegeven.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Test taken uitvoeren op het HDInsight-cluster om Data Lake Storage Gen1 te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, kunt u test taken hierop uitvoeren om ervoor te zorgen dat het toegang tot Data Lake Storage Gen1 kan krijgen. Hiervoor voert u een voor beeld van een Hive-taak uit om een tabel te maken die gebruikmaakt van de voorbeeld gegevens die al beschikbaar zijn in Data Lake Storage Gen1 op *\<cluster root> /example/data/sample.log*.

In deze sectie maakt u een SSH-verbinding (Secure Shell) in het HDInsight Linux-cluster dat u hebt gemaakt en voert u vervolgens een Hive-query uit.

* Als u een Windows-client gebruikt om een SSH-verbinding te maken met het cluster, raadpleegt u [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Als u een Linux-client gebruikt om een SSH-verbinding te maken met het cluster, raadpleegt u [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Nadat u de verbinding hebt gemaakt, start u de Hive-opdracht regel interface (CLI) met behulp van de volgende opdracht:

    ```powershell
    hive
    ```

2. Gebruik de CLI om de volgende instructies in te voeren om een nieuwe tabel met de naam **Voer tuigen** te maken met behulp van de voorbeeld gegevens in data Lake Storage gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

U ziet de uitvoer van de query op de SSH-console.

>[!NOTE]
>Het pad naar de voorbeeld gegevens in de voor gaande CREATE TABLE opdracht is `adl:///example/data/` , waarbij `adl:///` de hoofdmap van het cluster is. Na het voor beeld van de cluster basis die in deze zelf studie is opgegeven, is de opdracht `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . U kunt het kortere alternatief gebruiken of het volledige pad naar de hoofdmap van het cluster opgeven.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Toegang tot Data Lake Storage Gen1 met behulp van HDFS-opdrachten
Nadat u het HDInsight-cluster hebt geconfigureerd voor het gebruik van Data Lake Storage Gen1, kunt u de shell-opdrachten Hadoop Distributed File System (HDFS) gebruiken om toegang te krijgen tot de Store.

In deze sectie maakt u een SSH-verbinding in het HDInsight Linux-cluster dat u hebt gemaakt en voert u vervolgens de HDFS-opdrachten uit.

* Als u een Windows-client gebruikt om een SSH-verbinding te maken met het cluster, raadpleegt u [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Als u een Linux-client gebruikt om een SSH-verbinding te maken met het cluster, raadpleegt u [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Nadat u de verbinding hebt gemaakt, kunt u de bestanden in Data Lake Storage Gen1 weer geven met behulp van de volgende HDFS File System-opdracht.

```azurepowershell
hdfs dfs -ls adl:///
```

U kunt ook de `hdfs dfs -put` opdracht gebruiken om bestanden te uploaden naar Data Lake Storage gen1 en vervolgens gebruiken `hdfs dfs -ls` om te controleren of de bestanden zijn geüpload.

## <a name="see-also"></a>Zie tevens
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure Portal: een HDInsight-cluster maken voor het gebruik van Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
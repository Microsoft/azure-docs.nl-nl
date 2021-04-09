---
title: Data Lake Storage Gen1 gebruiken met Hadoop in azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Data Lake Storage Gen1 en voor het opslaan van resultaten van de analyse.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945406"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters

> [!Note]
> Implementeer nieuwe HDInsight-clusters met behulp van [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) voor verbeterde prestaties en nieuwe functies.

Als u gegevens in een HDInsight-cluster wilt analyseren, kunt u de gegevens opslaan in [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)of [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Met alle opslag opties kunt u HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan.

In dit artikel leert u hoe Data Lake Storage Gen1 werkt met HDInsight-clusters. Zie [Azure Blob-opslag gebruiken met Azure hdinsight-clusters](hdinsight-hadoop-use-blob-storage.md)voor meer informatie over de werking van Azure Blob-opslag met HDInsight-clusters. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!NOTE]  
> Data Lake Storage Gen1 wordt altijd geopend via een beveiligd kanaal, zodat er geen `adls` bestandssysteem naam is. U gebruikt altijd `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Beschik baarheid voor HDInsight-clusters

Apache Hadoop ondersteunt een principe van het standaard bestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Geef tijdens het proces voor het maken van HDInsight-clusters een BLOB-container op in Azure Storage als het standaard bestandssysteem. Of met HDInsight 3,5 en nieuwere versies kunt u Azure Blob-opslag of Azure Data Lake Storage Gen1 selecteren als het standaard bestandssysteem met een paar uitzonde ringen. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio.

HDInsight-clusters kunnen Data Lake Storage Gen1 op twee manieren gebruiken:

* als standaardopslag
* Als extra opslag, met Azure Blob Storage als standaard opslag.

Op dit moment worden slechts enkele typen HDInsight-clusters ondersteund met Data Lake Storage Gen1 als standaard opslag en extra opslag accounts:

| HDInsight-clustertype | Data Lake Storage Gen1 als standaard opslag | Data Lake Storage Gen1 als extra opslag ruimte| Notities |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight-versie 4,0 | Nee | Nee |ADLS Gen1 wordt niet ondersteund met HDInsight 4,0 |
| HDInsight-versie 3.6 | Ja | Ja | Behalve HBase|
| HDInsight-versie 3.5 | Ja | Ja | Behalve HBase|
| HDInsight-versie 3.4 | Nee | Ja | |
| HDInsight-versie 3.3 | Nee | Nee | |
| HDInsight-versie 3.2 | Nee | Ja | |
| Storm | | |U kunt Data Lake Storage Gen1 gebruiken om gegevens van een storm-topologie te schrijven. U kunt Data Lake Storage Gen1 ook gebruiken voor referentie gegevens die vervolgens kunnen worden gelezen door een storm-topologie.|

> [!WARNING]  
> HDInsight HBase wordt niet ondersteund met Azure Data Lake Storage Gen1

Het gebruik van Data Lake Storage Gen1 als een extra opslag account heeft geen invloed op de prestaties. Of de mogelijkheid om te lezen van of te schrijven naar Azure Blob-opslag van het cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 als standaard opslag gebruiken

Wanneer HDInsight met Data Lake Storage Gen1 als standaard opslag is geïmplementeerd, worden de cluster bestanden opgeslagen in `adl://mydatalakestore/<cluster_root_path>/` , waarbij `<cluster_root_path>` de naam is van een map die u in data Lake Storage maakt. Als u voor elk cluster een pad naar een hoofdmap opgeeft, kunt u hetzelfde Data Lake Storage-account voor meer dan één cluster gebruiken. U kunt dus instellingen hebben waarbij:

* Cluster1 het pad `adl://mydatalakestore/cluster1storage` kan gebruiken
* Cluster2 het pad `adl://mydatalakestore/cluster2storage` kan gebruiken

U ziet dat beide clusters gebruikmaken van hetzelfde Data Lake Storage Gen1 account **mydatalakestore**. Elk cluster heeft toegang tot een eigen hoofd bestandssysteem in Data Lake Storage. De Azure Portal implementatie-ervaring vraagt u een mapnaam te gebruiken zoals **/clusters/ \<clustername>** voor het hoofdpad.

Als u Data Lake Storage Gen1 als standaard opslag wilt gebruiken, moet u de Service-Principal toegang verlenen tot de volgende paden:

* De hoofdmap van het Data Lake Storage Gen1-account.  Bijvoorbeeld: adl://mydatalakestore/.
* De map voor alle mappen van het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters.
* De map voor het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters/cluster1storage.

Zie Data Lake Storage toegang configureren voor meer informatie over het maken van een Service-Principal en het verlenen van toegang.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Een certificaat uit Azure-sleutel kluis extra heren voor het maken van een cluster

Als het certificaat voor uw service-principal is opgeslagen in Azure Key Vault, moet u het certificaat converteren naar de juiste indeling. De volgende code fragmenten laten zien hoe u de conversie kunt uitvoeren.

Down load eerst het certificaat van Key Vault en pak het uit `SecretValueText` .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Converteer vervolgens de `SecretValueText` naar een certificaat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Vervolgens kunt u de gebruiken `$identityCertificate` om een nieuw cluster te implementeren, zoals in het volgende code fragment:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 als extra opslag gebruiken

U kunt Data Lake Storage Gen1 ook gebruiken als extra opslag ruimte voor het cluster. In dergelijke gevallen kan de standaard opslag van het cluster een Azure Blob-opslag of een Azure Data Lake Storage Gen1-account zijn. Wanneer u HDInsight-taken uitvoert voor de gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1 als extra opslag, gebruikt u het volledig gekwalificeerde pad. Bijvoorbeeld:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

De URL bevat nu geen **cluster_root_path** . Dat komt omdat Data Lake Storage in dit geval geen standaard opslag is. U hoeft alleen maar het pad naar de bestanden op te geven.

Als u een Data Lake Storage Gen1 als extra opslag ruimte wilt gebruiken, moet u de Service-Principal toegang verlenen tot de paden waar uw bestanden worden opgeslagen.  Bijvoorbeeld:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Zie Data Lake Storage toegang configureren voor meer informatie over het maken van een Service-Principal en het verlenen van toegang.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Meer dan één Data Lake Storage Gen1 account gebruiken

Het toevoegen van een Data Lake Storage-account als extra en het toevoegen van meer dan een Data Lake Storage accounts kan worden uitgevoerd. Geef het HDInsight-cluster machtigingen voor gegevens in een of meer Data Lake Storage accounts. Zie Data Lake Storage Gen1 toegang configureren hieronder.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 toegang configureren

Als u Azure Data Lake Storage Gen1 toegang vanaf uw HDInsight-cluster wilt configureren, moet u een service-principal voor Azure Active Directory (Azure AD) hebben. Alleen een Azure AD-beheerder kan een service-principal maken. De service-principal moet worden gemaakt met een certificaat. Zie voor meer informatie [Snelstartgids: Clusters instellen in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) en [Service-principal maken met zelfondertekend certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Als u Azure Data Lake Storage Gen1 als extra opslag voor HDInsight-cluster gaat gebruiken, raden we u ten zeerste aan dit te doen tijdens het maken van het cluster zoals beschreven in dit artikel. Het is niet mogelijk om Azure Data Lake Storage Gen1 als extra opslag toe te voegen aan een bestaand HDInsight-cluster.

Zie [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over het model voor toegangs beheer.

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren om toegang te krijgen tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster met:

    ```
    adl:///<file path>
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voor beelden van gegevens toegang

Voor beelden zijn gebaseerd op een [SSH-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofd knooppunt van het cluster. De voor beelden gebruiken alle drie de URI-schema's. Vervang `DATALAKEACCOUNT` en `CLUSTERNAME` door de relevante waarden.

#### <a name="a-few-hdfs-commands"></a>Enkele hdfs-opdrachten

1. Maak een bestand op lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen in de cluster opslag.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens uit de lokale opslag kopiëren naar de cluster opslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Mapinhoud weer geven in cluster opslag.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Er worden drie bestands locaties weer gegeven voor illustratie doeleinden. Gebruik slechts één van de vermeldingen voor daad werkelijke uitvoering `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Pad naar opslag van Ambari identificeren

Als u het volledige pad naar het geconfigureerde standaard archief wilt identificeren, gaat u naar **HDFS**-  >  **configuraties** en voert u `fs.defaultFS` in het vak invoer filter in.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>HDInsight-clusters maken met toegang tot Data Lake Storage Gen1

Gebruik de volgende koppelingen voor gedetailleerde instructies over het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1.

* [Portal gebruiken](./hdinsight-hadoop-provision-linux-clusters.md)
* [Power shell gebruiken (met Data Lake Storage Gen1 als standaard opslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Power shell gebruiken (met Data Lake Storage Gen1 als extra opslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sjablonen gebruiken](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Het HDInsight-certificaat voor toegang tot Data Lake Storage Gen1 vernieuwen

In het volgende voor beeld van een Power shell-code wordt een certificaat uit een lokaal bestand of Azure Key Vault gelezen en wordt uw HDInsight-cluster bijgewerkt met het nieuwe certificaat voor toegang tot Azure Data Lake Storage Gen1. Geef uw eigen HDInsight-cluster naam, de naam van de resource groep, de abonnements-ID, `app ID` het lokale pad naar het certificaat op. Typ het wacht woord wanneer u daarom wordt gevraagd.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HDFS-compatibele Azure Data Lake Storage Gen1 met HDInsight kunt gebruiken. Met deze opslag kunt u flexibele, langdurige, archiverings oplossingen voor gegevens verzameling bouwen. En gebruik HDInsight om de informatie in de opgeslagen gestructureerde en ongestructureerde gegevens te ontgrendelen.

Zie voor meer informatie:

* [Snelstartgids: Clusters instellen in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
* [Een HDInsight-cluster maken om Data Lake Storage Gen1 te gebruiken met behulp van de Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Shared Access signatures voor Azure Blob Storage gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
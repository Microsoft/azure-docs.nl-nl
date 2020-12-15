---
title: Toegang beperken met behulp van hand tekeningen voor gedeelde toegang-Azure HDInsight
description: Meer informatie over het gebruik van hand tekeningen voor gedeelde toegang voor het beperken van HDInsight-toegang tot gegevens die zijn opgeslagen in Azure Blob Storage.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 141db7feee987b7fffc578e19c60bd94ad56d239
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511634"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Shared Access signatures voor Azure Blob Storage gebruiken om de toegang tot gegevens in HDInsight te beperken

HDInsight heeft volledige toegang tot de gegevens in de Azure Blob Storage-accounts die zijn gekoppeld aan het cluster. U kunt hand tekeningen voor gedeelde toegang gebruiken voor de BLOB-container om de toegang tot de gegevens te beperken. Shared Access signatures (SAS) zijn een functie van Azure Blob Storage-accounts waarmee u de toegang tot gegevens kunt beperken. U kunt bijvoorbeeld alleen-lezen toegang bieden tot gegevens.

> [!IMPORTANT]  
> Voor een oplossing met Apache zwerver kunt u overwegen om gebruik te maken van HDInsight die lid is van een domein. Zie het [HDInsight-document domein-gekoppeld configureren](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) voor meer informatie.

> [!WARNING]  
> HDInsight moet volledige toegang hebben tot de standaard opslag voor het cluster.

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Een bestaande [opslag container](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Als u Power shell gebruikt, hebt u de [AZ-module](/powershell/azure/)nodig.

* Als u Azure CLI wilt gebruiken en u deze nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

* Als u [python](https://www.python.org/downloads/), versie 2,7 of hoger gebruikt.

* Als C# wordt gebruikt, moet Visual Studio versie 2013 of hoger zijn.

* Het URI-schema voor uw opslag account. Dit schema is `wasb://` voor Azure Blob Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Blob-opslag, zou de URI zijn `wasbs://` .

* Een bestaand HDInsight-cluster waaraan een Shared Access Signature moet worden toegevoegd. Als dat niet het geval is, kunt u Azure PowerShell gebruiken om een cluster te maken en een Shared Access Signature toe te voegen tijdens het maken van het cluster.

* De voorbeeld bestanden van [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Deze opslag plaats bevat de volgende items:

  * Een Visual Studio-project dat een opslag container, opgeslagen beleid en SAS kan maken voor gebruik met HDInsight
  * Een python-script dat een opslag container, opgeslagen beleid en SAS kan maken voor gebruik met HDInsight
  * Een Power shell-script waarmee een HDInsight-cluster kan worden gemaakt en geconfigureerd voor het gebruik van de SAS. Hieronder wordt een bijgewerkte versie gebruikt.
  * Een voorbeeld bestand: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Shared Access Signatures

Er zijn twee soorten hand tekeningen voor gedeelde toegang:

* `Ad hoc`: De begin tijd, verloop tijd en machtigingen voor de SA'S zijn allemaal opgegeven op de SAS-URI.

* `Stored access policy`: Er is een opgeslagen toegangs beleid gedefinieerd in een resource container, zoals een BLOB-container. Een beleid kan worden gebruikt om beperkingen voor een of meer Shared Access-hand tekeningen te beheren. Wanneer u een SAS koppelt aan een opgeslagen toegangs beleid, neemt de SAS de beperkingen over, de start tijd, de verloop tijd en de machtigingen die zijn gedefinieerd voor het opgeslagen toegangs beleid.

Het verschil tussen de twee formulieren is van belang voor een belang rijk scenario: intrekken. Een SAS is een URL, dus iedereen die de SAS verkrijgt, kan deze gebruiken. Het maakt niet uit wie het had aangevraagd om te beginnen. Als een SAS openbaar wordt gepubliceerd, kan deze worden gebruikt door iedereen ter wereld. Een gedistribueerde SAS is geldig tot er een van de volgende vier dingen gebeurt:

1. De verloop tijd die is opgegeven voor de SA'S, is bereikt.

2. De verloop tijd die is opgegeven voor het opgeslagen toegangs beleid waarnaar de SA'S verwijzen, is bereikt. De volgende scenario's zorgen ervoor dat de verloop tijd wordt bereikt:

    * Het tijds interval is verstreken.
    * Het beleid voor opgeslagen toegang is gewijzigd om verloop tijd in het verleden te hebben. Het wijzigen van de verloop tijd is een manier om de SAS in te trekken.

3. Het opgeslagen toegangs beleid waarnaar wordt verwezen door de SA'S, wordt verwijderd. Dit is een andere manier om de SAS in te trekken. Als u het opgeslagen toegangs beleid met dezelfde naam opnieuw maakt, zijn alle SAS-tokens voor het vorige beleid geldig (als de verloop tijd op de SA'S niet is verstreken). Als u van plan bent om de SAS in te trekken, moet u een andere naam gebruiken als u het toegangs beleid opnieuw maakt met een verloop tijd in de toekomst.

4. De account sleutel die is gebruikt voor het maken van de SA'S, wordt opnieuw gegenereerd. Het opnieuw genereren van de sleutel zorgt ervoor dat alle toepassingen die de vorige sleutel gebruiken, niet kunnen worden geverifieerd. Werk alle onderdelen bij naar de nieuwe sleutel.

> [!IMPORTANT]  
> De URI van een Shared Access-hand tekening is gekoppeld aan de account sleutel die wordt gebruikt om de hand tekening te maken en het bijbehorende opgeslagen toegangs beleid (indien van toepassing). Als er geen opgeslagen toegangs beleid is opgegeven, is de enige manier om een hand tekening voor gedeelde toegang in te trekken door de account sleutel te wijzigen.

U wordt aangeraden altijd opgeslagen toegangs beleid te gebruiken. Wanneer u een opgeslagen beleid gebruikt, kunt u de hand tekeningen intrekken of de verloop datum naar behoefte verlengen. In de stappen in dit document wordt opgeslagen toegangs beleid gebruikt voor het genereren van SAS.

Zie [informatie over het SAS-model](../storage/common/storage-sas-overview.md)voor meer informatie over de hand tekeningen voor gedeelde toegang.

## <a name="create-a-stored-policy-and-sas"></a>Een opgeslagen beleid en SAS maken

Sla het SAS-token op dat aan het einde van elke methode is geproduceerd. Het token ziet er ongeveer uit als in de volgende uitvoer:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>PowerShell gebruiken

Vervang `RESOURCEGROUP` , `STORAGEACCOUNT` , en `STORAGECONTAINER` door de juiste waarden voor uw bestaande opslag container. Wijzig de map naar `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` of herzie de `-File` para meter om het absolute pad voor te bevatten `Set-AzStorageblobcontent` . Voer de volgende Power shell-opdracht in:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

Het gebruik van variabelen in deze sectie is gebaseerd op een Windows-omgeving. Er zijn kleine variaties nodig voor bash of andere omgevingen.

1. Vervang `STORAGEACCOUNT` en door `STORAGECONTAINER` de juiste waarden voor uw bestaande opslag container.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Stel de opgehaalde primaire sleutel in op een variabele voor later gebruik. Vervang door `PRIMARYKEY` de opgehaalde waarde in de vorige stap en voer de volgende opdracht in:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Wijzig de map naar `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` of herzie de `--file` para meter om het absolute pad voor te bevatten `az storage blob upload` . Voer de overige opdrachten uit:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Python gebruiken

Open het `SASToken.py` bestand en vervang `storage_account_name` , `storage_account_key` , en `storage_container_name` met de juiste waarden voor uw bestaande opslag container en voer het script uit.

Mogelijk moet u uitvoeren `pip install --upgrade azure-storage` als het fout bericht wordt weer gegeven `ImportError: No module named azure.storage` .

### <a name="using-c"></a>Met C\#

1. Open de oplossing in Visual Studio.

2. Klik in Solution Explorer met de rechter muisknop op het project **SASExample** en selecteer **Eigenschappen**.

3. Selecteer **instellingen** en voeg waarden toe voor de volgende vermeldingen:

    |Item |Beschrijving |
    |---|---|
    |StorageConnectionString|De connection string voor het opslag account waarvoor u een opgeslagen beleid en SA'S wilt maken. De notatie moet zijn `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` als `myaccount` de naam van uw opslag account en `mykey` de sleutel voor het opslag account is.|
    |ContainerName|De container in het opslag account waartoe u de toegang wilt beperken.|
    |SASPolicyName|De naam die moet worden gebruikt om het opgeslagen beleid te maken.|
    |FileToUpload|Het pad naar een bestand dat naar de container wordt geüpload.|

4. Voer het project uit. Sla het SAS-beleids token, de naam van het opslag account en de container naam op. Deze waarden worden gebruikt bij het koppelen van het opslag account aan uw HDInsight-cluster.

## <a name="use-the-sas-with-hdinsight"></a>De SAS met HDInsight gebruiken

Wanneer u een HDInsight-cluster maakt, moet u een primair opslag account opgeven. U kunt ook extra opslag accounts opgeven. Beide methoden voor het toevoegen van opslag hebben volledige toegang tot de opslag accounts en containers die worden gebruikt.

Gebruik een Shared Access Signature om de toegang tot de container te beperken. Voeg een aangepast item toe aan de configuratie van de **kern site** voor het cluster. U kunt de vermelding toevoegen tijdens het maken van het cluster met behulp van Power shell of nadat het cluster is gemaakt met Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Een cluster maken dat gebruikmaakt van de SAS

Vervang,,,, `CLUSTERNAME` `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` en `TOKEN` door de juiste waarden. Voer de Power shell-opdrachten in:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Wanneer u wordt gevraagd naar de gebruikers naam en het wacht woord voor HTTP/s of SSH, moet u een wacht woord opgeven dat voldoet aan de volgende criteria:
>
> * Moet ten minste 10 tekens lang zijn.
> * Moet ten minste één cijfer bevatten.
> * Moet ten minste één niet-alfanumeriek teken bevatten.
> * Moet ten minste één hoofd letter of kleine letters bevatten.

Het duurt even voordat dit script is voltooid, meestal ongeveer 15 minuten. Wanneer het script is voltooid zonder fouten, is het cluster gemaakt.

### <a name="use-the-sas-with-an-existing-cluster"></a>De SAS gebruiken met een bestaand cluster

Als u een bestaand cluster hebt, kunt u de SAS toevoegen aan de **kern site** configuratie met behulp van de volgende stappen:

1. Open de Ambari-webgebruikersinterface voor uw cluster. Het adres voor deze pagina is `https://YOURCLUSTERNAME.azurehdinsight.net` . Wanneer u hierom wordt gevraagd, moet u zich bij het cluster aanmelden met de beheerders naam (admin) en het wacht woord dat u hebt gebruikt bij het maken van het cluster.

1. Navigeer naar **HDFS**  >  **configs**  >  **Geavanceerde**  >  **aangepaste kern-site**.

1. Vouw de sectie **aangepaste kern site** uit, blader naar het einde en selecteer vervolgens **eigenschap toevoegen...**. Gebruik de volgende waarden voor **sleutel** en **waarde**:

    * **Sleutel**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Waarde**: de sa's die door een van de eerder uitgevoerde methoden zijn geretourneerd.

    Vervang door `CONTAINERNAME` de naam van de container die u hebt gebruikt in combi natie met de C#-of SAS-toepassing. Vervang door `STORAGEACCOUNTNAME` de naam van het opslag account dat u hebt gebruikt.

    Selecteer **toevoegen** om deze sleutel en waarde op te slaan

1. Selecteer de knop **Opslaan** om de configuratie wijzigingen op te slaan. Als hierom wordt gevraagd, voegt u een beschrijving van de wijziging toe (bijvoorbeeld het toevoegen van SAS-opslag toegang) en selecteert u vervolgens **Opslaan**.

    Selecteer **OK** wanneer de wijzigingen zijn voltooid.

   > [!IMPORTANT]  
   > U moet verschillende services opnieuw starten voordat de wijzigingen van kracht worden.

1. Er wordt een vervolg keuzelijst **opnieuw opstarten** weer gegeven. Selecteer in de vervolg keuzelijst **alle betrokken opnieuw opstarten** en __Bevestig opnieuw opstarten__.

    Herhaal dit proces voor **MapReduce2** en **garens**.

1. Zodra de services opnieuw zijn opgestart, selecteert u deze en schakelt u de onderhouds modus uit in de vervolg keuzelijst **service acties** .

## <a name="test-restricted-access"></a>Beperkte toegang testen

Gebruik de volgende stappen om te controleren of u alleen items kunt lezen en weer geven in het SAS-opslag account.

1. Maak verbinding met het cluster. Vervang door `CLUSTERNAME` de naam van uw cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Als u de inhoud van de container wilt weer geven, gebruikt u de volgende opdracht bij de prompt:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Vervang door `SASCONTAINER` de naam van de container die is gemaakt voor het SAS-opslag account. Vervang door `SASACCOUNTNAME` de naam van het opslag account dat wordt gebruikt voor de SAS.

    De lijst bevat het bestand dat is geüpload toen de container en SA'S werden gemaakt.

3. Gebruik de volgende opdracht om te controleren of u de inhoud van het bestand kunt lezen. Vervang de `SASCONTAINER` en `SASACCOUNTNAME` als in de vorige stap. Vervang door `sample.log` de naam van het bestand dat wordt weer gegeven in de vorige opdracht:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Met deze opdracht wordt de inhoud van het bestand weer gegeven.

4. Gebruik de volgende opdracht om het bestand te downloaden naar het lokale bestands systeem:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Met deze opdracht wordt het bestand gedownload naar een lokaal bestand met de naam **testfile.txt**.

5. Gebruik de volgende opdracht om het lokale bestand te uploaden naar een nieuw bestand met de naam **testupload.txt** op de SAS-opslag:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    U krijgt een bericht te zien dat vergelijkbaar is met de volgende tekst:

    ```output
    put: java.io.IOException
    ```

    Deze fout treedt op omdat de opslag locatie alleen-lezen is en alleen lijst. Gebruik de volgende opdracht om de gegevens op de standaard opslag voor het cluster te plaatsen, wat schrijfbaar is:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Deze keer wordt de bewerking voltooid.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u opslag met beperkte toegang kunt toevoegen aan uw HDInsight-cluster, kunt u meer informatie krijgen over andere manieren om te werken met gegevens op uw cluster:

* [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gebruikers machtigen voor Apache Ambari-weergaven](hdinsight-authorize-users-to-ambari.md)

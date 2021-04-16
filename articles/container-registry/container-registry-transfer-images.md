---
title: Artefacten overdragen
description: Verzamelingen van afbeeldingen of andere artefacten overdragen van het ene containerregister naar een ander register door een overdrachtspijplijn te maken met behulp van Azure-opslagaccounts
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: e921880eb0b8ae5a38e69c9c0045f6a26d84084d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497979"
---
# <a name="transfer-artifacts-to-another-registry"></a>Artefacten overdragen naar een ander register

In dit artikel wordt beschreven hoe u verzamelingen met afbeeldingen of andere registerartefacten van het ene Azure-containerregister naar een ander register kunt overdragen. De bron- en doelregisters kunnen zich in dezelfde of verschillende abonnementen, Active Directory-tenants, Azure-clouds of fysiek losgekoppelde clouds. 

Als u artefacten wilt  overdragen, maakt u een overdrachtspijplijn die artefacten repliceert tussen twee registers met behulp van [blobopslag:](../storage/blobs/storage-blobs-introduction.md)

* Artefacten uit een bronregister worden geëxporteerd naar een blob in een bronopslagaccount 
* De blob wordt gekopieerd van het bronopslagaccount naar een doelopslagaccount
* De blob in het doelopslagaccount wordt geïmporteerd als artefacten in het doelregister. U kunt de importpijplijn zo instellen dat deze wordt getriggerd wanneer de artefactblob wordt bijgewerkt in de doelopslag.

Overdracht is ideaal voor het kopiëren van inhoud tussen twee Azure-containerregisters in fysiek losgekoppelde clouds, gemediateerd door opslagaccounts in elke cloud. Als u in plaats daarvan afbeeldingen wilt kopiëren uit containerregisters in verbonden [](container-registry-import-images.md) clouds, waaronder Docker Hub en andere cloudleveranciers, wordt het importeren van afbeeldingen aanbevolen.

In dit artikel gebruikt u Azure Resource Manager sjabloonimplementaties om de overdrachtspijplijn te maken en uit te voeren. De Azure CLI wordt gebruikt voor het inrichten van de bijbehorende resources, zoals opslaggeheimen. Azure CLI versie 2.2.0 of hoger wordt aanbevolen. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren][azure-cli].

Deze functie is beschikbaar in de **servicelaag Premium** Container Registry. Zie Azure Container Registry servicelagen voor meer [informatie over registerservicelagen en -limieten.](container-registry-skus.md)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* **Containerregisters: u** hebt een bestaand bronregister met artefacten nodig om over te dragen en een doelregister. ACR-overdracht is bedoeld voor verplaatsing tussen fysiek losgekoppelde clouds. Voor het testen kunnen de bron- en doelregisters zich in hetzelfde of een ander Azure-abonnement, active directory-tenant of cloud hebben. 

   Als u een register wilt maken, gaat u naar [Quickstart: Een privécontainerregister maken met behulp van de Azure CLI.](container-registry-get-started-azure-cli.md) 
* **Opslagaccounts:** maak bron- en doelopslagaccounts in een abonnement en locatie naar keuze. Voor testdoeleinden kunt u hetzelfde abonnement of dezelfde abonnementen gebruiken als uw bron- en doelregisters. Voor scenario's in meerdere cloudomgevingen maakt u doorgaans een afzonderlijk opslagaccount in elke cloud. 

  Maak indien nodig de opslagaccounts met de [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) of andere hulpprogramma's. 

  Maak een blobcontainer voor artefactoverdracht in elk account. Maak bijvoorbeeld een container met de naam *transfer*. Twee of meer overdrachtspijplijnen kunnen hetzelfde opslagaccount delen, maar moeten verschillende opslagcontainerbereiken gebruiken.
* **Sleutelkluizen: sleutelkluizen** zijn nodig voor het opslaan van SAS-tokengeheimen die worden gebruikt voor toegang tot bron- en doelopslagaccounts. Maak de bron- en doelsleutelkluizen in hetzelfde Azure-abonnement of dezelfde abonnementen als uw bron- en doelregisters. Voor demonstratiedoeleinden gaan de sjablonen en opdrachten die in dit artikel worden gebruikt ook ervan uit dat de bron- en doelsleutelkluizen zich respectievelijk in dezelfde resourcegroepen bevinden als de bron- en doelregisters. Dit gebruik van algemene resourcegroepen is niet vereist, maar het vereenvoudigt de sjablonen en opdrachten die in dit artikel worden gebruikt.

   Maak indien nodig sleutelkluizen met de [Azure CLI](../key-vault/secrets/quick-create-cli.md) of andere hulpprogramma's.

* **Omgevingsvariabelen:** stel bijvoorbeeld opdrachten in dit artikel de volgende omgevingsvariabelen in voor de bron- en doelomgevingen. Alle voorbeelden zijn opgemaakt voor de Bash-shell.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Overzicht van scenario's

U maakt de volgende drie pijplijnbronnen voor de overdracht van afbeeldingen tussen registers. Alle worden gemaakt met behulp van PUT-bewerkingen. Deze resources worden gebruikt voor *uw bron-* *en doelregisters* en opslagaccounts. 

Opslagverificatie maakt gebruik van SAS-tokens, beheerd als geheimen in sleutelkluizen. De pijplijnen gebruiken beheerde identiteiten om de geheimen in de kluizen te lezen.

* **[ExportPipeline:](#create-exportpipeline-with-resource-manager)** langdurige resource die informatie op hoog niveau bevat over het *bronregister* en het opslagaccount. Deze informatie omvat de blobcontainer-URI van de bronopslag en de sleutelkluis die het SAS-bron-token beheert. 
* **[ImportPipeline:](#create-importpipeline-with-resource-manager)** langdurige resource die informatie op hoog niveau bevat over het *doelregister* en opslagaccount. Deze informatie omvat de blobcontainer-URI van de doelopslag en de sleutelkluis die het SAS-token van het doel beheert. Een importtrigger is standaard ingeschakeld, zodat de pijplijn automatisch wordt uitgevoerd wanneer een artefact-blob in de doelopslagcontainer komt. 
* **[PipelineRun:](#create-pipelinerun-for-export-with-resource-manager)** resource die wordt gebruikt om een ExportPipeline- of ImportPipeline-resource aan te roepen.  
  * U kunt de ExportPipeline handmatig uitvoeren door een PipelineRun-resource te maken en de artefacten op te geven die u wilt exporteren.  
  * Als een importtrigger is ingeschakeld, wordt De ImportPipeline automatisch uitgevoerd. Het kan ook handmatig worden uitgevoerd met behulp van een PipelineRun. 
  * Momenteel kunnen maximaal **50 artefacten** worden overgedragen met elke PipelineRun.

### <a name="things-to-know"></a>Dingen die u moet weten
* ExportPipeline en ImportPipeline staan doorgaans in verschillende Active Directory-tenants die zijn gekoppeld aan de bron- en doelwolken. Voor dit scenario zijn afzonderlijke beheerde identiteiten en sleutelkluizen vereist voor het exporteren en importeren van resources. Voor testdoeleinden kunnen deze resources in dezelfde cloud worden geplaatst en identiteiten delen.
* Standaard maken de sjablonen ExportPipeline en ImportPipeline elk een door het systeem toegewezen beheerde identiteit mogelijk voor toegang tot key vault-geheimen. De ExportPipeline- en ImportPipeline-sjablonen ondersteunen ook een door de gebruiker toegewezen identiteit die u op geeft. 

## <a name="create-and-store-sas-keys"></a>SAS-sleutels maken en opslaan

Overdracht maakt gebruik van SAS-tokens (Shared Access Signature) voor toegang tot de opslagaccounts in de bron- en doelomgevingen. Genereer en sla tokens op zoals beschreven in de volgende secties.  

### <a name="generate-sas-token-for-export"></a>SAS-token genereren voor export

Voer de [opdracht az storage container generate-sas][az-storage-container-generate-sas] uit om een SAS-token te genereren voor de container in het bronopslagaccount, dat wordt gebruikt voor het exporteren van artefacten.

*Aanbevolen tokenmachtigingen:* Lezen, Schrijven, Lijst, Toevoegen. 

In het volgende voorbeeld wordt opdrachtuitvoer toegewezen aan de EXPORT_SAS omgevingsvariabele, vooraf vooraf laten gaan door het teken '?'. Werk de `--expiry` waarde voor uw omgeving bij:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>SAS-token opslaan voor export

Sla het SAS-token op in de Azure-sleutelkluis van uw bron met [az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>SAS-token genereren voor importeren

Voer de [opdracht az storage container generate-sas][az-storage-container-generate-sas] uit om een SAS-token te genereren voor de container in het doelopslagaccount, dat wordt gebruikt voor het importeren van artefacten.

*Aanbevolen tokenmachtigingen:* Lezen, Verwijderen, Lijst

In het volgende voorbeeld wordt opdrachtuitvoer toegewezen aan de IMPORT_SAS omgevingsvariabele, vooraf vooraf laten gaan door het teken '?'. Werk de `--expiry` waarde voor uw omgeving bij:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>SAS-token opslaan voor importeren

Sla het SAS-token op in uw Azure-doelsleutelkluis [met az keyvault secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>ExportPipeline maken met Resource Manager

Maak een ExportPipeline-resource voor uw broncontainerregister met behulp Azure Resource Manager sjabloonimplementatie.

Kopieer ExportPipeline Resource Manager [sjabloonbestanden naar](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) een lokale map.

Voer de volgende parameterwaarden in het bestand `azuredeploy.parameters.json` in:

|Parameter  |Waarde  |
|---------|---------|
|registryName     | Naam van het broncontainerregister      |
|exportPipelineName     |  Naam die u kiest voor de exportpijplijn       |
|targetUri     |  URI van de opslagcontainer in uw bronomgeving (het doel van de exportpijplijn).<br/>Voorbeeld: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Naam van de bronsleutelkluis  |
|sasTokenSecretName  | Naam van het SAS-tokengeheim in de bronsleutelkluis <br/>Voorbeeld: acrexportsas

### <a name="export-options"></a>Exportopties

De `options` eigenschap voor de exportpijplijnen ondersteunt optionele Booleaanse waarden. De volgende waarden worden aanbevolen:

|Parameter  |Waarde  |
|---------|---------|
|opties | OverwriteBlobs: bestaande doel-blobs overschrijven<br/>ContinueOnErrors: ga door met het exporteren van resterende artefacten in het bronregister als één artefactexport mislukt.

### <a name="create-the-resource"></a>De resource maken

Voer [az deployment group create uit om][az-deployment-group-create] een resource met de naam *exportPipeline te maken,* zoals wordt weergegeven in de volgende voorbeelden. Met de eerste optie schakelt de voorbeeldsjabloon standaard een door het systeem toegewezen identiteit in de ExportPipeline-resource in. 

Met de tweede optie kunt u de resource voorzien van een door de gebruiker toegewezen identiteit. (Het maken van de door de gebruiker toegewezen identiteit wordt niet weergegeven.)

Met beide opties configureert de sjabloon de identiteit voor toegang tot het SAS-token in de exportsleutelkluis. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Optie 1: Resource maken en door het systeem toegewezen identiteit inschakelen

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Optie 2: Een resource maken en een door de gebruiker toegewezen identiteit verstrekken

Geef in deze opdracht de resource-id van de door de gebruiker toegewezen identiteit op als een extra parameter.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Noteer in de uitvoer van de opdracht de resource-id ( `id` ) van de pijplijn. U kunt deze waarde opslaan in een omgevingsvariabele voor later gebruik door [de az deployment group show uit te uitvoeren.][az-deployment-group-show] Bijvoorbeeld:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>ImportPipeline maken met Resource Manager 

Maak een ImportPipeline-resource in het doelcontainerregister met behulp Azure Resource Manager sjabloonimplementatie. De pijplijn is standaard ingeschakeld om automatisch te importeren wanneer het opslagaccount in de doelomgeving een artefact-blob heeft.

Kopieer ImportPipeline Resource Manager [sjabloonbestanden naar](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) een lokale map.

Voer de volgende parameterwaarden in het bestand `azuredeploy.parameters.json` in:

Parameter  |Waarde  |
|---------|---------|
|registryName     | Naam van het doelcontainerregister      |
|importPipelineName     |  Naam die u kiest voor de importpijplijn       |
|sourceUri     |  URI van de opslagcontainer in uw doelomgeving (de bron voor de importpijplijn).<br/>Voorbeeld: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Naam van de doelsleutelkluis |
|sasTokenSecretName     |  Naam van het SAS-tokengeheim in de doelsleutelkluis<br/>Voorbeeld: acr importsas |

### <a name="import-options"></a>Opties voor importeren

De `options` eigenschap voor de importpijplijn ondersteunt optionele Booleaanse waarden. De volgende waarden worden aanbevolen:

|Parameter  |Waarde  |
|---------|---------|
|opties | OverwriteTags: bestaande doeltags overschrijven<br/>DeleteSourceBlobOnSuccess: de bronopslagblob verwijderen na een geslaagde import naar het doelregister<br/>ContinueOnErrors: ga door met het importeren van resterende artefacten in het doelregister als het importeren van één artefact mislukt.

### <a name="create-the-resource"></a>De resource maken

Voer [az deployment group create uit om][az-deployment-group-create] een resource met de naam *importPipeline te maken,* zoals wordt weergegeven in de volgende voorbeelden. Met de eerste optie schakelt de voorbeeldsjabloon standaard een door het systeem toegewezen identiteit in de ImportPipeline-resource in. 

Met de tweede optie kunt u de resource voorzien van een door de gebruiker toegewezen identiteit. (Het maken van de door de gebruiker toegewezen identiteit wordt niet weergegeven.)

Met beide opties configureert de sjabloon de identiteit voor toegang tot het SAS-token in de importsleutelkluis. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Optie 1: Een resource maken en een door het systeem toegewezen identiteit inschakelen

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Optie 2: Een resource maken en een door de gebruiker toegewezen identiteit verstrekken

In deze opdracht geeft u de resource-id van de door de gebruiker toegewezen identiteit op als een extra parameter.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Als u van plan bent de import handmatig uit te voeren, noteert u de resource-id ( `id` ) van de pijplijn. U kunt deze waarde opslaan in een omgevingsvariabele voor later gebruik door de [opdracht az deployment group show uit te][az-deployment-group-show] voeren. Bijvoorbeeld:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Pijplijn makenUitvoeren voor exporteren met Resource Manager 

Maak een PipelineRun-resource voor uw broncontainerregister met behulp Azure Resource Manager sjabloonimplementatie. Deze resource voert de ExportPipeline-resource uit die u eerder hebt gemaakt en exporteert opgegeven artefacten uit het containerregister als een blob naar uw bronopslagaccount.

Kopieer PipelineRun Resource Manager [sjabloonbestanden naar](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) een lokale map.

Voer de volgende parameterwaarden in het bestand `azuredeploy.parameters.json` in:

|Parameter  |Waarde  |
|---------|---------|
|registryName     | Naam van het broncontainerregister      |
|pipelineRunName     |  Naam die u kiest voor de run       |
|pipelineResourceId     |  Resource-id van de exportpijplijn.<br/>Voorbeeld: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Naam die u kiest voor de artefactblob die is geëxporteerd naar uw bronopslagaccount, zoals *myblob*
|Artefacten | Matrix van bronartefacten die moeten worden overdragen, als tags of manifest-digests<br/>Voorbeeld: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Als u een PipelineRun-resource met identieke eigenschappen opnieuw wilt installeren, moet u ook de eigenschap [forceUpdateTag](#redeploy-pipelinerun-resource) gebruiken.

Voer [az deployment group create uit om][az-deployment-group-create] de resource PipelineRun te maken. In het volgende voorbeeld wordt de implementatie *exportPipelineRun genoemd.*

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Sla voor later gebruik de resource-id van de pijplijn-run op in een omgevingsvariabele:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Het kan enkele minuten duren voordat artefacten zijn geëxporteerd. Wanneer de implementatie is voltooid, controleert u het exporteren van artefacten door de geëxporteerde blob in de *overdrachtscontainer* van het bronopslagaccount te bekijken. Voer bijvoorbeeld de opdracht [az storage blob list][az-storage-blob-list] uit:

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Blob overdragen (optioneel) 

Gebruik het AzCopy-hulpprogramma of andere methoden om [blobgegevens over](../storage/common/storage-use-azcopy-v10.md#transfer-data) te dragen van het bronopslagaccount naar het doelopslagaccount.

Met de volgende opdracht wordt bijvoorbeeld myblob gekopieerd van de [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) *overdrachtscontainer* in het bronaccount naar de *overdrachtscontainer* in het doelaccount. Als de blob in het doelaccount bestaat, wordt deze overschreven. Verificatie maakt gebruik van SAS-tokens met de juiste machtigingen voor de bron- en doelcontainers. (Stappen voor het maken van tokens worden niet weergegeven.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline-resource activeren

Als u de parameter van de ImportPipeline hebt ingeschakeld (de standaardwaarde), wordt de pijplijn geactiveerd nadat de blob is gekopieerd naar `sourceTriggerStatus` het doelopslagaccount. Het kan enkele minuten duren voordat artefacten zijn geïmporteerd. Wanneer het importeren is voltooid, controleert u het importeren van artefacten door de opslagplaatsen in het doelcontainerregister weer te brengen. Voer bijvoorbeeld [az acr repository list uit:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

Als u de parameter van de importpijplijn niet hebt ingeschakeld, moet u de `sourceTriggerStatus` ImportPipeline-resource handmatig uitvoeren, zoals wordt weergegeven in de volgende sectie. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>PipelineRun maken voor importeren met Resource Manager (optioneel) 
 
U kunt ook een PipelineRun-resource gebruiken om een ImportPipeline te activeren voor het importeren van artefacten in uw doelcontainerregister.

Kopieer PipelineRun Resource Manager [sjabloonbestanden naar](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) een lokale map.

Voer de volgende parameterwaarden in het bestand `azuredeploy.parameters.json` in:

|Parameter  |Waarde  |
|---------|---------|
|registryName     | Naam van het doelcontainerregister      |
|pipelineRunName     |  De naam die u voor de run kiest       |
|pipelineResourceId     |  Resource-id van de importpijplijn.<br/>Voorbeeld: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|Sourcename     |  Naam van de bestaande blob voor geëxporteerde artefacten in uw opslagaccount, zoals *myblob*

Als u een PipelineRun-resource met identieke eigenschappen opnieuw wilt installeren, moet u ook de eigenschap [forceUpdateTag](#redeploy-pipelinerun-resource) gebruiken.

Voer [az deployment group create uit om][az-deployment-group-create] de resource uit te voeren.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Sla voor later gebruik de resource-id van de pijplijn-run op in een omgevingsvariabele:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Wanneer de implementatie is voltooid, controleert u het importeren van artefacten door de opslagplaatsen in het doelcontainerregister te bekijken. Voer bijvoorbeeld [az acr repository list uit:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>PipelineRun-resource opnieuw gebruiken

Als u een PipelineRun-resource met identieke eigenschappen opnieuw wilt *installeren,* moet u gebruikmaken van de **eigenschap forceUpdateTag.** Deze eigenschap geeft aan dat de resource PipelineRun opnieuw moet worden gemaakt, zelfs als de configuratie niet is gewijzigd. Zorg ervoor dat forceUpdateTag steeds anders is wanneer u de PipelineRun-resource opnieuwplot. In het onderstaande voorbeeld wordt een PipelineRun opnieuw gemaakt voor export. De huidige datum/tijd wordt gebruikt om forceUpdateTag in te stellen, zodat deze eigenschap altijd uniek is.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Pijplijnbronnen verwijderen

In de volgende voorbeeldopdrachten wordt [az resource delete gebruikt om][az-resource-delete] de pijplijnresources te verwijderen die in dit artikel zijn gemaakt. De resource-ID's zijn eerder opgeslagen in omgevingsvariabelen.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Problemen oplossen

* **Sjabloonimlementatie of fouten oplossen**
  * Als een pijplijn niet kan worden uitgevoerd, bekijkt u `pipelineRunErrorMessage` de eigenschap van de run-resource.
  * Zie Troubleshoot ARM template deployments (Problemen [met ARM-sjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) oplossen) voor veelvoorkomende fouten bij de implementatie van sjablonen
* **Problemen met het exporteren of importeren van opslagblobs**
  * SAS-token is mogelijk verlopen of heeft onvoldoende machtigingen voor de opgegeven export- of importuitvoer
  * Bestaande opslagblob in het bronopslagaccount wordt mogelijk niet overschreven tijdens meerdere exportuitvoeren. Controleer of de optie OverwriteBlob is ingesteld in de exportuitvoer en dat het SAS-token voldoende machtigingen heeft.
  * De opslagblob in het doelopslagaccount wordt mogelijk niet verwijderd nadat de import is uitgevoerd. Controleer of de optie DeleteBlobOnSuccess is ingesteld in de importuitvoer en of het SAS-token voldoende machtigingen heeft.
  * Opslagblob niet gemaakt of verwijderd. Controleer of de container die is opgegeven bij de export- of importuitvoer bestaat, of dat er een opgegeven opslagblob bestaat voor handmatige importuitvoer. 
* **Problemen met AzCopy**
  * Zie [Problemen met AzCopy oplossen.](../storage/common/storage-use-azcopy-configure.md)  
* **Problemen met de overdracht van artefacten**
  * Niet alle artefacten, of geen, worden overgedragen. Bevestig de spelling van artefacten in de exportuitvoer en de naam van de blob in export- en importuitvoer. Bevestig dat u maximaal 50 artefacten overdraagt.
  * Het uitvoeren van de pijplijn is mogelijk niet voltooid. Een export- of importuitvoer kan enige tijd duren. 
  * Geef voor andere pijplijnproblemen de [correlatie-id](../azure-resource-manager/templates/deployment-history.md) van de implementatie van de uitvoer- of importuitvoer naar het Azure Container Registry team.
* **Problemen met het binnenhalen van de afbeelding in een fysiek geïsoleerde omgeving**
  * Als er fouten optreden met betrekking tot vreemde lagen of pogingen om mcr.microsoft.com op te lossen wanneer u probeert een afbeelding op te halen in een fysiek geïsoleerde omgeving, heeft uw afbeeldingsmanifest waarschijnlijk niet-distribueerbare lagen. Vanwege de aard van een fysiek geïsoleerde omgeving, kunnen deze afbeeldingen vaak niet worden pull. U kunt controleren of dit het geval is door het afbeeldingsmanifest te controleren op verwijzingen naar externe registers. Als dit het geval is, moet u de niet-distribueerbare lagen naar uw openbare cloud-ACR pushen voordat u een exportpijplijnuitvoer voor die afbeelding implementeert. Zie Niet-distribueerbare lagen naar een register pushen Hoe kan ik u hulp nodig [hebt bij het maken van een push-uiting.](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Volgende stappen

Zie de naslag voor de opdracht [az acr import][az-acr-import] als u enkele containerafbeeldingen vanuit een openbaar register of een ander privéregister wilt importeren in een Azure-containerregister.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete

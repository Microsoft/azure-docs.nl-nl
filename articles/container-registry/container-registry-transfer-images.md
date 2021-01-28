---
title: Artefacten overdragen
description: Verzamelingen van installatie kopieën of andere artefacten overdragen van een container register naar een ander REGI ster door een overdrachts pijplijn te maken met Azure Storage-accounts
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: ab6657ecd335a6de8c6c93e3c2ff392ac54c487c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935354"
---
# <a name="transfer-artifacts-to-another-registry"></a>Artefacten overdragen naar een ander REGI ster

In dit artikel wordt beschreven hoe u verzamelingen van installatie kopieën of andere register artefacten overdraagt van een Azure container Registry naar een ander REGI ster. De bron-en doel registers kunnen zich in dezelfde of verschillende abonnementen bevindt, Active Directory tenants, Azure-Clouds of fysiek losgekoppelde Clouds. 

Als u artefacten wilt overdragen, maakt u een *overdrachts pijplijn* waarmee artefacten tussen twee registers worden gerepliceerd met behulp van [Blob-opslag](../storage/blobs/storage-blobs-introduction.md):

* Artefacten van een bron register worden geëxporteerd naar een BLOB in een bron-opslag account 
* De BLOB wordt gekopieerd van het bron opslag account naar een doel opslag account
* De BLOB in het doel-opslag account wordt geïmporteerd als artefacten in het doel register. U kunt de import pijplijn zo instellen dat deze wordt geactiveerd wanneer de artefact-BLOB wordt bijgewerkt in de doel opslag.

Overdracht is ideaal voor het kopiëren van inhoud tussen twee Azure-container registers in fysiek losgekoppelde Clouds, doorgevoerd door opslag accounts in elke Cloud. Als u in plaats daarvan installatie kopieën wilt kopiëren uit container registers in verbonden Clouds, waaronder docker hub en andere Cloud leveranciers, wordt het [importeren van afbeeldingen](container-registry-import-images.md) aanbevolen.

In dit artikel gebruikt u Azure Resource Manager sjabloon implementaties om de overdrachts pijplijn te maken en uit te voeren. De Azure CLI wordt gebruikt om de gekoppelde resources in te richten, zoals opslag geheimen. Azure CLI-versie 2.2.0 of hoger wordt aanbevolen. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren][azure-cli].

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry-lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* **Container registers** : u hebt een bestaand bron register nodig met artefacten die moeten worden overgedragen en een doel register. ACR-overdracht is bedoeld voor beweging in fysiek losgekoppelde Clouds. Voor het testen kunnen de bron-en doel registers zich in hetzelfde of een ander Azure-abonnement, Active Directory-Tenant of Cloud bevindt. 

   Als u een REGI ster moet maken, raadpleegt u [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md). 
* **Opslag accounts** : Maak bron-en doel opslag accounts in een abonnement en locatie van uw keuze. Voor test doeleinden kunt u hetzelfde abonnement of dezelfde abonnementen gebruiken als voor de bron-en doel registers. Voor scenario's met meerdere clouds maakt u doorgaans een afzonderlijk opslag account in elke Cloud. 

  Maak, indien nodig, de opslag accounts met de [Azure cli](../storage/common/storage-account-create.md?tabs=azure-cli) of andere hulpprogram ma's. 

  Maak een BLOB-container voor artefact overdracht in elk account. Maak bijvoorbeeld een container met de naam *overdracht*. Twee of meer overdrachts pijplijnen kunnen hetzelfde opslag account delen, maar moeten verschillende opslag container bereiken gebruiken.
* **Sleutel kluizen** : sleutel kluizen zijn vereist voor het opslaan van SAS-token geheimen die worden gebruikt voor toegang tot de bron-en doel opslag accounts. Maak de bron-en doel sleutel kluizen in hetzelfde Azure-abonnement of op abonnementen als uw bron-en doel registers. Voor demonstratie doeleinden nemen de sjablonen en opdrachten die in dit artikel worden gebruikt ook aan dat de bron-en doel sleutel kluizen zich in dezelfde resource groepen bevinden als de bron-en doel registers. Dit gebruik van algemene resource groepen is niet vereist, maar het vereenvoudigt de sjablonen en opdrachten die in dit artikel worden gebruikt.

   Maak, indien nodig, sleutel kluizen met de [Azure cli](../key-vault/secrets/quick-create-cli.md) of andere hulpprogram ma's.

* **Omgevings variabelen** : voor de opdrachten in dit artikel stelt u de volgende omgevings variabelen in voor de bron-en doel omgeving. Alle voor beelden zijn opgemaakt voor de bash-shell.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Overzicht van scenario's

U maakt de volgende drie pijplijn resources voor het uitwisselen van afbeeldingen tussen registers. Alle zijn gemaakt met behulp van PUT-bewerkingen. Deze resources worden gebruikt voor de *bron* -en *doel* registers en opslag accounts. 

Opslag verificatie maakt gebruik van SAS-tokens die worden beheerd als geheimen in sleutel kluizen. De pijp lijnen gebruiken beheerde identiteiten om de geheimen in de kluizen te lezen.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** : lange duurzame bron die informatie op hoog niveau bevat over het *bron* register en het opslag account. Deze informatie omvat de bron opslag BLOB container-URI en de sleutel kluis die het SAS-bron token beheert. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** : lange duurzame bron die informatie op hoog niveau bevat over het *doel* register en het opslag account. Deze informatie omvat de doel-URI voor de BLOB-container en de sleutel kluis die het SAS-doel token beheert. Een import trigger is standaard ingeschakeld, zodat de pijp lijn automatisch wordt uitgevoerd wanneer een artefact-Blob in de doel opslag container terechtkomt. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** : de resource die wordt gebruikt om een ExportPipeline-of ImportPipeline-resource aan te roepen.  
  * U voert de ExportPipeline hand matig uit door een PipelineRun-resource te maken en de artefacten op te geven die moeten worden geëxporteerd.  
  * Als een import trigger is ingeschakeld, wordt de ImportPipeline automatisch uitgevoerd. Het kan ook hand matig worden uitgevoerd met een PipelineRun. 
  * Er kunnen momenteel Maxi maal **50 artefacten** worden overgedragen met elke PipelineRun.

### <a name="things-to-know"></a>Dingen die u moet weten
* De ExportPipeline en ImportPipeline bevinden zich doorgaans in verschillende Active Directory tenants die zijn gekoppeld aan de bron-en doel-Clouds. Voor dit scenario zijn afzonderlijke beheerde identiteiten en sleutel kluizen vereist voor de export-en import resources. Voor test doeleinden kunnen deze resources in dezelfde Cloud worden geplaatst, waarbij identiteiten worden gedeeld.
* Standaard kunnen de ExportPipeline-en ImportPipeline-sjablonen elk een door het systeem toegewezen beheerde identiteit inschakelen om toegang te krijgen tot sleutel kluis geheimen. De ExportPipeline-en ImportPipeline-sjablonen bieden ook ondersteuning voor een door de gebruiker toegewezen identiteit die u opgeeft. 

## <a name="create-and-store-sas-keys"></a>SAS-sleutels maken en opslaan

Overdracht gebruikt SAS-tokens (Shared Access Signature) om toegang te krijgen tot de opslag accounts in de bron-en doel omgeving. Tokens genereren en opslaan, zoals beschreven in de volgende secties.  

### <a name="generate-sas-token-for-export"></a>SAS-token voor export genereren

Voer de opdracht [AZ storage container generate-SAS][az-storage-container-generate-sas] uit om een SAS-token te genereren voor de container in het bron-opslag account dat wordt gebruikt voor het exporteren van artefacten.

*Aanbevolen token machtigingen*: lezen, schrijven, lijst, toevoegen. 

In het volgende voor beeld wordt de uitvoer van de opdracht toegewezen aan de omgevings variabele EXPORT_SAS, voorafgegaan door het teken '? '. Werk de `--expiry` waarde voor uw omgeving bij:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>SAS-token voor exporteren opslaan

Sla het SAS-token op in uw bron-Azure-sleutel kluis met [AZ Key kluis Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>SAS-token genereren voor importeren

Voer de opdracht [AZ storage container generate-SAS][az-storage-container-generate-sas] uit om een SAS-token te genereren voor de container in het doel-opslag account dat wordt gebruikt voor het importeren van artefacten.

*Aanbevolen token machtigingen*: lezen, verwijderen, lijst

In het volgende voor beeld wordt de uitvoer van de opdracht toegewezen aan de omgevings variabele IMPORT_SAS, voorafgegaan door het teken '? '. Werk de `--expiry` waarde voor uw omgeving bij:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>SAS-token voor importeren opslaan

Sla het SAS-token op in uw doel-Azure-sleutel kluis met [AZ Key kluis Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>ExportPipeline maken met Resource Manager

Maak een ExportPipeline-resource voor uw bron container register met behulp van Azure Resource Manager sjabloon implementatie.

Kopieer ExportPipeline Resource Manager- [sjabloon bestanden](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) naar een lokale map.

Voer de volgende parameter waarden in het bestand in `azuredeploy.parameters.json` :

|Parameter  |Waarde  |
|---------|---------|
|registerpad     | Naam van het REGI ster van de bron container      |
|exportPipelineName     |  De naam die u voor de export pijplijn hebt gekozen       |
|targetUri     |  De URI van de opslag container in uw bron omgeving (het doel van de export pijplijn).<br/>Voorbeeld: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Naam van de bron sleutel kluis  |
|sasTokenSecretName  | Naam van het SAS-token geheim in de bron sleutel kluis <br/>Voor beeld: acrexportsas

### <a name="export-options"></a>Export opties

De `options` eigenschap voor de export pijplijnen ondersteunt optionele Booleaanse waarden. De volgende waarden worden aanbevolen:

|Parameter  |Waarde  |
|---------|---------|
|opties | OverwriteBlobs-bestaande doel-blobs overschrijven<br/>ContinueOnErrors: Ga door met het exporteren van de resterende artefacten in het bron register als het exporteren van een artefact mislukt.

### <a name="create-the-resource"></a>De resource maken

Voer [AZ Deployment Group Create][az-deployment-group-create] uit om een resource te maken met de naam *exportPipeline* , zoals wordt weer gegeven in de volgende voor beelden. Met de eerste optie maakt de voorbeeld sjabloon standaard een door het systeem toegewezen identiteit in de ExportPipeline-resource. 

Met de tweede optie kunt u de resource voorzien van een door de gebruiker toegewezen identiteit. (Het maken van de door de gebruiker toegewezen identiteit wordt niet weer gegeven.)

Met beide opties configureert de sjabloon de identiteit voor toegang tot het SAS-token in de sleutel kluis exporteren. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Optie 1: resource maken en door het systeem toegewezen identiteit inschakelen

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Optie 2: een resource maken en een door de gebruiker toegewezen identiteit geven

In deze opdracht geeft u de resource-ID van de door de gebruiker toegewezen identiteit op als een extra para meter.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

In de uitvoer van de opdracht noteert u de resource-ID ( `id` ) van de pijp lijn. U kunt deze waarde in een omgevings variabele opslaan voor later gebruik door de [AZ-implementatie groep weer geven][az-deployment-group-show]uit te voeren. Bijvoorbeeld:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>ImportPipeline maken met Resource Manager 

Maak een ImportPipeline-bron in uw doel container register met behulp van Azure Resource Manager sjabloon implementatie. De pijp lijn is standaard ingeschakeld om automatisch te importeren wanneer het opslag account in de doel omgeving een artefact-BLOB heeft.

Kopieer ImportPipeline Resource Manager- [sjabloon bestanden](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) naar een lokale map.

Voer de volgende parameter waarden in het bestand in `azuredeploy.parameters.json` :

Parameter  |Waarde  |
|---------|---------|
|registerpad     | Naam van het REGI ster van de doel container      |
|importPipelineName     |  De naam die u voor de import pijplijn hebt gekozen       |
|sourceUri     |  De URI van de opslag container in uw doel omgeving (de bron voor de import pijplijn).<br/>Voorbeeld: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  De naam van de doel sleutel kluis |
|sasTokenSecretName     |  Naam van het SAS-token geheim in de doel sleutel kluis<br/>Voor beeld: ACR importsas |

### <a name="import-options"></a>Opties voor importeren

De `options` eigenschap voor de import pijplijn ondersteunt optionele Booleaanse waarden. De volgende waarden worden aanbevolen:

|Parameter  |Waarde  |
|---------|---------|
|opties | OverwriteTags-bestaande doel Tags overschrijven<br/>DeleteSourceBlobOnSuccess-de bron opslag-BLOB verwijderen na een succes volle import naar het doel register<br/>ContinueOnErrors: Ga door met het importeren van de resterende artefacten in het doel register als het importeren van een artefact mislukt.

### <a name="create-the-resource"></a>De resource maken

Voer [AZ Deployment Group Create][az-deployment-group-create] uit om een resource te maken met de naam *importPipeline* , zoals wordt weer gegeven in de volgende voor beelden. Met de eerste optie maakt de voorbeeld sjabloon standaard een door het systeem toegewezen identiteit in de ImportPipeline-resource. 

Met de tweede optie kunt u de resource voorzien van een door de gebruiker toegewezen identiteit. (Het maken van de door de gebruiker toegewezen identiteit wordt niet weer gegeven.)

Met een van beide opties configureert de sjabloon de identiteit voor toegang tot het SAS-token in de sleutel kluis importeren. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Optie 1: resource maken en door het systeem toegewezen identiteit inschakelen

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Optie 2: een resource maken en een door de gebruiker toegewezen identiteit geven

In deze opdracht geeft u de resource-ID van de door de gebruiker toegewezen identiteit op als een extra para meter.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Als u van plan bent om de import hand matig uit te voeren, noteert u de resource-ID ( `id` ) van de pijp lijn. U kunt deze waarde in een omgevings variabele opslaan voor later gebruik door de opdracht [AZ Deployment Group show][az-deployment-group-show] uit te voeren. Bijvoorbeeld:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>PipelineRun maken voor export met Resource Manager 

Maak een PipelineRun-resource voor uw bron container register met behulp van Azure Resource Manager sjabloon implementatie. Deze resource voert de ExportPipeline-resource uit die u eerder hebt gemaakt en exporteert de opgegeven artefacten uit het container register als een BLOB naar uw bron-opslag account.

Kopieer PipelineRun Resource Manager- [sjabloon bestanden](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) naar een lokale map.

Voer de volgende parameter waarden in het bestand in `azuredeploy.parameters.json` :

|Parameter  |Waarde  |
|---------|---------|
|registerpad     | Naam van het REGI ster van de bron container      |
|pipelineRunName     |  De naam die u voor de uitvoering hebt gekozen       |
|pipelineResourceId     |  Resource-ID van de export pijplijn.<br/>Voorbeeld: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  De naam die u kiest voor de artefacten-blobs die zijn geëxporteerd naar uw bron-opslag account, zoals *myblob*
|artefacten | Matrix van bron artefacten die moeten worden overgedragen, zoals Tags of manifest digesties<br/>Voorbeeld: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Als u een PipelineRun-resource met identieke eigenschappen opnieuw implementeert, moet u ook de eigenschap [updatetag](#redeploy-pipelinerun-resource) gebruiken.

Voer [AZ Deployment Group Create][az-deployment-group-create] uit om de PipelineRun-resource te maken. In het volgende voor beeld worden de implementatie- *exportPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Voor later gebruik slaat u de resource-ID van de pijplijn uitvoering op in een omgevings variabele:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Het kan enkele minuten duren voordat artefacten worden geëxporteerd. Wanneer de implementatie is voltooid, controleert u de export van artefacten door de geëxporteerde Blob in de container *overdracht* van het bron-opslag account te vermelden. Voer bijvoorbeeld de opdracht [AZ Storage BLOB List][az-storage-blob-list] uit:

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Overdracht-BLOB (optioneel) 

Gebruik het hulp programma AzCopy of andere methoden voor het [overdragen van BLOB-gegevens](../storage/common/storage-use-azcopy-v10.md#transfer-data) van het bron opslag account naar het doel opslag account.

Met de volgende opdracht wordt bijvoorbeeld [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) myblob gekopieerd van de container *overdracht* in het bron account naar de container *overdracht* in het doel account. Als de BLOB in het doel account bestaat, wordt deze overschreven. Verificatie maakt gebruik van SAS-tokens met de juiste machtigingen voor de bron-en doel containers. (De stappen voor het maken van tokens worden niet weer gegeven.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline-resource activeren

Als u de `sourceTriggerStatus` para meter van de ImportPipeline (de standaard waarde) hebt ingeschakeld, wordt de pijp lijn geactiveerd nadat de BLOB naar het doel opslag account is gekopieerd. Het kan enkele minuten duren voordat artefacten worden geïmporteerd. Wanneer het importeren is voltooid, controleert u het importeren van artefacten door de opslag plaatsen in het doel container register weer te geven. Voer bijvoorbeeld [AZ ACR Repository List][az-acr-repository-list]uit:

```azurecli
az acr repository list --name <target-registry-name>
```

Als u de `sourceTriggerStatus` para meter van de import-pijp lijn niet hebt ingeschakeld, voert u de ImportPipeline-resource hand matig uit, zoals wordt weer gegeven in de volgende sectie. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>PipelineRun maken voor import met Resource Manager (optioneel) 
 
U kunt ook een PipelineRun-Resource gebruiken om een ImportPipeline te activeren voor het importeren van artefacten in het doel container register.

Kopieer PipelineRun Resource Manager- [sjabloon bestanden](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) naar een lokale map.

Voer de volgende parameter waarden in het bestand in `azuredeploy.parameters.json` :

|Parameter  |Waarde  |
|---------|---------|
|registerpad     | Naam van het REGI ster van de doel container      |
|pipelineRunName     |  De naam die u voor de uitvoering hebt gekozen       |
|pipelineResourceId     |  Resource-ID van de import pijplijn.<br/>Voorbeeld: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  De naam van de bestaande BLOB voor geëxporteerde artefacten in uw opslag account, zoals *myblob*

Als u een PipelineRun-resource met identieke eigenschappen opnieuw implementeert, moet u ook de eigenschap [updatetag](#redeploy-pipelinerun-resource) gebruiken.

Voer [AZ Deployment Group Create][az-deployment-group-create] uit om de resource uit te voeren.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Voor later gebruik slaat u de resource-ID van de pijplijn uitvoering op in een omgevings variabele:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>PipelineRun-resource opnieuw implementeren

Als u een PipelineRun-resource met *identieke eigenschappen* opnieuw implementeert, moet u gebruikmaken van de eigenschap **updatetag** . Deze eigenschap geeft aan dat de PipelineRun-resource opnieuw moet worden gemaakt, zelfs als de configuratie niet is gewijzigd. Zorg ervoor dat Updatetag afwijkt van elke keer dat u de PipelineRun-resource implementeert. In het onderstaande voor beeld wordt een PipelineRun voor het exporteren opnieuw gemaakt. De huidige datum/tijd wordt gebruikt om Updatetag in te stellen, zodat deze eigenschap altijd uniek is.

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

## <a name="delete-pipeline-resources"></a>Pijplijn resources verwijderen

De volgende voorbeeld opdrachten gebruiken [AZ resource delete][az-resource-delete] om de pijplijn resources te verwijderen die in dit artikel zijn gemaakt. De resource-Id's zijn eerder opgeslagen in omgevings variabelen.

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

* **Sjabloonimlementatie fouten of-fouten**
  * Als een pijp lijn niet kan worden uitgevoerd, kijkt u naar de `pipelineRunErrorMessage` eigenschap van de uitvoerings bron.
  * Zie [problemen met arm-sjabloon implementaties oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) voor veelvoorkomende fouten bij het implementeren van sjablonen
* **Problemen met het exporteren of importeren van opslag-blobs**
  * SAS-token is mogelijk verlopen of heeft mogelijk onvoldoende machtigingen voor de opgegeven export-of import bewerking
  * De bestaande opslag-Blob in het bron opslag account kan mogelijk niet worden overschreven tijdens meerdere export uitvoeringen. Controleer of de optie OverwriteBlob is ingesteld in de export uitvoering en of het SAS-token voldoende machtigingen heeft.
  * De opslag-Blob in het doel opslag account is mogelijk niet verwijderd nadat de import bewerking is voltooid. Controleer of de optie DeleteBlobOnSuccess is ingesteld in de import uitvoering en of het SAS-token voldoende machtigingen heeft.
  * De opslag-blob is niet gemaakt of verwijderd. Controleer of de container die is opgegeven in export of import uitvoeren bestaat of dat de opgegeven opslag-BLOB bestaat voor het hand matig uitvoeren van import. 
* **Problemen met AzCopy**
  * Zie [problemen met AzCopy oplossen](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problemen bij het overdragen van artefacten**
  * Niet alle artefacten, of geen, worden overgebracht. Controleer de spelling van artefacten in de export uitvoering en de naam van de BLOB in export-en import uitvoeringen. Bevestig dat u een maximum van 50 artefacten wilt overdragen.
  * De pijplijn uitvoering is mogelijk niet voltooid. Het uitvoeren van een export-of import bewerking kan enige tijd duren. 
  * Voor andere pijplijn problemen geeft u de [correlatie-id](../azure-resource-manager/templates/deployment-history.md) van de implementatie van de export-of import uitvoering op in het Azure container Registry-team.


## <a name="next-steps"></a>Volgende stappen

Als u één container installatie kopie wilt importeren in een Azure container Registry vanuit een openbaar REGI ster of een ander persoonlijk REGI ster, raadpleegt u de opdracht [AZ ACR import][az-acr-import] Command.

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

---
title: 'Zelfstudie: Een scène weergeven in de cloud'
description: Leer hoe u een Autodesk 3ds Max-scène kunt renderen met Arnold, met behulp van de Batch-renderingservice en de Azure-opdrachtregelinterface
ms.topic: tutorial
ms.date: 12/30/2020
ms.custom: mvc, devx-track-azurecli
ROBOTS: NOINDEX
ms.openlocfilehash: 5165e5feb566a4b9081f40b681b92aafa143869f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491738"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Zelfstudie: Een scène renderen met Azure Batch

Azure Batch biedt mogelijkheden voor rendering in de cloud waarbij u betaalt op basis van gebruik. Azure Batch ondersteunt rendering-apps zoals Autodesk Maya, 3ds Max, Arnold en V-Ray. In deze zelfstudie wordt stapsgewijs uitgelegd hoe u een kleine scène rendert met Batch met behulp van de Azure-opdrachtregelinterface. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> - Een scène uploaden naar Azure Storage
> - Een Batch-pool voor rendering maken
> - Een scène met één frame renderen
> - De pool schalen en een scène met meerdere frames renderen
> - Gerenderde uitvoer downloaden

In deze zelfstudie rendert u een 3ds Max-scène met Batch met behulp van de ray-tracing renderer [Arnold](https://www.autodesk.com/products/arnold/overview). De Azure Batch-pool maakt gebruik van een Microsoft Azure Marketplace-installatiekopie met vooraf geïnstalleerde grafische en renderingtoepassingen die betalen per gebruik-licenties bieden.

## <a name="prerequisites"></a>Vereisten

- U hebt een abonnement op basis van betalen-per-gebruik of een andere betaalde Azure-optie nodig om renderingtoepassingen in Batch te kunnen gebruiken op basis van betalen per gebruik. **Licenties op basis van betalen per gebruik worden niet ondersteund als u gebruikmaakt van een gratis Azure-aanbieding die een financieel tegoed biedt.**

- De 3ds Max-voorbeeldscène voor deze zelfstudie staat op [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene), samen met een Bash-voorbeeldscript en JSON-configuratiebestanden. De 3ds Max-scène is afkomstig uit de [voorbeeldbestanden van Autodesk 3ds Max](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Autodesk 3ds Max-voorbeeldbestanden zijn beschikbaar onder de Creative Commons-licentie Naamsvermelding-NietCommercieel-GelijkDelen. Copyright &copy; Autodesk, Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!TIP]
> U kunt [Arnold-taaksjablonen](https://github.com/Azure/batch-extension-templates/tree/master/templates/arnold/render-windows-frames) weergeven in de GitHub-opslagplaats met Azure Batch-extensiesjablonen.

## <a name="create-a-batch-account"></a>Batch-account maken

Als u dat nog niet hebt gedaan, maakt u een resourcegroep, een Batch-account en een gekoppeld opslagaccount in uw abonnement.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus2*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus2
```

Maak een Azure-opslagaccount in de resourcegroep met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create). Voor deze zelfstudie gebruikt u het opslagaccount om een 3ds Max-bronscène en de gerenderde uitvoer op te slaan.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Maak een Batch-account met behulp van de opdracht [az batch account create](/cli/azure/batch/account#az-batch-account-create). In het volgende voorbeeld wordt een Batch-account met de naam *mybatchaccount* gemaakt in *myResourceGroup*, en wordt het gemaakte opslagaccount gekoppeld.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Als u rekenpools en -taken wilt maken en beheren, moet u zich verifiëren bij Batch. Meld u aan bij het account met behulp van de opdracht [az batch account login](/cli/azure/batch/account#az-batch-account-login). Nadat u zich hebt aangemeld, maken de `az batch`-opdrachten gebruik van deze accountcontext. In het volgende voorbeeld wordt verificatie met gedeelde sleutels gebruikt, op basis van de naam en sleutel van het Batch-account. Batch ondersteunt ook verificatie via [Azure Active Directory](batch-aad-auth.md) om afzonderlijke gebruikers of een toepassing zonder toezicht te verifiëren.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="upload-a-scene-to-storage"></a>Een scène uploaden naar de opslag

Als u de bronscène wilt uploaden naar de opslag, moet u eerst toegang krijgen tot het opslagaccount en een doelcontainer voor de blobs maken. Exporteer de omgevingsvariabelen `AZURE_STORAGE_KEY` en `AZURE_STORAGE_ACCOUNT` om daarmee toegang te krijgen tot het Azure-opslagaccount. De eerste Bash-shell-opdracht gebruikt de opdracht [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) om de eerste accountsleutel op te halen. Nadat u deze omgevingsvariabelen hebt ingesteld, gebruiken de opslagopdrachten deze accountcontext.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Maak nu een blobcontainer in het opslagaccount voor de scènebestanden. In het volgende voorbeeld wordt de opdracht [az storage container create](/cli/azure/storage/container#az-storage-container-create) gebruikt om een blobcontainer met de naam *scenefiles* te maken die openbare leestoegang toestaat.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Download de scène `MotionBlur-Dragon-Flying.max` van [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) naar een lokale werkmap. Bijvoorbeeld:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Upload het scènebestand vanuit uw lokale werkmap naar de blobcontainer. In het volgende voorbeeld wordt de opdracht [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) gebruikt, waarmee u meerdere bestanden kunt uploaden:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Een renderingpool maken

Maak een Batch-pool voor rendering met de opdracht [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). In dit voorbeeld geeft u de instellingen van de pool op in een JSON-bestand. Maak in uw huidige shell een bestand met de naam *mypool.json* en kopieer en plak de volgende inhoud. Zorg ervoor dat alle tekst correct wordt gekopieerd. (U kunt het bestand downloaden van [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```

Batch ondersteunt toegewezen knooppunten en [knooppunten met een lage prioriteit](batch-low-pri-vms.md) en u kunt een of beide gebruiken in uw pools. Toegewezen rekenknooppunten zijn gereserveerd voor uw pool. Knooppunten met een lage prioriteit worden aangeboden tegen een lagere prijs en worden gehaald uit het overschot van de VM-capaciteit in Azure. Knooppunten met een lage prioriteit zijn niet beschikbaar als Azure onvoldoende capaciteit heeft.

De opgegeven pool bevat één knooppunt met lage prioriteit waarop een installatiekopie van Windows Server wordt uitgevoerd met software voor de Batch-renderingservice. Deze pool heeft een licentie om te renderen met 3ds Max en Arnold. In een latere stap schaalt u de pool naar een groter aantal knooppunten.

Als u nog niet bent aangemeld bij uw Batch-account, gebruikt u de opdracht [az batch account login](/cli/azure/batch/account#az-batch-account-login) om dit te doen. Maak vervolgens de pool door het JSON-bestand door te geven aan de opdracht `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
```

Het inrichten van de pool duurt enkele minuten. Als u de status van de pool wilt zien, voert u de opdracht [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) uit. Met de volgende opdracht wordt de toewijzingsstatus van de pool opgehaald:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Ga door met de volgende stappen om een Batch-taak en taken te maken terwijl de status van de pool wordt gewijzigd. De pool is volledig ingericht als de toewijzingsstatus `steady` is en alle knooppunten actief zijn.  

## <a name="create-a-blob-container-for-output"></a>Een blobcontainer voor uitvoer maken

In de voorbeelden in deze zelfstudie maakt elke taak in de renderingtaak een uitvoerbestand. Voordat u de taak plant, maakt u een blobcontainer in uw opslagaccount als bestemming voor de uitvoerbestanden. In het volgende voorbeeld wordt de opdracht [az storage container create](/cli/azure/storage/container#az-storage-container-create) gebruikt om de container *job-myrenderjob* met openbare leestoegang te maken.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Batch moet een SAS-token (Shared Access Signature) gebruiken om uitvoerbestanden naar de container te kunnen schrijven. Maak het token met de opdracht [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). In dit voorbeeld wordt een token gemaakt om te schrijven naar elke blobcontainer in het account. Het token verloopt op 15 november 2021:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2021-11-15
```

Noteer het token dat wordt geretourneerd door de opdracht. Dit token ziet er ongeveer als volgt uit. U gebruikt dit token in een latere stap.

`se=2021-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

## <a name="render-a-single-frame-scene"></a>Een scène met één frame renderen

### <a name="create-a-job"></a>Een taak maken

Maak een renderingtaak die wordt uitgevoerd in de pool met de opdracht [az batch job create](/cli/azure/batch/job#az-batch-job-create). De Batch-taak heeft in eerste instantie geen taken.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Een taak maken

Gebruik de opdracht [az batch task create](/cli/azure/batch/task#az-batch-task-create) om een renderingtaak in de Batch-taak te maken. In dit voorbeeld geeft u de taakinstellingen op in een JSON-bestand. Maak in uw huidige shell een bestand met de naam *myrendertasks.json* en kopieer en plak de volgende inhoud. Zorg ervoor dat alle tekst correct wordt gekopieerd. (U kunt het bestand downloaden van [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

De taak geeft een 3ds Max-opdracht op om één frame van de scène *MotionBlur DragonFlying.max* te renderen.

Wijzig de elementen `blobSource` en `containerURL` in het JSON-bestand, zodat ze de naam van uw opslagaccount en uw SAS-token bevatten. 

> [!TIP]
> Uw `containerURL` eindigt op uw SAS-token en ziet er ongeveer zo uit: `https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Voeg de taak toe aan de Batch-taak met de volgende opdracht:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch plant de taak en de taak wordt uitgevoerd zodra een knooppunt in de pool beschikbaar is.

### <a name="view-task-output"></a>Taakuitvoer weergeven

Het uitvoeren van de taak duurt enkele minuten. Gebruik de opdracht [az batch task show](/cli/azure/batch/task#az-batch-task-show) om details over de taak weer te geven.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

De taak genereert *dragon0001.jpg* op het rekenknooppunt en uploadt dit naar de container *job-myrenderjob* in uw opslagaccount. Als u de uitvoer wilt bekijken, downloadt u het bestand uit de opslag naar uw lokale computer met de opdracht [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Open *dragon.jpg* op uw computer. De gerenderde afbeelding ziet er ongeveer als volgt uit:

![Gerenderde draak, frame 1](./media/tutorial-rendering-cli/dragon-frame.png) 

## <a name="scale-the-pool"></a>De pool schalen

U wijzigt u de pool om deze voor te bereiden op een grotere renderingtaak met meerdere frames. Batch biedt een aantal manieren om rekenresources te schalen, waaronder [automatisch schalen](batch-automatic-scaling.md) waarbij knoppunten worden toegevoegd of verwijderd wanneer de taak daarom vraagt. Voor dit eenvoudige voorbeeld gebruikt u de opdracht [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) om het aantal knooppunten met lage prioriteit in de pool te verhogen naar *6*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Deze wijziging duurt enkele minuten. Tijdens dit proces stelt u de volgende taken in die moeten worden uitgevoerd in de bestaande renderingtaak.

## <a name="render-a-multiframe-scene"></a>Een scène met meerdere frames renderen

Net als in het voorbeeld met één frame gebruikt u de opdracht [az batch task create](/cli/azure/batch/task#az-batch-task-create) om renderingtaken te maken in de taak genaamd *myrenderjob*. Hier geeft u de taakinstellingen op in een JSON-bestand genaamd *myrendertask_multi.json*. (U kunt het bestand downloaden van [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) Elk van de zes taken geeft een Arnold-opdrachtregel op om één frame van de 3ds Max-scène *MotionBlur DragonFlying.max* te renderen.

Maak een bestand in uw huidige shell met de naam *myrendertask_multi.json* en kopieer en plak de inhoud uit het gedownloade bestand. Wijzig de elementen `blobSource` en `containerURL` in het JSON-bestand, zodat ze de naam van uw opslagaccount en uw SAS-token bevatten. Let erop dat u de instellingen voor elk van de zes taken wijzigt. Sla het bestand op en voer de volgende opdracht uit om de taken in de wachtrij te plaatsen:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Taakuitvoer weergeven

Het uitvoeren van de taak duurt enkele minuten. Gebruik de opdracht [az batch task list](/cli/azure/batch/task#az-batch-task-list) om de status van de taken weer te geven. Bijvoorbeeld:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Gebruik de opdracht [az batch task show](/cli/azure/batch/task#az-batch-task-show) om details over afzonderlijke taken weer te geven. Bijvoorbeeld:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```

De taken genereren uitvoerbestanden met genaamd *dragon0002.jpg* - *dragon0007.jpg* op de rekenknooppunten en uploaden ze naar de container *job-myrenderjob* in uw opslagaccount. Als u de uitvoer wilt bekijken, downloadt u de bestanden naar een map op uw lokale computer met de opdracht [az storage blob download-batch](/cli/azure/storage/blob). Bijvoorbeeld:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Open een van de bestanden op uw computer. Het gerenderde frame 6 ziet er als volgt uit:

![Gerenderde draak, frame 6](./media/tutorial-rendering-cli/dragon-frame6.png) 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, het Batch-account, de pools en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt. U verwijdert de resources als volgt:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - Scènes uploaden naar Azure Storage
> - Een Batch-pool voor rendering maken
> - Een scène met één frame renderen met Arnold
> - De pool schalen en een scène met meerdere frames renderen
> - Gerenderde uitvoer downloaden

Raadpleeg de documentatie over de Batch-renderingservice voor meer informatie over rendering op cloudschaal.

> [!div class="nextstepaction"]
> [Service voor batch-rendering](batch-rendering-service.md)

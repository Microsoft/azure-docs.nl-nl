---
title: Gebruik Azure-pijp lijnen om & te bouwen HPC-oplossingen
description: Meer informatie over het implementeren van een build/release-pijp lijn voor een HPC-toepassing die wordt uitgevoerd op Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435542"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Gebruik Azure-pijp lijnen om HPC-oplossingen te bouwen en te implementeren

Hulpprogram ma's van Azure DevOps kunnen worden omgezet in geautomatiseerd bouwen en testen van High Performance Computing (HPC)-oplossingen. [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines) bieden een scala aan moderne doorlopende integratie (CI) en continue implementatie (cd) voor het bouwen, implementeren, testen en bewaken van software. Deze processen versnellen uw software levering, zodat u zich kunt richten op uw code in plaats van de infra structuur en bewerkingen te ondersteunen.

In dit artikel wordt uitgelegd hoe u CI/CD-processen kunt instellen met behulp van [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines) voor HPC-oplossingen die zijn geïmplementeerd op Azure batch.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, hebt u een [Azure DevOps-organisatie](/azure/devops/organizations/accounts/create-organization)nodig. U moet ook [een project maken in azure DevOps](/azure/devops/organizations/projects/create-project).

Het is handig om basis informatie te krijgen over [broncode beheer](/azure/devops/user-guide/source-control) en [Azure Resource Manager sjabloon syntaxis](../azure-resource-manager/templates/template-syntax.md) voordat u begint.

## <a name="create-an-azure-pipeline"></a>Een Azure-pijp lijn maken

In dit voor beeld maakt u een pijp lijn voor Build en release om een Azure Batch-infra structuur te implementeren en een toepassings pakket te publiceren. Ervan uitgaande dat de code lokaal is ontwikkeld, is dit de algemene implementatie stroom:

![Diagram waarin de stroom van de implementatie in de pijp lijn wordt weer gegeven](media/batch-ci-cd/DeploymentFlow.png)

In dit voor beeld worden verschillende Azure Resource Manager sjablonen en bestaande binaire bestanden gebruikt. U kunt deze voor beelden naar uw opslag plaats kopiëren en deze naar Azure DevOps pushen.

### <a name="understand-the-azure-resource-manager-templates"></a>Inzicht in de Azure Resource Manager sjablonen

In dit voor beeld wordt gebruikgemaakt van verschillende Azure Resource Manager sjablonen om de oplossing te implementeren. Er worden drie mogelijkheden sjablonen (vergelijkbaar met eenheden of modules) gebruikt voor het implementeren van een specifiek onderdeel van functionaliteit. Er wordt vervolgens een end-to-end oplossings sjabloon (deployment.jsop) gebruikt om die onderliggende sjablonen te implementeren. Met deze [gekoppelde sjabloon structuur ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) kan elke sjabloon afzonderlijk worden getest en opnieuw worden gebruikt in oplossingen.

![Diagram van een gekoppelde sjabloon structuur met behulp van Azure Resource Manager sjablonen.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Met deze sjabloon wordt een Azure Storage-account gedefinieerd dat vereist is om de toepassing te implementeren in het batch-account. Zie de [Naslag Gids voor Resource Manager-sjablonen voor resource typen van micro soft. Storage](/azure/templates/microsoft.storage/allversions)voor meer informatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

De volgende sjabloon definieert een [Azure batch-account](accounts.md). Het batch-account fungeert als een platform voor het uitvoeren van talloze toepassingen in verschillende [Pools](nodes-and-pools.md#pools). Zie de [Naslag Gids voor Resource Manager-sjablonen voor Microsoft.Batresource typen](/azure/templates/microsoft.batch/allversions)voor meer informatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Met de volgende sjabloon maakt u een batch-pool in het batch-account. Zie de [Naslag Gids voor Resource Manager-sjablonen voor Microsoft.Batresource typen](/azure/templates/microsoft.batch/allversions)voor meer informatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

De laatste sjabloon fungeert als Orchestrator en implementeert de drie onderliggende sjablonen voor de mogelijkheid.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Inzicht in de HPC-oplossing

Zoals eerder is vermeld, gebruikt dit voor beeld verschillende Azure Resource Manager sjablonen en bestaande binaire bestanden. U kunt deze voor beelden naar uw opslag plaats kopiëren en deze naar Azure DevOps pushen.

Voor deze oplossing wordt ffmpeg gebruikt als het toepassings pakket. U kunt [het ffmpeg-pakket downloaden](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) als u dit nog niet hebt gedaan.

![Scherm afbeelding van de structuur van de opslag plaats.](media/batch-ci-cd/git-repository.jpg)

Er zijn vier hoofd secties voor deze opslag plaats:

- Een **arm-sjablonen-** map met de Azure Resource Manager sjablonen
- Een **HPC-toepassingsmap** met de Windows 64-bits versie van [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Een map met **pijp lijnen** met een yaml-bestand dat het build pipeline-proces definieert.
- Optioneel: een map van de **client-toepassing** , een kopie van de [Azure batch .net-bestands verwerking met ffmpeg-voor](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) beeld. Deze toepassing is niet nodig voor dit artikel.


> [!NOTE]
> Dit is slechts één voor beeld van een structuur voor een code base. Deze methode wordt gebruikt om aan te tonen dat de toepassing, de infra structuur en de pijplijn code worden opgeslagen in dezelfde opslag plaats.

Nu de bron code is ingesteld, kunt u beginnen met de eerste build.

## <a name="continuous-integration"></a>Continue integratie

[Azure-pijp lijnen](/azure/devops/pipelines/get-started/), binnen Azure DevOps Services, helpt u bij het implementeren van een pijp lijn voor bouwen, testen en implementatie voor uw toepassingen.

In deze fase van uw pijp lijn worden tests meestal uitgevoerd om code te valideren en de juiste onderdelen van de software te bouwen. Het aantal en de typen tests, en eventuele extra taken die u uitvoert, zijn afhankelijk van uw bredere build-en release strategie.

## <a name="prepare-the-hpc-application"></a>De HPC-toepassing voorbereiden

In deze sectie gaat u werken met de map **HPC-toepassing** . Deze map bevat de software (ffmpeg) die wordt uitgevoerd binnen het Azure Batch-account.

1. Navigeer naar het gedeelte builds van Azure pipelines in uw Azure DevOps-organisatie. Maak een **nieuwe pijp lijn**.

    ![Scherm opname van het nieuwe pijplijn scherm.](media/batch-ci-cd/new-build-pipeline.jpg)

1. U hebt twee opties voor het maken van een build-pijp lijn:

    a. [Gebruik de visuele ontwerper](/azure/devops/pipelines/get-started-designer). Als u dit wilt doen, selecteert u ' de visuele Designer gebruiken ' op de pagina **nieuwe pijp lijn** .

    b. [Gebruik yaml builds](/azure/devops/pipelines/get-started-yaml). U kunt een nieuwe YAML-pijp lijn maken door te klikken op de optie Azure opslag plaatsen of GitHub op de pagina **nieuwe pijp lijn** . U kunt het voor beeld hieronder ook opslaan in het bron beheer en verwijzen naar een bestaand YAML-bestand door Visual Designer te selecteren en vervolgens de YAML-sjabloon te gebruiken.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Wanneer de build zo nodig is geconfigureerd, selecteert u **& wachtrij opslaan**. Als u doorlopende integratie hebt ingeschakeld (in de sectie **Triggers** ), wordt de build automatisch geactiveerd wanneer een nieuwe door Voer van de opslag plaats wordt gemaakt en voldoen aan de voor waarden die zijn ingesteld in de build.

    ![Scherm opname van een bestaande build-pijp lijn.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Bekijk actuele updates over de voortgang van uw build in azure DevOps door te navigeren naar de sectie **Build** van Azure pipelines. Selecteer de juiste build uit uw build-definitie.

    ![Scherm opname van live uitvoer van build in azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Als u een-client toepassing gebruikt om uw HPC-oplossing uit te voeren, moet u een afzonderlijke build-definitie maken voor die toepassing. U kunt een aantal hand leidingen vinden in de documentatie over [Azure-pijp lijnen](/azure/devops/pipelines/get-started/index) .

## <a name="continuous-deployment"></a>Doorlopende implementatie

Azure-pijp lijnen worden ook gebruikt voor het implementeren van uw toepassing en de onderliggende infra structuur. [Release-pijp lijnen](/azure/devops/pipelines/release) maken continue implementatie mogelijk en automatiseert uw release proces.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Uw toepassing en de onderliggende infra structuur implementeren

Er zijn een aantal stappen voor het implementeren van de-infra structuur. Omdat deze oplossing gebruikmaakt van [gekoppelde sjablonen](../azure-resource-manager/templates/linked-templates.md), moeten deze sjablonen toegankelijk zijn vanaf een openbaar eind punt (http of https). Dit kan een opslag plaats op GitHub, een Azure Blob Storage-account of een andere opslag locatie zijn. De geüploade sjabloon artefacten kunnen veilig blijven, omdat ze kunnen worden ondergebracht in een persoonlijke modus, maar via een van de volgende SAS-tokens (Shared Access Signature) worden geopend.

In het volgende voor beeld ziet u hoe u een infra structuur implementeert met sjablonen van een Azure Storage-blob.

1. Maak een **nieuwe release definitie** en selecteer vervolgens een lege definitie. Wijzig de naam van de zojuist gemaakte omgeving in iets dat relevant is voor de pijp lijn.

    ![Scherm afbeelding van de eerste release pijplijn.](media/batch-ci-cd/Release-0.jpg)

1. Maak een afhankelijkheid van de build-pijp lijn om de uitvoer voor de HPC-toepassing op te halen.

    > [!NOTE]
    > Noteer de **bron alias**, omdat dit nodig is wanneer taken worden gemaakt in de release definitie.

    ![Scherm opname van een artefact koppeling naar de HPCApplicationPackage in de juiste build-pijp lijn.](media/batch-ci-cd/Release-1.jpg)

1. Maak een koppeling naar een ander artefact, deze keer een Azure-opslag plaats. Dit is vereist voor toegang tot de Resource Manager-sjablonen die zijn opgeslagen in uw opslag plaats. Als Resource Manager-sjablonen geen compilatie vereisen, hoeft u deze niet te pushen via een build-pijp lijn.

    > [!NOTE]
    > Noteer opnieuw de **bron alias**, omdat deze later nodig is.

    ![Scherm opname van een artefact koppeling naar de Azure-opslag plaatsen.](media/batch-ci-cd/Release-2.jpg)

1. Navigeer naar de sectie **Varia bles** . U wilt een aantal variabelen in uw pijp lijn maken, zodat u niet dezelfde gegevens opnieuw hoeft in te voeren in meerdere taken. In dit voor beeld worden de volgende variabelen gebruikt:

   - **applicationStorageAccountName**: naam van het opslag account dat de binaire bestanden van de HPC-toepassing bevat
   - **batchAccountApplicationName**: naam van de toepassing in het batch-account
   - **batchAccountName**: naam van het batch-account
   - **batchAccountPoolName**: naam van de pool van vm's die de verwerking uitvoeren
   - **batchApplicationId**: unieke id voor de batch toepassing
   - **batchApplicationVersion**: semantische versie van uw batch-toepassing (dat wil zeggen de binaire bestanden van ffmpeg)
   - **locatie**: locatie van de Azure-resources die moeten worden geïmplementeerd
   - **resourceGroupName**: de naam van de resource groep die moet worden gemaakt en waar uw resources worden geïmplementeerd
   - **storageAccountName**: naam van het opslag account dat de gekoppelde Resource Manager-sjablonen bevat

   ![Scherm opname van variabelen die zijn ingesteld voor de release van de Azure pipeline.](media/batch-ci-cd/Release-4.jpg)

1. Navigeer naar de taken voor de ontwikkel omgeving. In de onderstaande moment opname ziet u zes taken. Deze taken: de gezipte ffmpeg-bestanden downloaden, een opslag account implementeren om de geneste Resource Manager-sjablonen te hosten, deze resource manager-sjablonen kopiëren naar het opslag account, het batch-account en de vereiste afhankelijkheden implementeren, een toepassing maken in het Azure Batch-account en het toepassings pakket uploaden naar het Azure Batch-account.

    ![Scherm opname van de taken die worden gebruikt om de HPC-toepassing vrij te geven Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Voeg de taak **pijplijn artefact downloaden (preview)** toe en stel de volgende eigenschappen in:
    - **Weergave naam:** ApplicationPackage downloaden naar agent
    - **De naam van het artefact dat moet worden gedownload:** HPC-toepassing
    - **Pad om te downloaden naar**: $ (System. DefaultWorkingDirectory)

1. Maak een opslag account om uw Azure Resource Manager-sjablonen op te slaan. Een bestaand opslag account van de oplossing kan worden gebruikt, maar ter ondersteuning van dit zelf opgenomen voor beeld en de isolatie van inhoud, maakt u een speciaal opslag account.

    Voeg de implementatie taak voor de **Azure-resource groep** toe en stel de volgende eigenschappen in:
    - **Weergave naam:** Opslag account voor Resource Manager-sjablonen implementeren
    - **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    - **Actie**: resource groep maken of bijwerken
    - **Resource groep**: $ (resourceGroupName)
    - **Locatie**: $ (locatie)
    - **Sjabloon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.jsop
    - **Sjabloon parameters negeren**:-AccountName $ (storageAccountName)

1. Upload de artefacten van broncode beheer naar het opslag account met behulp van Azure-pijp lijnen. Als onderdeel van deze Azure-pijplijn taak kan de URI en SAS-token van de opslag account worden gegenereerd naar een variabele in azure-pijp lijnen, waardoor ze tijdens deze agent fase opnieuw kunnen worden gebruikt.

    Voeg de **Azure** -taak voor het kopiëren van bestanden toe en stel de volgende eigenschappen in:
    - **Bron:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    - **Type Azure-verbinding**: Azure Resource Manager
    - **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    - **Doel type**: Azure Blob
    - **RM-opslag account**: $ (storageAccountName)
    - **Container naam**: sjablonen
    - **URI van opslag container**: templateContainerUri
    - **SAS-token van opslag container**: templateContainerSasToken

1. De Orchestrator-sjabloon implementeren. Deze sjabloon bevat para meters voor de container-URI en SAS-token van de opslag account. De variabelen die in de Resource Manager-sjabloon zijn vereist, worden opgeslagen in de sectie variabelen van de release definitie of zijn ingesteld vanaf een andere Azure pipeline-taak (bijvoorbeeld een onderdeel van de Kopieer taak voor Azure-blobs).

    Voeg de implementatie taak voor de **Azure-resource groep** toe en stel de volgende eigenschappen in:
    - **Weergave naam:** Azure Batch implementeren
    - **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    - **Actie**: resource groep maken of bijwerken
    - **Resource groep**: $ (resourceGroupName)
    - **Locatie**: $ (locatie)
    - **Sjabloon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.jsop
    - **Sjabloon parameters overschrijven**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Een veelvoorkomende procedure is het gebruik van Azure Key Vault taken. Als voor de service-principal die is verbonden met uw Azure-abonnement, het juiste toegangs beleid is ingesteld, kunnen er geheimen van een Azure Key Vault worden gedownload en als variabelen worden gebruikt in uw pijp lijn. De naam van het geheim wordt ingesteld met de bijbehorende waarde. In de release definitie kan bijvoorbeeld naar een geheim van sshPassword worden verwezen met $ (sshPassword).

1. Met de volgende stappen wordt de Azure CLI aangeroepen. De eerste wordt gebruikt om een toepassing te maken in Azure Batch en gekoppelde pakketten te uploaden.

    Voeg de **Azure cli** -taak toe en stel de volgende eigenschappen in:
    - **Weergave naam:** Toepassing maken in Azure Batch-account
    - **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    - **Script locatie**: inline-script
    - **Inline-script**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. De tweede stap wordt gebruikt voor het uploaden van gekoppelde pakketten naar de toepassing (in dit geval de ffmpeg-bestanden).

    Voeg de **Azure cli** -taak toe en stel de volgende eigenschappen in:
    - **Weergave naam:** Pakket uploaden naar Azure Batch-account
    - **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    - **Script locatie**: inline-script
    - **Inline-script**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Het versie nummer van het toepassings pakket is ingesteld op een variabele. Hierdoor kunnen vorige versies van het pakket worden overschreven en kunt u het versie nummer van het pakket dat naar Azure Batch is gepusht, hand matig beheren.

1. Maak een nieuwe release door **release > een nieuwe release maken** te selecteren. Zodra deze is geactiveerd, selecteert u de koppeling naar uw nieuwe release om de status weer te geven.

1. Bekijk de live uitvoer van de agent door de knop **logs** onder uw omgeving te selecteren.

    ![Scherm afbeelding met de status van de release.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>De omgeving testen

Nadat de omgeving is ingesteld, bevestigt u dat de volgende tests kunnen worden voltooid.

Maak verbinding met het nieuwe Azure Batch-account met behulp van de Azure CLI vanuit een Power shell-opdracht prompt.

- Meld u aan bij uw Azure-account `az login` en volg de instructies om te verifiëren.
- Verifieer nu het batch-account: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>De beschik bare toepassingen weer geven

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Controleer of de groep geldig is

```azurecli
az batch pool list
```

Noteer de waarde van van `currentDedicatedNodes` de uitvoer van deze opdracht. Deze waarde wordt bij de volgende test aangepast.

#### <a name="resize-the-pool"></a>Grootte van de pool wijzigen

Wijzig de grootte van de pool zodat er reken knooppunten beschikbaar zijn voor taak-en taak tests, Controleer met de opdracht pool lijst om de huidige status weer te geven totdat het formaat is voltooid en er beschik bare knoop punten zijn

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg deze zelf studies voor meer informatie over het gebruik van een batch-account via een eenvoudige toepassing.

- [Een parallelle workload uitvoeren met Azure Batch met behulp van de Python API](tutorial-parallel-python.md)
- [een parallelle workload uitvoeren met Azure Batch met behulp van de .NET API](tutorial-parallel-dotnet.md)

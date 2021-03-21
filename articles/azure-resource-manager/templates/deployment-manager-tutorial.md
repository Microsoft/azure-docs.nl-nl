---
title: Azure Deployment Manager gebruiken om sjablonen te implementeren
description: Meer informatie over het gebruik van Resource Manager-sjablonen met Azure Deployment Manager om Azure-resources te implementeren.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627580"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Zelfstudie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen (openbare preview)

Meer informatie over het gebruik van [Azure Deployment Manager](./deployment-manager-overview.md) om uw toepassingen in meerdere regio's te implementeren. Als u liever een snellere benadering hebt: met de [quickstart voor Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) maakt u de vereiste configuraties in uw abonnement en past u de artefacten aan om een toepassing in meerdere regio's te implementeren. In de quickstart worden dezelfde taken uitgevoerd als in deze zelfstudie.

Als u Deployment Manager wilt gebruiken, moet u twee sjablonen maken:

* **Een topologiesjabloon**: beschrijft de Azure-resources die uw toepassingen vormen en waar ze moeten worden geïmplementeerd.
* **Een implementatiesjabloon**: beschrijft de stappen die moeten worden genomen bij het implementeren van uw toepassingen.

> [!IMPORTANT]
> Als uw abonnement is gemarkeerd voor Canary om nieuwe functies van Azure te testen, kunt u Azure Deployment Manager alleen gebruiken om te implementeren in de Canary-regio's.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Inzicht in het scenario
> * De zelfstudiebestanden downloaden
> * De artefacten voorbereiden
> * De door de gebruiker gedefinieerde beheerde identiteit maken
> * De servicetopologiesjabloon maken
> * De implementatiesjabloon maken
> * De sjablonen implementeren
> * De implementatie controleren
> * De nieuwere versie implementeren
> * Resources opschonen

Aanvullende bronnen:

* [Naslag informatie voor Azure Configuratiebeheer rest API](/rest/api/deploymentmanager/).
* [Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Enige ervaring met het ontwikkelen van [Azure Resource Manager-sjablonen](overview.md).
* Azure PowerShell. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
* Deployment Manager-cmdlets. Als u deze prerelease-cmdlets wilt installeren, hebt u de nieuwste versie van PowerShellGet nodig. Zie [PowerShellGet installeren](/powershell/scripting/gallery/installing-psget) voor informatie over het ophalen van de nieuwste versie. Nadat u PowerShellGet hebt geïnstalleerd, sluit u het PowerShell-venster. Open een nieuw PowerShell-venster met verhoogde bevoegdheden en gebruik de volgende opdracht:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Inzicht in het scenario

De servicetopologiesjabloon beschrijft de Azure-resources die uw service vormen en waar ze moeten worden geïmplementeerd. De definitie van de servicetopologie bevat de volgende hiërarchie:

* Servicetopologie
  * Services
    * Service-eenheden

Het volgende diagram illustreert de servicetopologie die in deze zelfstudie wordt gebruikt:

![Diagram van het scenario in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Er zijn twee services die zijn toegewezen in de VS-West en de VS-Oost-locaties. Elke service heeft twee service-eenheden: een front-end-webtoepassing en een back-end-opslag account. De definities van service-eenheden bevatten koppelingen naar de sjabloon en de parameter bestanden die de webtoepassingen en de opslag accounts maken.

## <a name="download-the-tutorial-files"></a>De zelfstudiebestanden downloaden

1. Download [de sjablonen en de artefacten](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) die in deze zelfstudie worden gebruikt.
1. Pak de bestanden uit op uw computer.

Onder de hoofdmap bevinden zich twee mappen:

* _ADMTemplates_: bevat de Deployment Manager-sjablonen, namelijk:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: bevat zowel de sjabloonartefacten als de binaire artefacten. Zie [De artefacten voorbereiden](#prepare-the-artifacts).

Er zijn twee sets sjablonen. Eén set is de Configuratiebeheer sjablonen die worden gebruikt voor het implementeren van de service topologie en de implementatie. De andere set wordt aangeroepen vanuit de service-eenheden om webservices en opslag accounts te maken.

## <a name="prepare-the-artifacts"></a>De artefacten voorbereiden

De map ArtifactStore uit de download bevat twee mappen:

![Diagram van de artefactbron in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* De map _templates_: bevat de sjabloonartefacten. De mappen _1.0.0.0_ en _1.0.0.1_ vertegenwoordigen de twee versies van de binaire artefacten. Binnen elke versie bevindt zich een map voor elke service: _ServiceEUS_ (Service East VS) en _ServiceWUS_ (Service West VS). Elke service heeft een combinatie van een sjabloon en parameterbestanden voor het maken van een opslagaccount en een andere combinatie voor het maken van een webtoepassing. De webtoepassingsjabloon roept een gecomprimeerd pakket aan, dat de bestanden voor de webtoepassing bevat. Het gecomprimeerde bestand is een binair artefact dat is opgeslagen in de map met binaire bestanden.
* De map _binaries_: bevat de binaire artefacten. De mappen _1.0.0.0_ en _1.0.0.1_ vertegenwoordigen de twee versies van de binaire artefacten. Binnen elke versie is er één ZIP-bestand voor om de webtoepassing te maken op de locatie vs-West en het andere zip-bestand om de webtoepassing te maken op de locatie VS-Oost.

De twee versies (1.0.0.0 en 1.0.0.1) zijn voor de [implementatie van de revisie](#deploy-the-revision). Hoewel zowel de sjabloonartefacten als de binaire artefacten twee versies hebben, zijn alleen de binaire artefacten verschillend tussen de twee versies. In de praktijk worden binaire artefacten vaker bijgewerkt in vergelijking met sjabloonartefacten.

1. Open _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ in een teksteditor. Het is een basis sjabloon voor het maken van een opslag account.
1. Open _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Sjabloon voor webtoepassing maken in zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    De sjabloon roept een implementatiepakket aan dat de bestanden van de webtoepassing bevat. In deze zelf studie bevat het gecomprimeerde pakket alleen een _index.html_ -bestand.
1. Open _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![ContainerRoot sjabloonparameters webtoepassing maken in zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    De waarde van `deployPackageUri` is het pad naar het implementatie pakket. De para meter bevat een `$containerRoot` variabele. De waarde van `$containerRoot` is opgenomen in de [implementatie sjabloon](#create-the-rollout-template) door het samen voegen van de SAS-locatie van de artefact bron, de hoofdmap voor artefacten en `deployPackageUri` .
1. Open _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    In de HTML worden de locatie en de versie-informatie weer gegeven. Het binaire bestand in de map _1.0.0.1_ toont _versie 1.0.0.1_. Nadat u de service hebt geïmplementeerd, kunt u naar deze pagina's navigeren.
1. Bekijk andere artefactbestanden. Dit helpt u een beter inzicht te krijgen in het scenario.

Sjabloonartefacten worden gebruikt door de servicetopologiesjabloon, en binaire artefacten worden gebruikt door de implementatiesjabloon. Zowel de topologiesjabloon als de implementatiesjabloon definieert een Azure-resource met een artefactbron, wat een resource is die wordt gebruikt om Resource Manager te wijzen op de sjabloon en binaire artefacten die in de implementatie worden gebruikt. Ter vereenvoudiging van de zelfstudie wordt één opslagaccount gebruikt voor het opslaan van zowel de sjabloonartefacten als de binaire artefacten. Beide artefactbronnen verwijzen naar hetzelfde opslagaccount.

Voer het volgende PowerShell-script uit om een resourcegroep te maken, een opslagcontainer te maken, een blob-container te maken, de gedownloade bestanden te uploaden en vervolgens een SAS-token te maken.

> [!IMPORTANT]
> `projectName` in het Power shell-script wordt gebruikt voor het genereren van namen voor de Azure-Services die in deze zelf studie worden geïmplementeerd. Verschillende Azure-Services hebben verschillende vereisten voor namen. Kies een naam van minder dan 12 tekens met alleen kleine letters en cijfers om ervoor te zorgen dat de implementatie slaagt.
> Sla een kopie van de projectnaam op. U gebruikt hetzelfde `projectName` in de zelf studie.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Maak een kopie van de URL met het SAS-token. Deze URL is nodig voor het invullen van een veld in de twee parameter bestanden: topologie parameters bestand en implementatie parameter bestand.

Open de container vanuit het Azure Portal en controleer of zowel de _binaire bestanden_ als de _sjabloon_ mappen en de bestanden worden geüpload.

## <a name="create-the-user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit maken

Later in de zelfstudie voert u een implementatie uit. Een door de gebruiker toegewezen beheerde identiteit is nodig voor het uitvoeren van de implementatieacties (bijvoorbeeld het implementeren van de webtoepassingen en het opslagaccount). Deze identiteit moet toegang hebben tot het Azure-abonnement waarin u de service implementeert en voldoende machtigingen hebben om de implementatie van het artefact te kunnen voltooien.

U moet een door de gebruiker toegewezen beheerde identiteit maken en toegangsbeheer voor uw abonnement configureren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Maak een [door de gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. Selecteer in de portal **Abonnementen** in het linkermenu en selecteer vervolgens uw abonnement.
1. Selecteer **Toegangsbeheer (IAM)** en selecteer vervolgens **Roltoewijzing toevoegen**.
1. Typ of selecteer de volgende waarden:

    ![Toegangscontrole voor door de gebruiker toegewezen beheerde identiteit in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rol**: geef voldoende machtigingen om de implementatie van het artefact (de webtoepassingen en de opslagaccounts) te kunnen voltooien. Selecteer **Inzender** in deze zelfstudie. In de praktijk wilt u de machtigingen tot het minimum beperken.
    * **Toegang toewijzen aan**: Selecteer door de **gebruiker toegewezen beheerde identiteit**.
    * Selecteer door de gebruiker toegewezen beheerde identiteit die u eerder in de zelfstudie hebt gemaakt.
1. Selecteer **Opslaan**.

## <a name="create-the-service-topology-template"></a>De servicetopologiesjabloon maken

Open _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>De parameters

De sjabloon bevat de volgende parameters:

* `projectName`: Deze naam wordt gebruikt om de namen voor de Configuratiebeheer resources te maken. De **naam van de** service topologie is bijvoorbeeld een **demo**-ServiceTopology. De resource namen worden gedefinieerd in de sectie van de sjabloon `variables` .
* `azureResourcelocation`: Om de zelf studie te vereenvoudigen, delen alle resources deze locatie, tenzij deze anders is opgegeven.
* `artifactSourceSASLocation`: De SAS-URI naar de BLOB-container waar service-eenheids sjabloon en parameter bestanden worden opgeslagen voor implementatie. Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* `templateArtifactRoot`: Het offset-pad van de BLOB-container waar de sjablonen en para meters worden opgeslagen. De standaardwaarde is _templates/1.0.0.0_. Wijzig deze waarde niet tenzij u de mapstructuur wilt wijzigen zoals wordt uitgelegd in [De artefacten voorbereiden](#prepare-the-artifacts). In deze zelfstudie worden relatieve paden gebruikt. Het volledige pad wordt gemaakt door het samen voegen `artifactSourceSASLocation` , `templateArtifactRoot` , en `templateArtifactSourceRelativePath` (of `parametersArtifactSourceRelativePath` ).
* `targetSubscriptionID`: De abonnements-ID waarnaar de Configuratiebeheer resources worden geïmplementeerd en gefactureerd. Gebruik in deze zelfstudie uw abonnements-id.

### <a name="the-variables"></a>De variabelen

De sectie variabelen definieert de namen van de resources, de Azure-locaties voor de twee services: `ServiceWUS` en en `ServiceEUS` de artefact paden:

![Topologiesjabloonvariabelen in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Vergelijk de artefactpaden met de mapstructuur die u hebt geüpload naar het opslagaccount. U ziet dat de artefactpaden relatieve paden zijn. Het volledige pad wordt gemaakt door het samen voegen `artifactSourceSASLocation` , `templateArtifactRoot` , en `templateArtifactSourceRelativePath` (of `parametersArtifactSourceRelativePath` ).

### <a name="the-resources"></a>De resources

Op het hoofd niveau worden twee resources gedefinieerd: *een artefact bron* en *een service topologie*.

De definitie van de artefactbron is:

![Artefactbron van topologiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Op de volgende schermafbeelding ziet u alleen bepaalde onderdelen van de definities van de servicetopologie, services van de service-eenheden:

![Servicetopologie van topologiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Wordt gebruikt om de bron bron van het artefact te koppelen aan de resource van de service topologie.
* `dependsOn`: Alle bronnen van de service topologie zijn afhankelijk van de bron bron voor artefacten.
* `artifacts`: Wijs de sjabloon artefacten aan. Hier worden relatieve paden gebruikt. Het volledige pad wordt samengesteld door samen voeging `artifactSourceSASLocation` (gedefinieerd in de artefact bron), `artifactRoot` (gedefinieerd in de artefact bron) en `templateArtifactSourceRelativePath` (of `parametersArtifactSourceRelativePath` ).

### <a name="topology-parameters-file"></a>Topologieparameterbestand

U maakt een parameterbestand dat wordt gebruikt in combinatie met de topologiesjabloon.

1. Open _\ADMTemplates\CreateADMServiceTopology.Parameters.js_ in Visual Studio code of een tekst editor.
1. Voer de parameter waarden in:

    * `projectName`: Geef een teken reeks met 4-5 tekens op. Deze naam wordt gebruikt om unieke Azure-resource namen te maken.
    * `azureResourceLocation`: Als u niet bekend bent met Azure-locaties, gebruikt u **Centraal** in deze zelf studie.
    * `artifactSourceSASLocation`: Geef de SAS-URI op naar de hoofdmap (de BLOB-container) waar service-eenheids sjabloon en parameter bestanden worden opgeslagen voor implementatie.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
    * `templateArtifactRoot`: Tenzij u de mapstructuur van de artefacten wijzigt, kunt u in deze zelf studie _sjablonen/1.0.0.0_ gebruiken.

> [!IMPORTANT]
> De topologiesjabloon en de implementatiesjabloon delen enkele algemene parameters. Deze parameters moeten dezelfde waarden hebben. Deze para meters zijn: `projectName` , `azureResourceLocation` en `artifactSourceSASLocation` (beide artefact bronnen delen hetzelfde opslag account in deze zelf studie).

## <a name="create-the-rollout-template"></a>De implementatiesjabloon maken

Open _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>De parameters

De sjabloon bevat de volgende parameters:

![Implementatiesjabloonparameters in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Deze naam wordt gebruikt om de namen voor de Configuratiebeheer resources te maken. Als u bijvoorbeeld **demo** gebruikt, is de naam van de implementatie de **demo**-implementatie. De namen worden gedefinieerd in de sectie van de sjabloon `variables` .
* `azureResourcelocation`: Om de zelf studie te vereenvoudigen, delen alle Configuratiebeheer resources deze locatie, tenzij deze anders is opgegeven.
* `artifactSourceSASLocation`: De SAS-URI naar de hoofdmap (de BLOB-container) waar service-eenheids sjabloon en parameter bestanden worden opgeslagen voor implementatie. Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* `binaryArtifactRoot`: De standaard waarde is _binaire bestanden/1.0.0.0_. Wijzig deze waarde niet tenzij u de mapstructuur wilt wijzigen zoals wordt uitgelegd in [De artefacten voorbereiden](#prepare-the-artifacts). In deze zelfstudie worden relatieve paden gebruikt. Het volledige pad wordt gemaakt door het samen voegen `artifactSourceSASLocation` , `binaryArtifactRoot` en de `deployPackageUri` opgegeven in _CreateWebApplicationParameters.jsop_. Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* `managedIdentityID`: De door de gebruiker toegewezen beheerde identiteit waarmee de implementatie acties worden uitgevoerd. Zie [Door de gebruiker toegewezen beheerde identiteit maken](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>De variabelen

`variables`In het gedeelte worden de namen van de resources gedefinieerd. Zorg ervoor dat de naam van de servicetopologie, de servicenamen en de namen van de service-eenheden overeenkomen met de namen die zijn gedefinieerd in de [topologiesjabloon](#create-the-service-topology-template).

![Implementatiesjabloonvariabelen in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>De resources

Op het hoogste niveau zijn er drie resources gedefinieerd: een bronartefact, een stap en een implementatie.

De definitie van de artefactbron is identiek aan die in de topologiesjabloon is gedefinieerd. Zie [De servicetopologietopologiesjabloon maken](#create-the-service-topology-template) voor meer informatie.

De volgende scherm afbeelding toont de `wait` stap definitie:

![Wachtstap implementatiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

De duur maakt gebruik van de [ISO 8601-standaard](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (hoofdletters zijn verplicht) is een voorbeeld van 1 minuut wachten.

Op de volgende schermafbeelding worden alleen bepaalde onderdelen van de definitie van de implementatie weergegeven:

![Implementatie van implementatiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: De implementatie bron is afhankelijk van de bron bron voor artefacten en een van de gedefinieerde stappen.
* `artifactSourceId`: Wordt gebruikt om de bron bron van het artefacten te koppelen aan de resource voor de implementatie.
* `targetServiceTopologyId`: Wordt gebruikt om de resource van de service topologie te koppelen aan de resource voor de implementatie.
* `deploymentTargetId`: Dit is de resource-ID van de service-eenheid van de resource van de service topologie.
* `preDeploymentSteps` en `postDeploymentSteps` : bevat de implementaties tappen. In de sjabloon wordt een `wait` stap aangeroepen.
* `dependsOnStepGroups`: Configureer de afhankelijkheden tussen de stap groepen.

### <a name="rollout-parameters-file"></a>Implementatieparameterbestand

U maakt een parameterbestand dat wordt gebruikt in combinatie met de implementatiesjabloon.

1. Open _\ADMTemplates\CreateADMRollout.Parameters.js_ in Visual Studio code of een tekst editor.
1. Voer de parameter waarden in:

    * `projectName`: Geef een teken reeks met 4-5 tekens op. Deze naam wordt gebruikt om unieke Azure-resource namen te maken.
    * `azureResourceLocation`: Geef een Azure-locatie op.
    * `artifactSourceSASLocation`: Geef de SAS-URI op naar de hoofdmap (de BLOB-container) waar service-eenheids sjabloon en parameter bestanden worden opgeslagen voor implementatie.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Tenzij u de mapstructuur van de artefacten wijzigt, gebruikt u _binaire bestanden/1.0.0.0_ in deze zelf studie.
    * `managedIdentityID`: Voer de door de gebruiker toegewezen beheerde identiteit in. Zie [Door de gebruiker toegewezen beheerde identiteit maken](#create-the-user-assigned-managed-identity). De syntaxis is:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> De topologiesjabloon en de implementatiesjabloon delen enkele algemene parameters. Deze parameters moeten dezelfde waarden hebben. Deze para meters zijn: `projectName` , `azureResourceLocation` en `artifactSourceSASLocation` (beide artefact bronnen delen hetzelfde opslag account in deze zelf studie).

## <a name="deploy-the-templates"></a>De sjablonen implementeren

Azure PowerShell kan worden gebruikt om de sjablonen te implementeren.

1. Voer het script uit om de servicetopologie te implementeren.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Als u dit script uitvoert vanuit een andere Power shell-sessie dan dat u het script voor [artefacten](#prepare-the-artifacts) hebt voor bereid, moet u eerst de variabelen opnieuw vullen, met inbegrip van `$resourceGroupName` en `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` is een asynchrone aanroep. Het slagingsbericht betekent alleen dat de implementatie is gestart. Zie stap 2 en stap 4 van deze procedure om de implementatie te controleren.

1. Controleer of de servicetopologie en de onderstreepte resources zijn gemaakt met behulp van Azure Portal:

    ![Geïmplementeerde servicetopologieresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Verborgen typen weergeven** moet worden geselecteerd om de resources weer te geven.

1. <a id="deploy-the-rollout-template"></a>De implementatiesjabloon implementeren:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Controleer de voortgang van de implementatie met behulp van het volgende PowerShell-script:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    De Deployment Manager PowerShell-cmdlets moeten worden geïnstalleerd voordat u deze cmdlet kunt uitvoeren. Zie [Vereisten](#prerequisites). De `-Verbose` para meter kan worden gebruikt om de volledige uitvoer te bekijken.

    Het volgende voorbeeld toont de actieve status:

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Nadat de implementatie is geïmplementeerd, ziet u twee meer resource groepen die zijn gemaakt, één voor elke service.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Open de [Azure Portal](https://portal.azure.com).
1. Blader naar de zojuist gemaakte webtoepassingen onder de nieuwe resource groepen die zijn gemaakt met de implementatie.
1. Open de webtoepassing in een webbrowser. Controleer de locatie en de versie van het bestand _index.html_ .

## <a name="deploy-the-revision"></a>De revisie implementeren

Wanneer u een nieuwe versie (1.0.0.1) voor de webtoepassing hebt. Kunt u de volgende procedure gebruiken om de webtoepassing opnieuw te implementeren.

1. Open _CreateADMRollout.Parameters.jsop_.
1. Bijwerken `binaryArtifactRoot` naar _binaire bestanden/1.0.0.1_.
1. Voer de implementatie opnieuw uit volgens de instructies in [De sjablonen implementeren](#deploy-the-rollout-template).
1. Controleer de implementatie volgens de instructies in [De implementatie controleren](#verify-the-deployment). Op de webpagina wordt de versie 1.0.0.1 weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
1. Gebruik het veld **Filteren op naam** om u te beperken tot de resourcegroepen die u in deze zelfstudie hebt gemaakt.

    * **&lt;projectName>rg**: bevat de Deployment Manager-resources.
    * **&lt;projectName>ServiceWUSrg**: bevat de resources die zijn gedefinieerd door ServiceWUS.
    * **&lt;projectName>ServiceEUSrg**: bevat de resources die zijn gedefinieerd door ServiceEUS.
    * De resourcegroep voor de door de gebruiker gedefinieerde beheerde identiteit.
1. Selecteer de naam van de resourcegroep.
1. Selecteer **Resourcegroep verwijderen** in het bovenste menu.
1. Herhaal de laatste twee stappen als u andere resourcegroepen wilt verwijderen die zijn gemaakt in deze zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Deployment Manager gebruikt. Voor het integreren van statuscontrole in Azure Deployment Manager raadpleegt u [Zelfstudie: statuscontrole gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

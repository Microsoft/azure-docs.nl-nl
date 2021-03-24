---
title: Sjablonen opnieuw gebruiken voor verschillende Clouds
description: Ontwikkel Azure Resource Manager sjablonen (ARM-sjablonen) die consistent werken voor verschillende Cloud omgevingen. Maak nieuwe sjablonen voor de Azure Stack of werk deze bij.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 120e98ebebec1436c9171f6076baccd35be9b843
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888818"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>ARM-sjablonen ontwikkelen voor Cloud consistentie

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Een belang rijk voor deel van Azure is consistentie. Ontwikkelings investeringen voor één locatie kunnen in een ander worden herbruikbaar. Met een Azure Resource Manager sjabloon (ARM-sjabloon) kunnen uw implementaties consistent en herhaald worden in omgevingen, waaronder de wereld wijde Azure, Azure soevereine Clouds en Azure Stack. Als u sjablonen voor verschillende Clouds wilt gebruiken, moet u echter rekening houden met de Cloud afhankelijke afhankelijkheden, zoals in deze hand leiding wordt uitgelegd.

Micro soft biedt intelligente, zakelijke kant-en-klare Cloud Services op verschillende locaties, waaronder:

* Het wereld wijde Azure-platform wordt ondersteund door een groeiend netwerk van door micro soft beheerde data centers in regio's over de hele wereld.
* Geïsoleerde onafhankelijke Clouds, zoals Azure Duitsland, Azure Government en Azure China 21Vianet. Soevereine Clouds bieden een consistent platform met de meeste dezelfde fantastische functies die wereld wijde Azure-klanten toegang hebben.
* Azure Stack, een hybride Cloud platform waarmee u Azure-Services kunt leveren vanuit het Data Center van uw organisatie. Ondernemingen kunnen Azure Stack instellen in hun eigen data centers of Azure-Services gebruiken van service providers, waarop Azure Stack wordt uitgevoerd in hun faciliteiten (ook wel gehoste regio's genoemd).

Azure Resource Manager biedt de kern van al deze Clouds een API waarmee een groot aantal gebruikers interfaces kan communiceren met het Azure-platform. Deze API biedt u krachtige functionaliteit voor infrastructuur codes. Elk type resource dat beschikbaar is op het Azure-Cloud platform kan worden geïmplementeerd en geconfigureerd met Azure Resource Manager. Met één sjabloon kunt u uw volledige toepassing implementeren en configureren in een operationele eind status.

![Azure-omgevingen](./media/templates-cloud-consistency/environments.png)

Dankzij de consistentie van wereld wijd Azure, de soevereine Clouds, gehoste Clouds en een Cloud in uw Data Center kunt u profiteren van Azure Resource Manager. U kunt uw ontwikkelings investeringen in deze Clouds hergebruiken wanneer u de implementatie en configuratie van op basis van een sjabloon hebt ingesteld.

Hoewel de globale, soevereine, gehoste en hybride Clouds echter consistente services bieden, zijn niet alle Clouds identiek. Als gevolg hiervan kunt u een sjabloon maken met afhankelijkheden voor functies die alleen beschikbaar zijn in een specifieke Cloud.

In de rest van deze hand leiding worden de gebieden beschreven waarmee u rekening moet houden bij het plannen van het ontwikkelen van nieuwe en het bijwerken van bestaande ARM-sjablonen voor Azure Stack. In het algemeen moet uw controle lijst de volgende items bevatten:

* Controleer of de functies, eind punten, services en andere resources in uw sjabloon beschikbaar zijn op de doel implementatie locaties.
* Bewaar geneste sjablonen en configuratie artefacten op toegankelijke locaties en zorg ervoor dat u toegang hebt tot meerdere clouds.
* Gebruik dynamische verwijzingen in plaats van koppelingen en elementen met vaste code ring.
* Zorg ervoor dat de sjabloon parameters die u gebruikt, werken in de doel-Clouds.
* Controleer of de resource-specifieke eigenschappen beschikbaar zijn voor de doel-Clouds.

Zie [Sjabloonimlementatie](overview.md)voor een inleiding tot arm-sjablonen.

## <a name="ensure-template-functions-work"></a>Zorg ervoor dat sjabloon functies werken

De basis syntaxis van een ARM-sjabloon is JSON. Sjablonen gebruiken een superset van JSON, waardoor de syntaxis wordt uitgebreid met expressies en functies. De sjabloon taal processor wordt regel matig bijgewerkt ter ondersteuning van extra sjabloon functies. Zie [arm-sjabloon functies](template-functions.md)voor een gedetailleerde uitleg van de beschik bare sjabloon functies.

Nieuwe sjabloon functies die worden geïntroduceerd in Azure Resource Manager zijn niet onmiddellijk beschikbaar in soevereine Clouds of Azure Stack. Als u een sjabloon wilt implementeren, moeten alle functies waarnaar in de sjabloon wordt verwezen, beschikbaar zijn in de doel-Cloud.

Azure Resource Manager mogelijkheden zullen altijd eerst worden geïntroduceerd in globaal Azure. U kunt het volgende Power shell-script gebruiken om te controleren of nieuw geïntroduceerde-sjabloon functies ook beschikbaar zijn in Azure Stack:

1. Maak een kloon van de GitHub-opslag plaats: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Zodra u een lokale kloon van de opslag plaats hebt, maakt u verbinding met de Azure Resource Manager van het doel met Power shell.

1. Importeer de psm1-module en voer de Test-AzureRmTemplateFunctions-cmdlet uit:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Het script implementeert meerdere, geminimaliseerde sjablonen, elk met alleen unieke sjabloon functies. De uitvoer van het script rapporteert de ondersteunde en niet-beschik bare sjabloon functies.

## <a name="working-with-linked-artifacts"></a>Werken met gekoppelde artefacten

Een sjabloon kan verwijzingen bevatten naar gekoppelde artefacten en een implementatie bron bevatten die aan een andere sjabloon is gekoppeld. De gekoppelde sjablonen (ook wel geneste sjabloon genoemd) worden tijdens runtime opgehaald door Resource Manager. Een sjabloon kan ook verwijzingen bevatten naar artefacten voor VM-extensies (virtuele machine). Deze artefacten worden opgehaald door de VM-extensie die in de virtuele machine wordt uitgevoerd voor configuratie van de VM-extensie tijdens de sjabloon implementatie.

In de volgende secties worden overwegingen voor Cloud consistentie beschreven bij het ontwikkelen van sjablonen met artefacten die extern zijn voor de hoofd implementatie sjabloon.

### <a name="use-nested-templates-across-regions"></a>Geneste sjablonen gebruiken in verschillende regio's

Sjablonen kunnen worden onderverdeeld in kleine, herbruikbare sjablonen, die allemaal een specifiek doel hebben en kunnen opnieuw worden gebruikt in verschillende implementatie scenario's. Als u een implementatie wilt uitvoeren, geeft u één sjabloon op die ook wel het hoofd-of hoofd sjabloon wordt genoemd. Hiermee geeft u de resources op die moeten worden geïmplementeerd, zoals virtuele netwerken, Vm's en web-apps. De hoofd sjabloon kan ook een koppeling naar een andere sjabloon bevatten, wat betekent dat u sjablonen kunt nesten. Een geneste sjabloon kan ook koppelingen naar andere sjablonen bevatten. U kunt Maxi maal vijf niveaus dieper nesten.

De volgende code toont hoe de templateLink-para meter verwijst naar een geneste sjabloon:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2020-10-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager evalueert de hoofd sjabloon tijdens runtime en haalt elke geneste sjabloon op en evalueert deze. Nadat alle geneste sjablonen zijn opgehaald, wordt de sjabloon afgevlakt en wordt verdere verwerking gestart.

### <a name="make-linked-templates-accessible-across-clouds"></a>Gekoppelde sjablonen toegankelijk maken voor verschillende Clouds

Denk na over waar en hoe u de gekoppelde sjablonen opslaat die u gebruikt. Tijdens runtime, Azure Resource Manager opgehaald, en hiervoor is direct toegang nodig tot alle gekoppelde sjablonen. Een veelvoorkomende procedure is het gebruik van GitHub voor het opslaan van de geneste sjablonen. Een GitHub-opslag plaats kan bestanden bevatten die openbaar toegankelijk zijn via een URL. Hoewel deze techniek goed werkt voor de open bare Cloud en de soevereine Clouds, bevindt een Azure Stack omgeving zich mogelijk in een bedrijfs netwerk of op een niet-verbonden externe locatie, zonder uitgaande internet toegang. In dergelijke gevallen kan Azure Resource Manager de geneste sjablonen niet ophalen.

Een betere gewoonte voor implementaties in meerdere Clouds is het opslaan van uw gekoppelde sjablonen op een locatie die toegankelijk is voor de doel-Cloud. In het ideale geval worden alle implementatie artefacten onderhouden in en geïmplementeerd op basis van een continue integratie/doorlopende ontwikkeling (CI/CD)-pijp lijn. U kunt ook geneste sjablonen opslaan in een BLOB storage-container waarvan Azure Resource Manager ze kunt ophalen.

Aangezien de Blob-opslag op elke Cloud een andere eindpunt Fully Qualified Domain Name (FQDN) gebruikt, configureert u de sjabloon met de locatie van de gekoppelde sjablonen met twee para meters. Para meters kunnen gebruikers invoer accepteren tijdens de implementatie. Sjablonen worden meestal gemaakt en gedeeld door meerdere personen, zodat een best practice een standaard naam moet gebruiken voor deze para meters. Met behulp van naam conventies kunt u sjablonen herbruikbaarder maken voor verschillende regio's, Clouds en auteurs.

In de volgende code `_artifactsLocation` wordt gebruikt om te verwijzen naar één locatie, die alle implementatie-gerelateerde artefacten bevat. U ziet dat er een standaard waarde wordt gegeven. Als er bij de implementatie geen invoer waarde is opgegeven voor `_artifactsLocation` , wordt de standaard waarde gebruikt. De `_artifactsLocationSasToken` wordt gebruikt als invoer voor de `sasToken` . De standaard waarde moet een lege teken reeks zijn voor scenario's waarbij het `_artifactsLocation` niet is beveiligd, bijvoorbeeld een open bare github-opslag plaats.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

In de hele sjabloon worden koppelingen gegenereerd door de basis-URI (van de `_artifactsLocation` para meter) te combi neren met een pad dat relatief is voor artefacten en de `_artifactsLocationSasToken` . De volgende code laat zien hoe u de koppeling met de geneste sjabloon kunt opgeven met behulp van de URI-sjabloon functie:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Door deze methode te gebruiken, wordt de standaard waarde voor de `_artifactsLocation` para meter gebruikt. Als de gekoppelde sjablonen moeten worden opgehaald van een andere locatie, kan de parameter invoer worden gebruikt tijdens de implementatie om de standaard waarde te overschrijven. u hoeft de sjabloon zelf niet te wijzigen.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation gebruiken in plaats van hardcoding-koppelingen

Naast het gebruik van geneste sjablonen, wordt de URL in de `_artifactsLocation` para meter gebruikt als basis voor alle gerelateerde artefacten van een implementatie sjabloon. Sommige VM-extensies bevatten een koppeling naar een script dat buiten de sjabloon is opgeslagen. Voor deze uitbrei dingen mag u de koppelingen niet voorlopig hardcoderen we. De aangepaste scripts en Power shell DSC-uitbrei dingen kunnen bijvoorbeeld worden gekoppeld aan een extern script op GitHub, zoals wordt weer gegeven:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Als u de koppelingen naar het script hardcoding, kan het zijn dat de sjabloon niet kan worden geïmplementeerd op een andere locatie. Tijdens de configuratie van de VM-resource initieert de VM-agent die wordt uitgevoerd in de virtuele machine een down load van alle scripts die zijn gekoppeld aan de VM-extensie en slaat vervolgens de scripts op de lokale schijf van de virtuele machine op. Deze aanpak fungeert als de geneste sjabloon koppelingen die eerder zijn uitgelegd in de sectie geneste sjablonen gebruiken voor verschillende regio's.

Resource Manager haalt geneste sjablonen op tijdens runtime. Voor VM-extensies wordt het ophalen van externe artefacten uitgevoerd door de VM-agent. Naast de andere initiator van het ophalen van artefacten, is de oplossing in de sjabloon definitie hetzelfde. Gebruik de para meter _artifactsLocation met een standaard waarde van het basispad waar alle artefacten worden opgeslagen (met inbegrip van de VM-extensie scripts) en de `_artifactsLocationSasToken` para meter voor de invoer voor de sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Als u de absolute URI van een artefact wilt maken, is het gebruik van de functie URI-sjabloon in plaats van de functie concat de voorkeurs methode. Door hardcoded koppelingen te vervangen door de scripts in de VM-extensie met de URI-sjabloon functie, is deze functionaliteit in de sjabloon geconfigureerd voor consistentie in de Cloud.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Met deze aanpak kunnen alle implementatie artefacten, inclusief configuratie scripts, op dezelfde locatie worden opgeslagen als de sjabloon zelf. Als u de locatie van alle koppelingen wilt wijzigen, hoeft u alleen een andere basis-URL voor de _artifactsLocation-para meters_ op te geven.

## <a name="factor-in-differing-regional-capabilities"></a>Factor in verschillende regionale mogelijkheden

Met de flexibele ontwikkeling en doorlopende stroom van updates en nieuwe services die worden geïntroduceerd in azure, [kunnen regio's verschillen](https://azure.microsoft.com/regions/services/) in de beschik baarheid van services of updates. Na strenge interne tests worden nieuwe services of updates voor bestaande services doorgaans geïntroduceerd in een klein publiek van klanten die deel uitmaken van een validatie programma. Na een geslaagde klant validatie worden de services of updates beschikbaar gesteld binnen een subset van Azure-regio's en vervolgens geïntroduceerd in meer regio's, naar de soevereine Clouds, en mogelijk ook beschikbaar gesteld voor Azure Stack klanten.

Als u weet dat Azure-regio's en-Clouds in hun beschik bare Services kunnen verschillen, kunt u proactieve beslissingen nemen over uw sjablonen. Een goede plaats om te beginnen is door de beschik bare resource providers voor een cloud te onderzoeken. Een resource provider vertelt u de set resources en bewerkingen die beschikbaar zijn voor een Azure-service.

Met een sjabloon worden bronnen geïmplementeerd en geconfigureerd. Een resource type wordt door een resource provider verschaft. De compute resource provider (micro soft. Compute) biedt bijvoorbeeld meerdere resource typen, zoals informatie en Availability sets. Elke resource provider biedt een API voor het Azure Resource Manager dat is gedefinieerd door een gemeen schappelijk contract, waardoor een consistente, uniforme ervaring voor het ontwerpen van alle resource providers mogelijk wordt. Een resource provider die beschikbaar is in wereld wijd Azure is echter mogelijk niet beschikbaar in een soevereine Cloud of een Azure Stack regio.

![Resourceproviders](./media/templates-cloud-consistency/resource-providers.png)

Als u de resource providers wilt controleren die beschikbaar zijn in een bepaalde Cloud, voert u het volgende script uit in de Azure-opdracht regel interface ([cli](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

U kunt ook de volgende Power shell-cmdlet gebruiken om beschik bare resource providers te bekijken:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>De versie van alle resource typen controleren

Een set eigenschappen is gebruikelijk voor alle resource typen, maar elke resource heeft ook eigen specifieke eigenschappen. Nieuwe functies en gerelateerde eigenschappen worden op momenten door een nieuwe API-versie toegevoegd aan bestaande resource typen. Een resource in een sjabloon heeft een eigen API-versie-eigenschap `apiVersion` . Deze versie wordt gegarandeerd dat een bestaande bron configuratie in een sjabloon niet wordt beïnvloed door wijzigingen op het platform.

Nieuwe API-versies die zijn geïntroduceerd in bestaande resource typen in Global Azure zijn mogelijk niet onmiddellijk beschikbaar in alle regio's, soevereine Clouds of Azure Stack. Als u een lijst wilt weer geven van de beschik bare resource providers, resource typen en API-versies voor een Cloud, kunt u resource Explorer gebruiken in Azure Portal. Zoek naar resource Explorer in het menu alle services. Vouw het knoop punt providers in resource Explorer uit om alle beschik bare resource providers, hun resource typen en API-versies in die cloud te retour neren.

Als u de beschik bare API-versie voor alle resource typen in een bepaalde Cloud in azure CLI wilt weer geven, voert u het volgende script uit:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

U kunt ook de volgende Power shell-cmdlet gebruiken:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Verwijzen naar resource locaties met een para meter

Een sjabloon wordt altijd geïmplementeerd in een resource groep die zich in een regio bevindt. Naast de implementatie zelf heeft elke resource in een sjabloon ook een locatie-eigenschap die u kunt gebruiken om de regio op te geven waarin u wilt implementeren. Als u uw sjabloon voor Cloud consistentie wilt ontwikkelen, hebt u een dynamische manier nodig om naar resource locaties te verwijzen, omdat elke Azure Stack unieke locatie namen kan bevatten. Doorgaans worden resources geïmplementeerd in dezelfde regio als de resource groep, maar om scenario's te ondersteunen, zoals de beschik baarheid van meerdere regio's, kan het handig zijn om resources over verschillende regio's te verdelen.

Hoewel u de regio namen kunt voorlopig hardcoderen we wanneer u de bron eigenschappen in een sjabloon opgeeft, garandeert deze benadering niet dat de sjabloon kan worden geïmplementeerd in andere Azure Stack omgevingen, omdat de naam van de regio waarschijnlijk niet aanwezig is.

Als u wilt dat verschillende regio's worden opgegeven, voegt u een locatie voor invoer parameters toe aan de sjabloon met een standaard waarde. Als er tijdens de implementatie geen waarde is opgegeven, wordt de standaard waarde gebruikt.

De functie Temp late `[resourceGroup()]` retourneert een object dat de volgende sleutel/waarde-paren bevat:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Door te verwijzen naar de locatie sleutel van het object in de defaultValue van de invoer parameter, moet Azure Resource Manager tijdens runtime de `[resourceGroup().location]` sjabloon functie vervangen door de naam van de resource groep waarin de sjabloon is geïmplementeerd.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Met deze sjabloon functie kunt u uw sjabloon implementeren in een wille keurige Cloud zonder dat u de regio namen vooraf hoeft te weten. Daarnaast kan een locatie voor een specifieke resource in de sjabloon afwijken van de locatie van de resource groep. In dit geval kunt u deze configureren met behulp van aanvullende invoer parameters voor die specifieke resource, terwijl de andere resources in dezelfde sjabloon nog steeds de invoer parameter voor de eerste locatie gebruiken.

### <a name="track-versions-using-api-profiles"></a>Versies bijhouden met API-profielen

Het kan zeer lastig zijn om alle beschik bare resource providers en gerelateerde API-versies bij te houden die aanwezig zijn in Azure Stack. Op het moment van schrijven is de nieuwste API-versie voor **micro soft. Compute/Availability sets** in azure bijvoorbeeld `2018-04-01` , terwijl de beschik bare API-versie gemeen schappelijk is voor Azure en Azure stack is `2016-03-30` . De common API-versie voor **micro soft. Storage/Storage accounts** die wordt gedeeld tussen alle Azure-en Azure stack locaties `2016-01-01` , is, terwijl de nieuwste API-versie in Azure is `2018-02-01` .

Daarom heeft Resource Manager het concept van API-profielen in sjablonen geïntroduceerd. Zonder API-profielen wordt elke bron in een sjabloon geconfigureerd met een- `apiVersion` element dat de API-versie voor die specifieke resource beschrijft.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Een API-profiel versie fungeert als een alias voor een enkele API-versie per resource type die gemeen schappelijk is voor Azure en Azure Stack. In plaats van een API-versie op te geven voor elke resource in een sjabloon, geeft u alleen de API-profiel versie op in een nieuw hoofd element, met `apiProfile` de naam en laat u het `apiVersion` element voor de afzonderlijke resources weg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Met het API-profiel zorgt u ervoor dat de API-versies beschikbaar zijn op verschillende locaties. u hoeft dus niet hand matig de apiVersions te controleren die beschikbaar zijn op een specifieke locatie. Om ervoor te zorgen dat de API-versies waarnaar wordt verwezen door uw API-profiel in een Azure Stack omgeving aanwezig zijn, moeten de Azure Stack-Opera tors de oplossing up-to-date houden op basis van het beleid voor ondersteuning. Als een systeem meer dan zes maanden verouderd is, wordt het beschouwd als niet-naleving en moet de omgeving worden bijgewerkt.

Het API-profiel is geen vereist element in een sjabloon. Zelfs als u het element toevoegt, wordt het alleen gebruikt voor resources waarvoor geen `apiVersion` is opgegeven. Met dit element kunnen geleidelijke wijzigingen worden aangebracht, maar zijn er geen wijzigingen in bestaande sjablonen nodig.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Eindpunt verwijzingen controleren

Resources kunnen verwijzingen naar andere services op het platform bevatten. Aan een openbaar IP-adres kan bijvoorbeeld een open bare DNS-naam worden toegewezen. De open bare Cloud, de soevereine Clouds en Azure Stack oplossingen hebben hun eigen verschillende naam ruimten voor eind punten. In de meeste gevallen vereist een resource alleen een voor voegsel als invoer in de sjabloon. Tijdens runtime voegt Azure Resource Manager de eindpunt waarde toe. Sommige eindpunt waarden moeten expliciet worden opgegeven in de sjabloon.

> [!NOTE]
> Voor het ontwikkelen van sjablonen voor Cloud consistentie, voorlopig hardcoderen we u geen eindpunt naam ruimten.

De volgende twee voor beelden zijn algemene eindpunt naam ruimten die expliciet moeten worden opgegeven bij het maken van een resource:

* Opslag accounts (BLOB, wachtrij, tabel en bestand)
* Verbindings reeksen voor data bases en Azure cache voor redis

Naam ruimten van eind punten kunnen ook worden gebruikt in de uitvoer van een sjabloon als informatie voor de gebruiker wanneer de implementatie is voltooid. Hier volgen enkele algemene voor beelden:

* Opslag accounts (BLOB, wachtrij, tabel en bestand)
* Verbindings reeksen (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* Domeinnaam label van een openbaar IP-adres
* Cloud services

In het algemeen vermijdt u hardcoded eind punten in een sjabloon. De best practice is het gebruik van de functie voor de verwijzings sjabloon om de eind punten dynamisch op te halen. Zo is het eind punt meestal hardcoded de naam ruimte van het eind punt voor opslag accounts. Elk opslag account heeft een unieke FQDN die wordt samengesteld door de naam van het opslag account samen te voegen met de eindpunt naam ruimte. Een Blob Storage-account met de naam mystorageaccount1 resulteert in verschillende FQDN-namen, afhankelijk van de Cloud:

* `mystorageaccount1.blob.core.windows.net` Wanneer deze wordt gemaakt in de wereld wijde Azure-Cloud.
* `mystorageaccount1.blob.core.chinacloudapi.cn` Wanneer deze wordt gemaakt in de Azure China 21Vianet-Cloud.

Met de volgende functie van de referentie sjabloon wordt de naam ruimte van het eind punt opgehaald van de opslag Resource provider:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Door de hardcoded waarde van het eind punt van het opslag account te vervangen door de `reference` sjabloon functie, kunt u dezelfde sjabloon gebruiken om te implementeren in verschillende omgevingen zonder wijzigingen aan te brengen in de eindpunt referentie.

### <a name="refer-to-existing-resources-by-unique-id"></a>Verwijzen naar bestaande resources op unieke ID

U kunt ook verwijzen naar een bestaande resource van dezelfde of een andere resource groep, en binnen hetzelfde abonnement of een ander abonnement binnen dezelfde Tenant in dezelfde Cloud. Als u de resource-eigenschappen wilt ophalen, moet u de unieke id voor de resource zelf gebruiken. De `resourceId` sjabloon functie haalt de unieke id op van een resource, zoals SQL Server, zoals in de volgende code wordt weer gegeven:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

U kunt vervolgens de `resourceId` functie binnen de `reference` sjabloon functie gebruiken om de eigenschappen van een Data Base op te halen. Het object Return bevat de `fullyQualifiedDomainName` eigenschap met de volledige eindpunt waarde. Deze waarde wordt tijdens runtime opgehaald en biedt de Cloud-specifieke eindpunt naam ruimte. Als u de connection string wilt definiëren zonder hardcoding u de naam ruimte van het eind punt, kunt u de eigenschap van het retour object rechtstreeks in de connection string verwijzen, zoals wordt weer gegeven:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Resource-eigenschappen overwegen

Specifieke resources binnen Azure Stack omgevingen hebben unieke eigenschappen die u in uw sjabloon moet overwegen.

### <a name="ensure-vm-images-are-available"></a>Controleren of VM-installatie kopieën beschikbaar zijn

Azure biedt een uitgebreide selectie van VM-installatie kopieën. Deze installatie kopieën worden gemaakt en voor bereid voor implementatie door micro soft en partners. De afbeeldingen vormen de basis voor Vm's op het platform. Een Cloud consistente sjabloon moet echter alleen verwijzen naar beschik bare para meters, met name de uitgever, de aanbieding en de SKU van de VM-installatie kopieën die beschikbaar zijn voor de wereld wijde Azure, Azure soevereine Clouds of een Azure Stack oplossing.

Voer de volgende Azure CLI-opdracht uit om een lijst met beschik bare VM-installatie kopieën op een locatie op te halen:

```azurecli-interactive
az vm image list -all
```

U kunt dezelfde lijst ophalen met de Azure PowerShell cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) en de gewenste locatie opgeven met de `-Location` para meter. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Het duurt enkele minuten om alle beschik bare installatie kopieën te retour neren in de Europa-west regio van de wereld wijde Azure-Cloud.

Als u deze VM-installatie kopieën beschikbaar hebt gemaakt voor Azure Stack, wordt alle beschik bare opslag verbruikt. Azure Stack kunt de installatie kopieën die u wilt toevoegen aan een omgeving selecteren als u de kleinste schaal eenheid wilt aanpassen.

Het volgende code voorbeeld toont een consistente benadering om te verwijzen naar de uitgevers-, aanbiedings-en SKU-para meters in uw ARM-sjablonen:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Lokale VM-grootten controleren

Als u uw sjabloon voor Cloud consistentie wilt ontwikkelen, moet u ervoor zorgen dat de VM-grootte die u wilt instellen beschikbaar is in alle doel omgevingen. VM-grootten zijn een groepering van prestatie kenmerken en mogelijkheden. Sommige VM-grootten zijn afhankelijk van de hardware waarop de virtuele machine wordt uitgevoerd. Als u bijvoorbeeld een door GPU geoptimaliseerde virtuele machine wilt implementeren, moet de hardware-Gpu's worden geïnstalleerd op de computer waarop de Hyper Visor wordt uitgevoerd.

Wanneer micro soft een nieuwe grootte van een VM met bepaalde hardwarevereisten introduceert, wordt de grootte van de virtuele machine doorgaans eerst beschikbaar gemaakt in een kleine subset van regio's in de Azure-Cloud. Later wordt deze beschikbaar gesteld aan andere regio's en Clouds. Om ervoor te zorgen dat de VM-grootte bestaat in elke Cloud die u implementeert naar, kunt u de beschik bare grootten ophalen met de volgende Azure CLI-opdracht:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Voor Azure PowerShell gebruikt u:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)voor een volledige lijst met beschik bare Services.

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Het gebruik van Azure-Managed Disks in Azure Stack controleren

Beheerde schijven verwerken de opslag voor een Azure-Tenant. In plaats van een opslag account expliciet te maken en de URI voor een virtuele harde schijf (VHD) op te geven, kunt u beheerde schijven gebruiken om deze acties impliciet uit te voeren wanneer u een virtuele machine implementeert. Met Managed disks wordt de beschik baarheid verbeterd doordat alle schijven van virtuele machines in dezelfde beschikbaarheidsset in verschillende opslag eenheden worden geplaatst. Daarnaast kunnen bestaande Vhd's van de standaard-naar Premium-opslag worden geconverteerd met een aanzienlijk minder downtime.

Hoewel beheerde schijven zich op het schema voor Azure Stack bevinden, worden ze momenteel niet ondersteund. Tot die manier kunt u Cloud consistente sjablonen voor Azure Stack ontwikkelen door expliciet Vhd's op te geven met behulp van het- `vhd` element in de sjabloon voor de VM-resource, zoals wordt weer gegeven:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Als u daarentegen een configuratie van een beheerde schijf in een sjabloon wilt opgeven, verwijdert u het `vhd` element uit de configuratie van de schijf.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Met dezelfde wijzigingen worden ook [gegevens schijven](../../virtual-machines/using-managed-disks-template-deployments.md)toegepast.

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Controleer of er VM-extensies beschikbaar zijn in Azure Stack

Een andere overweging voor Cloud consistentie is het gebruik van [extensies van virtuele machines](../../virtual-machines/extensions/features-windows.md) voor het configureren van de resources in een VM. Niet alle VM-uitbrei dingen zijn beschikbaar in Azure Stack. Met een sjabloon kunt u de resources die zijn toegewezen aan de VM-extensie opgeven, afhankelijkheden en voor waarden maken in de sjabloon.

Als u bijvoorbeeld een virtuele machine met Microsoft SQL Server wilt configureren, kan de VM-extensie SQL Server als onderdeel van de sjabloon implementatie configureren. Bedenk wat er gebeurt als de implementatie sjabloon ook een toepassings server bevat die is geconfigureerd voor het maken van een Data Base op de virtuele machine met SQL Server. Naast het gebruik van een VM-extensie voor de toepassings servers, kunt u ook de afhankelijkheid van de toepassings server configureren op basis van de geslaagde retour van de SQL Server VM-extensie resource. Deze aanpak zorgt ervoor dat de virtuele machine met SQL Server is geconfigureerd en beschikbaar is wanneer de toepassings server de instructie krijgt om de data base te maken.

Met de declaratieve aanpak van de sjabloon kunt u de eind status van de resources en de bijbehorende onderlinge afhankelijkheden definiëren, terwijl het platform de vereiste logica voor de afhankelijkheden onderneemt.

#### <a name="check-that-vm-extensions-are-available"></a>Controleer of er VM-extensies beschikbaar zijn

Er bestaan veel typen VM-extensies. Bij het ontwikkelen van een sjabloon voor consistentie in de Cloud, moet u ervoor zorgen dat u alleen de extensies gebruikt die beschikbaar zijn in alle regio's van de sjabloon doelen.

Voer de volgende Azure CLI-opdracht uit om een lijst op te halen van de VM-extensies die beschikbaar zijn voor een specifieke regio (in dit voor beeld `myLocation` ):

```azurecli-interactive
az vm extension image list --location myLocation
```

U kunt ook de Azure PowerShell [Get-AzureRmVmImagePublisher-](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet uitvoeren en gebruiken `-Location` om de locatie van de installatie kopie van de virtuele machine op te geven. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Zorg ervoor dat de versies beschikbaar zijn

Omdat VM-extensies resources van Resource Manager voor de eerste partij zijn, hebben ze hun eigen API-versies. Zoals in de volgende code wordt weer gegeven, is het VM-extensie type een geneste resource in de resource provider micro soft. compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

De API-versie van de VM-extensie resource moet aanwezig zijn op alle locaties die u wilt richten op uw sjabloon. De locatie afhankelijkheid werkt als de beschik baarheid van de resource provider API-versie die eerder is besproken in de sectie ' de versie van alle resource typen controleren '.

Als u een lijst met de beschik bare API-versies voor de VM-extensie resource wilt ophalen, gebruikt u de cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) met de resource provider **micro soft. Compute** , zoals wordt weer gegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

U kunt ook VM-extensies gebruiken in virtuele-machine schaal sets. Dezelfde locatie voorwaarden zijn van toepassing. Als u uw sjabloon voor consistentie in de Cloud wilt ontwikkelen, moet u ervoor zorgen dat de API-versies beschikbaar zijn op alle locaties die u wilt implementeren. Als u de API-versies van de VM-extensie resource voor schaal sets wilt ophalen, gebruikt u dezelfde cmdlet als voorheen, maar geeft u het resource type voor de virtuele-machine schaal sets op zoals weer gegeven:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Er is ook een versie van elke specifieke extensie. Deze versie wordt weer gegeven in de `typeHandlerVersion` eigenschap van de VM-extensie. Zorg ervoor dat de versie die is opgegeven in het `typeHandlerVersion` element van de VM-extensies van uw sjabloon beschikbaar is op de locaties waar u de sjabloon wilt implementeren. Met de volgende code wordt bijvoorbeeld versie 1,7 opgegeven:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Als u een lijst met de beschik bare versies voor een specifieke VM-extensie wilt ophalen, gebruikt u de cmdlet [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) . In het volgende voor beeld worden de beschik bare versies van de VM-extensie voor Power shell DSC (desired state Configuration) opgehaald van **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Gebruik de opdracht [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) om een lijst met uitgevers op te halen. Gebruik de [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) Commend om het type aanvraag te verkrijgen.

## <a name="tips-for-testing-and-automation"></a>Tips voor testen en automatisering

Het is een uitdaging om alle gerelateerde instellingen, mogelijkheden en beperkingen bij te houden tijdens het ontwerpen van een sjabloon. De gemeen schappelijke aanpak is het ontwikkelen en testen van sjablonen op één Cloud voordat andere locaties zijn gericht. De eerdere tests die worden uitgevoerd in het ontwerp proces, zijn echter de minder probleem oplossing en code die uw ontwikkel team opnieuw moet schrijven. Implementaties die mislukken vanwege locatie afhankelijkheden, kunnen tijdrovend zijn om problemen op te lossen. Daarom raden we u aan om zo snel mogelijk geautomatiseerd testen in de ontwerp cyclus uit te voeren. U hebt uiteindelijk minder ontwikkel tijd en minder resources nodig en uw Cloud-consistente artefacten zullen nog waardevoler worden.

De volgende afbeelding toont een typisch voor beeld van een ontwikkelings proces voor een team met behulp van een Integrated Development Environment (IDE). In verschillende fasen van de tijd lijn worden verschillende test typen uitgevoerd. Hier werken twee ontwikkel aars aan dezelfde oplossing, maar dit scenario is ook van toepassing op één ontwikkelaar of een groot team. Elke ontwikkelaar maakt normaal gesp roken een lokale kopie van een centrale opslag plaats, waardoor elk item kan werken aan de lokale kopie zonder dat dit van invloed is op de andere bestanden die met dezelfde bestands namen werken.

![Diagram toont twee sets van eenheids testen en integratie tests parallel op lokale I D E, die in de C I/C D-ontwikkelings stroom samen voegen in eenheids tests, vervolgens op integratie tests, vervolgens implementatie testen en implementatie.](./media/templates-cloud-consistency/workflow.png)

Houd rekening met de volgende tips voor testen en automatisering:

* Maak gebruik van hulpprogram ma's voor testen. Visual Studio code en Visual Studio bevatten bijvoorbeeld IntelliSense en andere functies die u kunnen helpen bij het valideren van uw sjablonen.
* Als u de code kwaliteit tijdens de ontwikkeling van de lokale IDE wilt verbeteren, voert u statische code analyses uit met eenheids testen en integratie tests.
* Voor een nog betere ervaring tijdens de eerste ontwikkeling moet u testen en integratie tests alleen waarschuwen wanneer een probleem wordt gevonden en door gaan met de tests. Op die manier kunt u de problemen identificeren die moeten worden opgelost en de volg orde van de wijzigingen bepalen, ook wel test-aangedreven implementatie (TDD) genoemd.
* Houd er rekening mee dat sommige tests kunnen worden uitgevoerd zonder te zijn verbonden met Azure Resource Manager. Andere, zoals de implementatie van een sjabloon testen, vereisen dat Resource Manager bepaalde acties uitvoert die niet offline kunnen worden uitgevoerd.
* Het testen van een implementatie sjabloon met de validatie-API is niet gelijk aan een werkelijke implementatie. Ook als u een sjabloon uit een lokaal bestand implementeert, worden verwijzingen naar geneste sjablonen in de sjabloon rechtstreeks opgehaald door Resource Manager en worden artefacten waarnaar wordt verwezen door VM-extensies opgehaald door de VM-agent die wordt uitgevoerd in de geïmplementeerde VM.

## <a name="next-steps"></a>Volgende stappen

* [Overwegingen voor Azure Resource Manager sjablonen](/azure-stack/user/azure-stack-develop-templates)
* [Aanbevolen procedures voor ARM-sjablonen](template-syntax.md)

---
title: Een app klonen met PowerShell
description: Meer informatie over het klonen van App Service app naar een nieuwe app met behulp van PowerShell. Er worden diverse kloonscenario's behandeld, waaronder Traffic Manager integratie.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 63ab20b16ae41aa48822f1b5c8e733c93d97f581
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833180"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service-app klonen met Behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de release van Microsoft Azure PowerShell versie 1.1.0 is een nieuwe optie toegevoegd waarmee u een bestaande App Service-app kunt klonen naar een nieuwe app in een andere regio of `New-AzWebApp` in dezelfde regio. Met deze optie kunnen klanten snel en eenvoudig een aantal apps in verschillende regio's implementeren.

Het klonen van apps wordt ondersteund voor App Service-abonnementen standard, Premium, Premium V2 en Isolated. De nieuwe functie maakt gebruik van dezelfde beperkingen als App Service Backup-functie. Zie [Back-ups](manage-backup.md)maken van een app in Azure App Service.

## <a name="cloning-an-existing-app"></a>Een bestaande app klonen
Scenario: Een bestaande app in de regio VS - zuid-centraal en u wilt de inhoud klonen naar een nieuwe app in de regio VS - noord-centraal. Dit kan worden bereikt met behulp van de Azure Resource Manager versie van de PowerShell-cmdlet om een nieuwe app te maken met de `-SourceWebApp` optie .

Als u de naam van de resourcegroep kent die de bron-app bevat, kunt u de volgende PowerShell-opdracht gebruiken om de gegevens van de bron-app op te halen (in dit geval met de naam `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u een nieuwe App Service plan wilt maken, kunt u de opdracht `New-AzAppServicePlan` gebruiken, zoals in het volgende voorbeeld

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Met de opdracht kunt u de nieuwe app maken in de regio VS - noord-centraal en deze aan een bestaand `New-AzWebApp` App Service maken. Bovendien kunt u dezelfde resourcegroep als de bron-app gebruiken of een nieuwe resourcegroep definiëren, zoals wordt weergegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Als u een bestaande app wilt klonen, inclusief alle bijbehorende implementatiesleuven, moet u de `IncludeSourceWebAppSlots` parameter gebruiken.  Houd er rekening mee `IncludeSourceWebAppSlots` dat de parameter alleen wordt ondersteund voor het klonen van een hele app, inclusief alle sleuven. De volgende PowerShell-opdracht demonstreert het gebruik van die parameter met de `New-AzWebApp` opdracht :

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Als u een bestaande app binnen dezelfde regio wilt klonen, moet u een nieuwe resourcegroep en een nieuw App Service-plan in dezelfde regio maken en vervolgens de volgende PowerShell-opdracht gebruiken om de app te klonen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Een bestaande app klonen naar een App Service Environment
Scenario: Een bestaande app in de regio VS - zuid-centraal en u wilt de inhoud klonen naar een nieuwe app naar een bestaande App Service Environment (ASE).

Als u de naam van de resourcegroep kent die de bron-app bevat, kunt u de volgende PowerShell-opdracht gebruiken om de gegevens van de bron-app op te halen (in dit geval met de naam `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u de naam van de ASE en de naam van de resourcegroep weet waar de ASE bij hoort, kunt u de nieuwe app maken in de bestaande ASE, zoals wordt weergegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

De parameter is vereist vanwege een verouderde reden, maar wordt genegeerd wanneer `Location` u de app in een ASE maakt. 

## <a name="cloning-an-existing-app-slot"></a>Een bestaande app-sleuf klonen
Scenario: u wilt een bestaande implementatiesleuf van een app klonen naar een nieuwe app of een nieuwe sleuf. De nieuwe app kan zich in dezelfde regio als de oorspronkelijke app-sleuf of in een andere regio.

Als u de naam van de resourcegroep kent die de bron-app bevat, kunt u de volgende PowerShell-opdracht gebruiken om de gegevens van de bron-app-sleuf (in dit geval met de naam ) op te halen die is `source-appslot` gekoppeld aan `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

De volgende opdracht laat zien hoe u een kloon van de bron-app naar een nieuwe app maakt:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Een Traffic Manager tijdens het klonen van een app
Het maken van apps voor meerdere regio's en het configureren van Azure Traffic Manager om verkeer naar al deze apps te routeerden, is een belangrijk scenario om ervoor te zorgen dat de apps van klanten zeer beschikbaar zijn. Wanneer u een bestaande app kloont, hebt u de mogelijkheid om beide apps te verbinden met een nieuw Traffic Manager-profiel of een bestaande app. Alleen Azure Resource Manager versie van Traffic Manager wordt ondersteund.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Een nieuw Traffic Manager maken tijdens het klonen van een app
Scenario: u wilt een app klonen naar een andere regio tijdens het configureren van een Azure Resource Manager Traffic Manager-profiel dat beide apps bevat. De volgende opdracht laat zien hoe u een kloon van de bron-app naar een nieuwe app maakt tijdens het configureren van een Traffic Manager profiel:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Nieuwe gekloonde app toevoegen aan een bestaand Traffic Manager profiel
Scenario: u hebt al een Azure Resource Manager Traffic Manager-profiel en u wilt beide apps toevoegen als eindpunten. Als u dit wilt doen, moet u eerst de bestaande traffic manager-profiel-id samenstellen. U hebt de abonnements-id, de naam van de resourcegroep en de bestaande Traffic Manager-profielnaam nodig.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Nadat u de id van het verkeerslogboek hebt, wordt met de volgende opdracht gedemonstreerd hoe u een kloon van de bron-app naar een nieuwe app maakt terwijl u deze toevoegt aan een bestaand Traffic Manager profiel:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Als u een foutbericht ontvangt met de tekst 'SSL-validatie op de hostnaam van Traffic Manager mislukt', wordt u het beste het kenmerk -IgnoreCustomHostNames in de PowerShell-cmdlet gebruikt tijdens het uitvoeren van de kloonbewerking, of gebruik anders de portal.

## <a name="current-restrictions"></a>Huidige beperkingen
Dit zijn de bekende beperkingen voor het klonen van apps:

* Instellingen voor automatisch schalen worden niet gekloond
* Instellingen voor back-upschema worden niet gekloond
* VNET-instellingen worden niet gekloond
* App Insights wordt niet automatisch ingesteld in de doel-app
* Instellingen voor eenvoudige auth worden niet gekloond
* Kudu-extensie wordt niet gekloond
* TiP-regels worden niet gekloond
* Database-inhoud is niet gekloond
* Uitgaande IP-adressen worden gewijzigd als u naar een andere schaaleenheid kloont
* Niet beschikbaar voor Linux-apps
* Beheerde identiteiten worden niet gekloond

### <a name="references"></a>Referenties
* [App Service klonen](app-service-web-app-cloning.md)
* [Een back-up maken van een app in Azure App Service](manage-backup.md)
* [Azure Resource Manager ondersteuning voor Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Inleiding tot de App Service-omgeving](environment/intro.md)
* [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)


---
title: Een app klonen met PowerShell
description: Meer informatie over het klonen van uw App Service-app naar een nieuwe app met behulp van Power shell. Diverse kloon scenario's worden behandeld, waaronder Traffic Manager-integratie.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e3ae342e7cbd8a9c2e126de7666d07f0664be407
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573639"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>App-klonen Azure App Service met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de release van Microsoft Azure PowerShell versie 1.1.0 is een nieuwe optie toegevoegd waarmee `New-AzWebApp` u een bestaande app service app kunt klonen voor een nieuwe app in een andere regio of in dezelfde regio. Met deze optie kunnen klanten snel en eenvoudig een aantal apps implementeren in verschillende regio's.

Het klonen van apps wordt ondersteund voor Standard-, Premium-, Premium v2-en geïsoleerde app service-abonnementen. De nieuwe functie gebruikt dezelfde beperkingen als App Service back-upfunctie. Zie [een back-up maken van een app in azure app service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Een bestaande app klonen
Scenario: een bestaande app in de regio Zuid-Centraal VS en u wilt de inhoud klonen naar een nieuwe app in de regio Noord-Centraal vs. U kunt dit doen met behulp van de Azure Resource Manager versie van de Power shell-cmdlet om een nieuwe app te maken met de `-SourceWebApp` optie.

Als u de naam van de resource groep kent die de bron-app bevat, kunt u de volgende Power shell-opdracht gebruiken om de gegevens van de bron-app op te halen (in dit geval met de naam `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u een nieuw App Service plan wilt maken, kunt u `New-AzAppServicePlan` de opdracht gebruiken zoals in het volgende voor beeld

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Met de `New-AzWebApp` opdracht kunt u de nieuwe app maken in de regio Noord-Centraal VS en deze koppelen aan een bestaand app service plan. Daarnaast kunt u dezelfde resource groep gebruiken als de bron-app, of een nieuwe resource groep definiëren, zoals wordt weer gegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Als u een bestaande app wilt klonen, inclusief alle gekoppelde implementatie sleuven, moet u de `IncludeSourceWebAppSlots` para meter gebruiken.  Houd er rekening mee dat de `IncludeSourceWebAppSlots` para meter alleen wordt ondersteund voor het klonen van een volledige app, inclusief alle sleuven. Met de volgende Power shell-opdracht ziet u hoe die para meter wordt gebruikt met de `New-AzWebApp` opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Als u een bestaande app in dezelfde regio wilt klonen, moet u een nieuwe resource groep en een nieuw app service-plan maken in dezelfde regio. vervolgens gebruikt u de volgende Power shell-opdracht om de app te klonen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Een bestaande app klonen naar een App Service Environment
Scenario: een bestaande app in de regio Zuid-Centraal VS en u wilt de inhoud klonen naar een nieuwe app voor een bestaande App Service Environment (ASE).

Als u de naam van de resource groep kent die de bron-app bevat, kunt u de volgende Power shell-opdracht gebruiken om de gegevens van de bron-app op te halen (in dit geval met de naam `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u de naam van de ASE en de naam van de resource groep waarvan de ASE deel uitmaakt, weet, kunt u de nieuwe app maken in de bestaande ASE, zoals wordt weer gegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

De `Location` para meter is vereist vanwege een verouderde reden, maar wordt genegeerd wanneer u de app in een ASE maakt. 

## <a name="cloning-an-existing-app-slot"></a>Een bestaande app-sleuf klonen
Scenario: u wilt een bestaande implementatie site van een app klonen naar een nieuwe app of een nieuwe sleuf. De nieuwe app kan zich in dezelfde regio bevinden als de oorspronkelijke app-sleuf of in een andere regio.

Als u de naam van de resource groep kent die de bron-app bevat, kunt u de volgende Power shell-opdracht gebruiken om de gegevens van de bron app-sleuf (in dit geval met de naam) te verkrijgen die is `source-appslot` gekoppeld aan `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

De volgende opdracht illustreert het maken van een kloon van de bron-app naar een nieuwe app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Traffic Manager configureren tijdens het klonen van een app
Het maken van apps voor meerdere regio's en het configureren van Azure Traffic Manager voor het routeren van verkeer naar al deze apps, is een belang rijk scenario om ervoor te zorgen dat de apps van klanten Maxi maal beschikbaar zijn. Wanneer u een bestaande app wilt klonen, hebt u de mogelijkheid om beide apps te verbinden met een nieuw Traffic Manager-profiel of een bestaande. Alleen Azure Resource Manager versie van Traffic Manager wordt ondersteund.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Een nieuw Traffic Manager profiel maken tijdens het klonen van een app
Scenario: u wilt een app klonen naar een andere regio, terwijl u een Azure Resource Manager Traffic Manager-profiel configureert dat beide apps bevat. De volgende opdracht illustreert het maken van een kloon van de bron-app naar een nieuwe app tijdens het configureren van een nieuw Traffic Manager profiel:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Nieuwe gekloonde app toevoegen aan een bestaand Traffic Manager profiel
Scenario: u hebt al een Azure Resource Manager Traffic Manager-profiel en wilt beide apps als eind punten toevoegen. Hiervoor moet u eerst de bestaande Traffic Manager-profiel-ID assembleren. U hebt de abonnements-ID, de naam van de resource groep en de naam van het bestaande Traffic Manager-profiel nodig.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Nadat u de Traffic Manager-ID hebt, wordt met de volgende opdracht gedemonstreerd hoe u een kloon van de bron-app maakt naar een nieuwe app terwijl u deze toevoegt aan een bestaand Traffic Manager profiel:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Als u een fout bericht ontvangt met de melding ' SSL-validatie op de hostnaam van Traffic Manager is mislukt ', wordt u aangeraden het kenmerk-IgnoreCustomHostNames in de Power shell-cmdlet te gebruiken tijdens het uitvoeren van de kloon bewerking of het gebruik van de portal.

## <a name="current-restrictions"></a>Huidige beperkingen
Dit zijn de bekende beperkingen voor het klonen van apps:

* Instellingen voor automatisch schalen worden niet gekloond
* De instellingen voor het back-upschema zijn niet gekloond
* VNET-instellingen worden niet gekloond
* App Insights wordt niet automatisch ingesteld op de doel-app
* Eenvoudige verificatie-instellingen worden niet gekloond
* De kudu-extensie is niet gekloond
* TiP-regels worden niet gekloond
* Data Base-inhoud is niet gekloond
* Uitgaande IP-adressen worden gewijzigd bij het klonen naar een andere schaal eenheid
* Niet beschikbaar voor Linux-apps
* Beheerde identiteiten zijn niet gekloond

### <a name="references"></a>Referenties
* [App Service klonen](app-service-web-app-cloning.md)
* [Een back-up maken van een app in Azure App Service](manage-backup.md)
* [Azure Resource Manager ondersteuning voor Azure Traffic Manager preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Inleiding tot de App Service-omgeving](environment/intro.md)
* [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)


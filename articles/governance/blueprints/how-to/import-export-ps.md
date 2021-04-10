---
title: Blauw drukken importeren en exporteren met Power shell
description: Meer informatie over het werken met uw blauw drukken-definities als code. Delen, broncode beheer en beheren met de opdrachten exporteren en importeren.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919983"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Blauw druk-definities importeren en exporteren met Power shell

Azure-blauw drukken kan volledig worden beheerd via Azure Portal. Als organisaties in hun gebruik van Azure-blauw drukken beginnen, moeten ze denken aan de definities van blauw druk als beheerde code. Dit concept wordt vaak aangeduid als een infra structuur als code (IaC). Uw blauw drukken-definities behandelen als code biedt meer voor delen dan wat Azure Portal biedt. Dit zijn enkele voordelen:

- Blauw druk-definities delen
- Back-ups maken van uw blauw drukken-definities
- Blauw drukken-definities opnieuw gebruiken in verschillende tenants of abonnementen
- De blauw druk-definities in broncode beheer plaatsen
  - Automatische tests van blauw druk-definities in test omgevingen
  - Ondersteuning van pijp lijnen voor continue integratie en continue implementatie (CI/CD)

Wat uw redenen zijn, het beheren van uw blauw drukken-definities als code biedt voor delen. In dit artikel wordt beschreven hoe u `Import-AzBlueprintWithArtifact` de `Export-AzBlueprintWithArtifact` opdrachten en gebruikt in de module [AZ. blauw](https://powershellgallery.com/packages/Az.Blueprint/) drukken.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een gemiddelde werk ervaring hebt van Azure-blauw drukken. Als u dit nog niet hebt gedaan, kunt u de volgende artikelen door lopen:

- [Een blauwdruk maken in de portal](../create-blueprint-portal.md)
- Meer informatie over [implementatie fasen](../concepts/deployment-stages.md) en [de levens duur van de blauw druk](../concepts/lifecycle.md)
- Blauw druk-definities en-toewijzingen [maken](../create-blueprint-powershell.md) en [beheren](./manage-assignments-ps.md) met Power shell

Als deze nog niet is geïnstalleerd, volgt u de instructies in [De module Az.Blueprint toevoegen](./manage-assignments-ps.md#add-the-azblueprint-module) om de module **Az.Blueprint** van de PowerShell Gallery te installeren en te valideren.

## <a name="folder-structure-of-a-blueprint-definition"></a>Mapstructuur van een definitie van een blauw druk

Voordat u de blauw drukken gaat bekijken en importeren, bekijken we hoe de bestanden waaruit de definitie van de blauw druk bestaat, zijn gestructureerd. Een definitie van een blauw druk moet worden opgeslagen in een eigen map.

> [!IMPORTANT]
> Als er geen waarde wordt door gegeven aan de para meter **name** van de `Import-AzBlueprintWithArtifact` cmdlet, wordt de naam van de map waarvan de definitie van de blauw druk is opgeslagen, gebruikt.

Samen met de definitie van de blauw druk, die moet worden benoemd `blueprint.json` , zijn de artefacten waarvan de definitie van de blauw druk is samengesteld. Elk artefact moet zich in de submap met de naam bevindt `artifacts` .
Samen, de structuur van uw blauw druk-definitie als JSON-bestanden in mappen moet er als volgt uitzien:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>De definitie van de blauw druk exporteren

De stappen voor het exporteren van de definitie van de blauw druk zijn eenvoudig. Het exporteren van de blauw druk-definitie kan handig zijn voor het delen, het maken van een back-up of het in broncode beheer.

- **Blauw druk** [vereist]
  - Hiermee wordt de definitie van de blauw druk opgegeven
  - Gebruiken `Get-AzBlueprint` om het referentie object op te halen
- **OutputPath** [vereist]
  - Hiermee geeft u het pad op voor het opslaan van de JSON-bestanden van de blauw druk op
  - De uitvoer bestanden bevinden zich in een submap met de naam van de definitie van de blauw druk
- **Versie** (optioneel)
  - Hiermee geeft u de versie op die moet worden uitgevoerd als het referentie object **blauw** drukken verwijzingen naar meer dan één versie bevat.

1. Een verwijzing naar de definitie van de blauw druk ophalen om uit het abonnement te exporteren, weer gegeven als `{subId}` :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Gebruik de `Export-AzBlueprintWithArtifact` cmdlet om de opgegeven definitie van blauw drukken te exporteren:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>De definitie van de blauw druk importeren

Als u een [geëxporteerde blauw druk](#export-your-blueprint-definition) hebt of een hand matig gemaakte blauw definitie hebt gemaakt in de [vereiste mappen structuur](#folder-structure-of-a-blueprint-definition), kunt u die blauw druk-definitie importeren in een andere beheer groep of een ander abonnement.

Zie de [Azure Blueprint github opslag plaats](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)voor voor beelden van ingebouwde blauw drukken-definities.

- **Naam** [vereist]
  - Hiermee geeft u de naam op voor de nieuwe definitie van de blauw druk
- **InputPath** [vereist]
  - Hiermee geeft u het pad op voor het maken van de definitie van de blauw druk van
  - Moet overeenkomen met de [vereiste mappen structuur](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (optioneel)
  - De beheer groep-ID voor het opslaan van de definitie van de blauw druk op indien niet de huidige context standaard
  - U moet **ManagementGroupId** of **SubscriptionId** opgeven
- **SubscriptionId** (optioneel)
  - De abonnements-ID voor het opslaan van de definitie van de blauw druk op indien niet de huidige context standaard
  - U moet **ManagementGroupId** of **SubscriptionId** opgeven

1. Gebruik de `Import-AzBlueprintWithArtifact` cmdlet om de opgegeven definitie van blauw drukken te importeren:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Zodra de definitie van de blauw druk is geïmporteerd, [wijst u deze toe met Power shell](./manage-assignments-ps.md#create-blueprint-assignments).

Raadpleeg de volgende artikelen voor meer informatie over het maken van geavanceerde blauw drukken-definities:

- [Statische en dynamische para meters](../concepts/parameters.md)gebruiken.
- Pas de [volg orde van de blauw druk](../concepts/sequencing-order.md)aan.
- Beveilig implementaties met [behulp van de resource vergrendeling blauw](../concepts/resource-locking.md)drukken.
- [Blauw drukken als code beheren](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).
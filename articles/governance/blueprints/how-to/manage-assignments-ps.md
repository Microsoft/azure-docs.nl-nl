---
title: Toewijzingen beheren met Power shell
description: Meer informatie over het beheren van blauw druk-toewijzingen met de officiële Azure blauw drukken Power shell-module, AZ. blauw druk.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: d60fb887e07b4697b8e86a4e2fd74a735ac0bb58
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919373"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Toewijzingen beheren met Power shell

Een blauw druk-toewijzing kan worden beheerd met de module **AZ. blauw** drukken Azure PowerShell. De module ondersteunt het ophalen, maken, bijwerken en verwijderen van toewijzingen. De module kan ook details over bestaande blauw drukken-definities ophalen. In dit artikel wordt beschreven hoe u de module installeert en hoe u deze gebruikt.

## <a name="add-the-azblueprint-module"></a>De module AZ. Blue toevoegen

Als u Azure PowerShell wilt inschakelen om blauw drukken-toewijzingen te beheren, moet u de module toevoegen. Deze module kan worden gebruikt met lokaal geïnstalleerde PowerShell, met [Azure Cloud Shell](https://shell.azure.com) of met de [Azure PowerShell Docker-installatiekopie](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Basisvereisten

Voor de Azure-blauw drukken-module is de volgende software vereist:

- Azure PowerShell 1.5.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.
- PowerShellGet 2.0.1 of hoger. Als deze nog niet is geïnstalleerd of bijgewerkt, volgt u [deze instructies](/powershell/scripting/gallery/installing-psget) op.

### <a name="install-the-module"></a>Installeer de module

De Azure blauw drukken-module voor Power shell is **AZ. blauw druk**.

1. Voer vanuit een PowerShell-prompt met **beheerdersrechten** de volgende opdracht uit:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Als **AZ. accounts** al is geïnstalleerd, kan het nodig zijn om `-AllowClobber` de installatie te forceren.

1. Controleer of de module is geïmporteerd en de juiste versie is (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Blauw drukken-definities ophalen

De eerste stap bij het werken met een toewijzing wordt vaak een verwijzing naar de definitie van een blauw druk opgehaald.
De `Get-AzBlueprint` cmdlet haalt een of meer blauw drukken-definities op. De cmdlet kan blauw drukken-definities ophalen van een beheer groep met `-ManagementGroupId {mgId}` of een abonnement met `-SubscriptionId {subId}` . De para meter **name** haalt een blauw druk-definitie op, maar moet worden gebruikt met **ManagementGroupId** of **SubscriptionId**. **Versie** kan worden gebruikt met **naam** om duidelijk te zijn over welke blauw druk-definitie wordt geretourneerd. In plaats van **versie** wordt de `-LatestPublished` meest recent gepubliceerde versie door de switch oppakken.

In het volgende voor beeld worden `Get-AzBlueprint` alle versies van een blauw druk-definitie met de naam ' 101-blauw drukken-definitie-abonnement ' opgehaald van een specifiek abonnement dat wordt weer gegeven als `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

De voorbeeld uitvoer voor een blauw druk-definitie met meerdere versies ziet er als volgt uit:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

De [blauw druk-para meters](../concepts/parameters.md#blueprint-parameters) voor de definitie van de blauw druk kunnen worden uitgevouwen om meer informatie te geven.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Blauw druk-toewijzingen ophalen

Als de blauw druk-toewijzing al bestaat, kunt u er een verwijzing naar krijgen met de `Get-AzBlueprintAssignment` cmdlet. De cmdlet vindt **SubscriptionId** en **name** als optionele para meters. Als **SubscriptionId** niet is opgegeven, wordt de huidige abonnements context gebruikt.

In het volgende voor beeld wordt `Get-AzBlueprintAssignment` een enkele blauw druk-toewijzing met de naam ' toewijzing-Lock-resource-groups ' opgehaald uit een specifiek abonnement dat wordt weer gegeven als `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

De voorbeeld uitvoer voor een blauw druk-toewijzing ziet er als volgt uit:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Blauw druk-toewijzingen maken

Als de blauw druk toewijzing nog niet bestaat, kunt u deze maken met de `New-AzBlueprintAssignment` cmdlet. Voor deze cmdlet worden de volgende para meters gebruikt:

- **Naam** [vereist]
  - Geeft de naam aan van de toewijzing van de blauw druk
  - Moet uniek zijn en niet al bestaan in **SubscriptionId**
- **Blauw druk** [vereist]
  - Hiermee wordt de definitie van de blauw druk opgegeven die moet worden toegewezen
  - Gebruiken `Get-AzBlueprint` om het referentie object op te halen
- **Locatie** [vereist]
  - Hiermee geeft u de regio voor het door het systeem toegewezen beheerde identiteits-en abonnements implementatie object op dat moet worden gemaakt in
- **Abonnement** (optioneel)
  - Hiermee geeft u het abonnement op waarop de toewijzing wordt geïmplementeerd
  - Als niet wordt vermeld, wordt standaard de context van het huidige abonnement gebruikt
- **Vergren delen** (optioneel)
  - Hiermee wordt de [resource vergrendeling van de blauw druk](../concepts/resource-locking.md) gedefinieerd die moet worden gebruikt voor geïmplementeerde resources
  - Ondersteunde opties: _geen_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Als dit niet is gegeven, wordt standaard ingesteld op _geen_
- **SystemAssignedIdentity** (optioneel)
  - Selecteer deze optie om een door het systeem toegewezen beheerde identiteit voor de toewijzing te maken en de resources te implementeren
  - Standaard instelling voor de para meter Identity
  - Kan niet worden gebruikt met **UserAssignedIdentity**
- **UserAssignedIdentity** (optioneel)
  - Hiermee geeft u de door de gebruiker toegewezen beheerde identiteit op die moet worden gebruikt voor de toewijzing en voor het implementeren van de resources
  - Onderdeel van de para meter ' identiteit ' die is ingesteld
  - Kan niet worden gebruikt met **SystemAssignedIdentity**
- **Para meter** (optioneel)
  - Een [hash-tabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met sleutel-waardeparen voor het instellen van [dynamische para meters](../concepts/parameters.md#dynamic-parameters) voor de toewijzing van de blauw druk
  - De standaard waarde voor een dynamische para meter is de **DefaultValue** in de definitie
  - Als er geen para meter is ingesteld en geen **DefaultValue** heeft, is de para meter niet optioneel

    > [!NOTE]
    > De **para meter** biedt geen ondersteuning voor secureStrings.

- **ResourceGroupParameter** (optioneel)
  - Een [hash-tabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met bron groeps artefacten
  - Elke tijdelijke aanduiding voor een resource groeps artefact heeft sleutel/waarde-paren voor het dynamisch instellen van de **naam** en **locatie** van het bron groeps artefact
  - Als er geen para meter voor een resource groep is ingesteld en geen **DefaultValue** heeft, is de para meter van de resource groep niet optioneel
- **AssignmentFile** (optioneel)
  - Het pad naar de weer gave van een JSON-bestand van een blauw druk-toewijzing
  - Deze para meter maakt deel uit van een Power shell-parameterset die alleen de **naam**, **blauw druk** en **SubscriptionId** bevat, plus de algemene para meters.

### <a name="example-1-provide-parameters"></a>Voor beeld 1: para meters opgeven

In het volgende voor beeld wordt een nieuwe toewijzing gemaakt van versie 1,1 van de blauw druk-definitie ' My-blauw ' die is opgehaald met `Get-AzBlueprint` , wordt de locatie van het beheerde identiteits-en toewijzings object ingesteld op ' westus2 ', worden de resources met _AllResourcesReadOnly_ vergrendeld en worden de hash-tabellen ingesteld voor zowel **para meter** -als **ResourceGroupParameter** op een specifiek abonnement dat wordt weer gegeven als `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

De voorbeeld uitvoer voor het maken van een blauw druk-toewijzing ziet er als volgt uit:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Voor beeld 2: een definitie bestand voor een JSON-toewijzing gebruiken

In het volgende voor beeld wordt bijna dezelfde toewijzing gemaakt als [voor beeld 1](#example-1-provide-parameters). In plaats van para meters door te geven aan de cmdlet, wordt in het voor beeld het gebruik van een JSON-toewijzings definitie bestand en de para meter **AssignmentFile** weer gegeven. Daarnaast is de eigenschap **excludedPrincipals** geconfigureerd als onderdeel van de **vergren delingen**. Er is geen Power shell-para meter voor **excludedPrincipals** en de eigenschap kan alleen worden geconfigureerd door deze in te stellen via het definitie bestand van de JSON-toewijzing.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Voor een voor beeld van het definitie bestand van de JSON-toewijzing voor een door de gebruiker toegewezen beheerde identiteit raadpleegt u de hoofd tekst van de aanvraag in het [voor beeld: toewijzing met door de gebruiker toegewezen beheerde identiteit](/rest/api/blueprints/assignments/createorupdate#examples) voor rest API.

## <a name="update-blueprint-assignments"></a>Blauw druk-toewijzingen bijwerken

Soms is het nodig om een blauw druk-toewijzing bij te werken die al is gemaakt. De `Set-AzBlueprintAssignment` cmdlet verwerkt deze actie. De cmdlet krijgt de meeste van de para meters die door de cmdlet worden gebruikt `New-AzBlueprintAssignment` , waardoor alles dat is ingesteld voor de toewijzing, kan worden bijgewerkt. De uitzonde ringen zijn de _naam_, _blauw druk_ en _SubscriptionId_. Alleen de beschik bare waarden worden bijgewerkt.

Zie [regels voor het bijwerken van toewijzingen](./update-existing-assignments.md#rules-for-updating-assignments)voor informatie over wat er gebeurt bij het bijwerken van een blauw druk-toewijzing.

- **Naam** [vereist]
  - Geeft de naam aan van de toewijzing van de blauw druk die moet worden bijgewerkt
  - Wordt gebruikt voor het zoeken van de toewijzing die moet worden bijgewerkt, niet voor het wijzigen van de toewijzing
- **Blauw druk** [vereist]
  - Hiermee geeft u de blauw druk definitie van de blauw druk toewijzen
  - Gebruiken `Get-AzBlueprint` om het referentie object op te halen
  - Wordt gebruikt voor het zoeken van de toewijzing die moet worden bijgewerkt, niet voor het wijzigen van de toewijzing
- **Locatie** (optioneel)
  - Hiermee geeft u de regio voor het door het systeem toegewezen beheerde identiteits-en abonnements implementatie object op dat moet worden gemaakt in
- **Abonnement** (optioneel)
  - Hiermee geeft u het abonnement op waarop de toewijzing wordt geïmplementeerd
  - Als niet wordt vermeld, wordt standaard de context van het huidige abonnement gebruikt
  - Wordt gebruikt voor het zoeken van de toewijzing die moet worden bijgewerkt, niet voor het wijzigen van de toewijzing
- **Vergren delen** (optioneel)
  - Hiermee wordt de [resource vergrendeling van de blauw druk](../concepts/resource-locking.md) gedefinieerd die moet worden gebruikt voor geïmplementeerde resources
  - Ondersteunde opties: _geen_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optioneel)
  - Selecteer deze optie om een door het systeem toegewezen beheerde identiteit voor de toewijzing te maken en de resources te implementeren
  - Standaard instelling voor de para meter Identity
  - Kan niet worden gebruikt met **UserAssignedIdentity**
- **UserAssignedIdentity** (optioneel)
  - Hiermee geeft u de door de gebruiker toegewezen beheerde identiteit op die moet worden gebruikt voor de toewijzing en voor het implementeren van de resources
  - Onderdeel van de para meter ' identiteit ' die is ingesteld
  - Kan niet worden gebruikt met **SystemAssignedIdentity**
- **Para meter** (optioneel)
  - Een [hash-tabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met sleutel-waardeparen voor het instellen van [dynamische para meters](../concepts/parameters.md#dynamic-parameters) voor de toewijzing van de blauw druk
  - De standaard waarde voor een dynamische para meter is de **DefaultValue** in de definitie
  - Als er geen para meter is ingesteld en geen **DefaultValue** heeft, is de para meter niet optioneel

    > [!NOTE]
    > De **para meter** biedt geen ondersteuning voor secureStrings.

- **ResourceGroupParameter** (optioneel)
  - Een [hash-tabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met bron groeps artefacten
  - Elke tijdelijke aanduiding voor een resource groeps artefact heeft sleutel/waarde-paren voor het dynamisch instellen van de **naam** en **locatie** van het bron groeps artefact
  - Als er geen para meter voor een resource groep is ingesteld en geen **DefaultValue** heeft, is de para meter van de resource groep niet optioneel

In het volgende voor beeld wordt de toewijzing van versie 1,1 van de blauw druk-definitie ' mijn-blauw druk ' bijgewerkt met `Get-AzBlueprint` door de vergrendelings modus te wijzigen:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

De voorbeeld uitvoer voor het maken van een blauw druk-toewijzing ziet er als volgt uit:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Blauw drukken-toewijzingen verwijderen

Wanneer het tijd is voor het verwijderen van een blauw druk-toewijzing, wordt `Remove-AzBlueprintAssignment` deze actie door de cmdlet afgehandeld. De cmdlet krijgt een **naam** of **input object** om op te geven welke blauw druk-toewijzing moet worden verwijderd. **SubscriptionId** is _vereist_ en moet in alle gevallen worden opgegeven.

In het volgende voor beeld wordt een bestaande blauw druk-toewijzing opgehaald met `Get-AzBlueprintAssignment` en vervolgens verwijderd uit het specifieke abonnement, weer gegeven als `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>Voorbeeld van code

Alle stappen in het volgende voor beeld worden de definitie van de blauw druk opgehaald, vervolgens wordt een blauw druk-toewijzing gemaakt, bijgewerkt en verwijderd in het specifieke abonnement, aangeduid als `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).
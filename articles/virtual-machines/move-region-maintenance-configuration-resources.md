---
title: Resources die zijn gekoppeld aan een onderhoudsconfiguratie verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van resources die zijn gekoppeld aan een VM-onderhoudsconfiguratie naar een andere Azure-regio
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865239"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Resources in een onderhoudsbeheerconfiguratie verplaatsen naar een andere regio

Volg dit artikel om resources die zijn gekoppeld aan een onderhoudsbeheerconfiguratie te verplaatsen naar een andere Azure-regio. Mogelijk wilt u een configuratie om een aantal redenen verplaatsen. Bijvoorbeeld om te profiteren van een nieuwe regio, om functies of services te implementeren die beschikbaar zijn in een specifieke regio, om te voldoen aan de vereisten voor intern beleid en governance, of in reactie op capaciteitsplanning.

[Met onderhoudsbeheer,](maintenance-control.md)met aangepaste onderhoudsconfiguraties, kunt u bepalen hoe platformupdates worden toegepast op VM's en op Toegewezen Azure-hosts. Er zijn een aantal scenario's voor het verplaatsen van onderhoudsbeheer tussen regio's:

- Als u de resources wilt verplaatsen die zijn gekoppeld aan een onderhoudsconfiguratie, maar niet de configuratie zelf, volgt u dit artikel.
- Volg deze instructies om de configuratie van uw onderhoudsbeheer te verplaatsen, maar niet de resources die aan de configuratie [zijn gekoppeld.](move-region-maintenance-configuration.md)
- Volg eerst deze instructies om zowel de onderhoudsconfiguratie als de bijbehorende resources [te verplaatsen.](move-region-maintenance-configuration.md) Volg vervolgens de instructies in dit artikel.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het verplaatsen van de resources die zijn gekoppeld aan een onderhoudsbeheerconfiguratie:

- Zorg ervoor dat de resources die u verplaatst, aanwezig zijn in de nieuwe regio voordat u begint.
- Controleer de onderhoudsbeheerconfiguraties die zijn gekoppeld aan de Azure-VM's en Toegewezen Azure-hosts die u wilt verplaatsen. Controleer elke resource afzonderlijk. Er is momenteel geen manier om configuraties voor meerdere resources op te halen.
- Bij het ophalen van configuraties voor een resource:
    - Zorg ervoor dat u de abonnements-id voor het account gebruikt, niet een Azure Dedicated Host-id.
    - CLI: De parameter --output table wordt alleen gebruikt voor leesbaarheid en kan worden verwijderd of gewijzigd.
    - PowerShell: de parameter Format-Table name wordt alleen gebruikt voor de leesbaarheid en kan worden verwijderd of gewijzigd.
    - Als u PowerShell gebruikt, krijgt u een foutmelding als u configuraties probeert weer te geven voor een resource die geen bijbehorende configuraties heeft. De fout is vergelijkbaar met: 'Bewerking is mislukt met status: Niet gevonden'. Details: 404 Client error: Not Found for URL'.

    
## <a name="prepare-to-move"></a>Voorbereiden op verplaatsen

1. Definieer deze variabelen voordat u begint. We hebben voor elk voorbeeld een voorbeeld gegeven.

    **Variabele** | **Details** | **Voorbeeld**
    --- | ---
    $subId | Id voor abonnement met de onderhoudsconfiguraties | 'our-subscription-ID'
    $rsrcGroupName | Naam van resourcegroep (Azure-VM) | 'VMResourceGroup'
    $vmName | VM-resourcenaam |  'myVM'
    $adhRsrcGroupName |  Resourcegroep (toegewezen hosts) | 'HostResourceGroup'
    $adh | Toegewezen hostnaam | 'myHost'
    $adhParentName | Naam van bovenliggende resource | "HostGroup"
    
2. De onderhoudsconfiguraties ophalen met behulp van de [PowerShell-opdracht Get-AZConfigurationAssignment:](/powershell/module/az.maintenance/get-azconfigurationassignment)

    - Voer voor Azure Dedicated Hosts het volgende uit:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Voer voor Azure-VM's het volgende uit:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. De onderhoudsconfiguraties ophalen met behulp van de [CLI-opdracht az maintenance assignment:](/cli/azure/maintenance/assignment)

    - Voor Toegewezen Azure-hosts:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Voor Azure-VM's:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Verplaatsen 

1. [Volg deze instructies om](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) de Azure-VM's naar de nieuwe regio te verplaatsen.
2. Nadat de resources zijn verplaatst, past u de onderhoudsconfiguraties waar nodig opnieuw toe op de resources in de nieuwe regio, afhankelijk van of u de onderhoudsconfiguraties hebt verplaatst. U kunt een onderhoudsconfiguratie toepassen op een resource met behulp [van PowerShell](../virtual-machines/maintenance-control-powershell.md) of [CLI.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>De overstap controleren

Controleer de resources in de nieuwe regio en controleer de bijbehorende configuraties voor de resources in de nieuwe regio. 

## <a name="clean-up-source-resources"></a>Bronbronnen ops schonen

Na de overstap kunt u overwegen om de verplaatste resources in de bronregio te verwijderen.


## <a name="next-steps"></a>Volgende stappen

Volg [deze instructies](move-region-maintenance-configuration.md) als u onderhoudsconfiguraties moet verplaatsen. 

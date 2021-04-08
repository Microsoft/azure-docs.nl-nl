---
title: Resources die zijn gekoppeld aan een onderhouds configuratie verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van resources die zijn gekoppeld aan een VM-onderhouds configuratie naar een andere Azure-regio
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730503"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Resources in een onderhouds beheer configuratie verplaatsen naar een andere regio

Volg dit artikel om de resources die zijn gekoppeld aan een onderhouds configuratie te verplaatsen naar een andere Azure-regio. Mogelijk wilt u een configuratie om een aantal redenen verplaatsen. Om bijvoorbeeld te profiteren van een nieuwe regio, voor het implementeren van functies of services die beschikbaar zijn in een bepaalde regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op de capaciteits planning.

Met [onderhouds beheer](maintenance-control.md), met aangepaste onderhouds configuraties, kunt u bepalen hoe platform updates worden toegepast op vm's en aan voor Azure toegewezen hosts. Er zijn enkele scenario's voor het verplaatsen van onderhouds beheer over regio's:

- Als u de resources wilt verplaatsen die zijn gekoppeld aan een onderhouds configuratie, maar niet de configuratie zelf, volgt u dit artikel.
- Volg [deze instructies](move-region-maintenance-configuration.md)om de configuratie van uw onderhouds controle te verplaatsen, maar niet de resources die zijn gekoppeld aan de configuratie.
- Als u de onderhouds configuratie en de bijbehorende resources wilt verplaatsen, volgt u eerst [deze instructies](move-region-maintenance-configuration.md). Volg vervolgens de instructies in dit artikel.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het verplaatsen van de resources die zijn gekoppeld aan een onderhouds configuratie:

- Zorg ervoor dat de resources die u verplaatst zich in de nieuwe regio bevinden voordat u begint.
- Controleer de configuraties voor onderhouds beheer die zijn gekoppeld aan de Azure Vm's en de voor Azure toegewezen hosts die u wilt verplaatsen. Controleer elke resource afzonderlijk. Er is momenteel geen manier om configuraties voor meerdere resources op te halen.
- Bij het ophalen van configuraties voor een bron:
    - Zorg ervoor dat u de abonnements-ID voor het account gebruikt, niet een door Azure toegewezen host-ID.
    - CLI: de para meter--Output Table wordt alleen gebruikt voor de Lees baarheid en kan worden verwijderd of gewijzigd.
    - Power shell: de Format-Table naam parameter wordt alleen gebruikt voor de Lees baarheid en kan worden verwijderd of gewijzigd.
    - Als u Power shell gebruikt, wordt er een fout bericht weer geven als u configuraties probeert te vermelden voor een resource die geen gekoppelde configuraties heeft. De fout ziet er ongeveer als volgt uit: ' bewerking mislukt met status: ' is niet gevonden '. Details: 404-client fout: niet gevonden voor URL.

    
## <a name="prepare-to-move"></a>Voorbereiden om te verplaatsen

1. Voordat u begint, moet u deze variabelen definiëren. We hebben hier een voor beeld gegeven.

    **Variabele** | **Details** | **Voorbeeld**
    --- | ---
    $subId | ID voor het abonnement dat de onderhouds configuraties bevat | "ons-abonnement-ID"
    $rsrcGroupName | Naam van resource groep (Azure VM) | "VMResourceGroup"
    $vmName | Naam van de VM-resource |  MyVM
    $adhRsrcGroupName |  Resource groep (toegewezen hosts) | "HostResourceGroup"
    $adh | Toegewezen hostnaam | "myHost"
    $adhParentName | Naam van bovenliggende resource | HostGroup
    
2. De onderhouds configuraties ophalen met de Power shell [-opdracht Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) :

    - Voer voor voor Azure toegewezen hosts de volgende handelingen uit:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Voor virtuele Azure-machines voert u de volgende handelingen uit:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. De onderhouds configuraties ophalen met de CLI [AZ-onderhouds toewijzing](/cli/azure/ext/maintenance/maintenance/assignment) opdracht:

    - Voor met Azure toegewezen hosts:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Voor Azure-Vm's:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Verplaatsen 

1. [Volg deze instructies](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) om de virtuele Azure-machines te verplaatsen naar de nieuwe regio.
2. Nadat de resources zijn verplaatst, moet u de onderhouds configuraties opnieuw Toep assen op de resources in de nieuwe regio, afhankelijk van het feit of u de onderhouds configuraties hebt verplaatst. U kunt een onderhouds configuratie Toep assen op een resource met behulp van [Power shell](../virtual-machines/maintenance-control-powershell.md) of [cli](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>De verplaatsing controleren

Controleer de bronnen in de nieuwe regio en controleer de bijbehorende configuraties voor de resources in de nieuwe regio. 

## <a name="clean-up-source-resources"></a>Bron resources opschonen

Na de verplaatsing kunt u overwegen de verplaatste resources te verwijderen in de bron regio.


## <a name="next-steps"></a>Volgende stappen

Volg [deze instructies](move-region-maintenance-configuration.md) als u onderhouds configuraties moet verplaatsen. 

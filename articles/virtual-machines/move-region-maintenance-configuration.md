---
title: Een onderhoudsconfiguratie verplaatsen naar een andere Azure-regio
description: Meer informatie over het verplaatsen van een VM-onderhoudsconfiguratie naar een andere Azure-regio
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: f833f3eb9e3d94da6178a0a9a9cf4f95ec0682e7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865365"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Een onderhoudsbeheerconfiguratie verplaatsen naar een andere regio

Volg dit artikel om een onderhoudsbeheerconfiguratie te verplaatsen naar een andere Azure-regio. Mogelijk wilt u een configuratie om een aantal redenen verplaatsen. Bijvoorbeeld om te profiteren van een nieuwe regio, om functies of services te implementeren die beschikbaar zijn in een specifieke regio, om te voldoen aan de vereisten voor intern beleid en governance, of in reactie op capaciteitsplanning.

[Met onderhoudsbeheer,](maintenance-control.md)met aangepaste onderhoudsconfiguraties, kunt u bepalen hoe platformupdates worden toegepast op VM's en op Toegewezen Azure-hosts. Er zijn een aantal scenario's voor het verplaatsen van onderhoudsbeheer tussen regio's:

- Als u de configuratie van uw onderhoudsbeheer wilt verplaatsen, maar niet de resources die aan de configuratie zijn gekoppeld, volgt u de instructies in dit artikel.
- Volg deze instructies om de resources te verplaatsen die zijn gekoppeld aan een onderhoudsconfiguratie, maar niet de [configuratie zelf.](move-region-maintenance-configuration-resources.md)
- Als u zowel de onderhoudsconfiguratie als de bijbehorende resources wilt verplaatsen, volgt u eerst de instructies in dit artikel. Volg vervolgens [deze instructies.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het verplaatsen van een configuratie voor onderhoudsbeheer:

- Onderhoudsconfiguraties zijn gekoppeld aan Azure-VM's of Toegewezen Azure-hosts. Zorg ervoor dat er VM-/hostbronnen in de nieuwe regio aanwezig zijn voordat u begint.
- Identificeren: 
    - Bestaande configuraties voor onderhoudsbeheer.
    - De resourcegroepen waarin zich momenteel bestaande configuraties bevinden. 
    - De resourcegroepen waaraan de configuraties worden toegevoegd nadat ze naar de nieuwe regio zijn verplaatst. 
    - De resources die zijn gekoppeld aan de onderhoudsconfiguratie die u wilt verplaatsen.
    - Controleer of de resources in de nieuwe regio hetzelfde zijn als de resources die zijn gekoppeld aan de huidige onderhoudsconfiguraties. De configuraties kunnen dezelfde namen hebben in de nieuwe regio als in de oude, maar dit is niet vereist.

## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen 

1. Haal alle onderhoudsconfiguraties in elk abonnement op. Voer hiervoor de opdracht AZ [maintenance configuration list](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) uit om dit te doen, en vervang $subId door uw abonnements-id.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Bekijk de geretourneerde tabellijst met configuratierecords binnen het abonnement. Hier volgt een voorbeeld. Uw lijst bevat waarden voor uw specifieke omgeving.

    **Naam** | **Locatie** | **Resourcegroep**
    --- | --- | ---
    Onderhoud overslaan | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | testconfiguratie
    

3. Sla uw lijst op als referentie. Wanneer u de configuraties verplaatst, kunt u controleren of alles is verplaatst.
4. Wijs ter referentie elke configuratie/resourcegroep toe aan de nieuwe resourcegroep in de nieuwe regio.
5. Maak nieuwe onderhoudsconfiguraties in de nieuwe regio met [behulp van PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)of [CLI.](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)
6. Koppel de configuraties aan de resources in de nieuwe regio met behulp [van PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)of [CLI.](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)


## <a name="verify-the-move"></a>De verplaatsen controleren

Nadat u de configuraties hebt verplaatst, vergelijkt u configuraties en resources in de nieuwe regio met de tabellijst die u hebt voorbereid.


## <a name="clean-up-source-resources"></a>Bronbronnen ops schonen

Na de overstap kunt u overwegen om de verplaatste onderhoudsconfiguraties in de bronregio, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)of CLI te [verwijderen.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>Volgende stappen

Volg [deze instructies als](move-region-maintenance-configuration-resources.md) u resources wilt verplaatsen die zijn gekoppeld aan onderhoudsconfiguraties. 

---
title: Doel instellingen wijzigen bij het verplaatsen van virtuele Azure-machines tussen regio's met Azure resource-overschakeling
description: Meer informatie over het wijzigen van doel instellingen bij het verplaatsen van virtuele Azure-machines tussen regio's met Azure resource-overschakeling.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: eb28e4c8f6b465e2a9b38cc4571bc4a00baf4ef7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979620"
---
# <a name="modify-target-settings"></a>Doelinstelling wijzigen

In dit artikel wordt beschreven hoe u doel instellingen kunt wijzigen bij het verplaatsen van resources tussen Azure-regio's met [Azure resource](overview.md)-overschakeling.


## <a name="modify-vm-settings"></a>VM-instellingen wijzigen

Wanneer u virtuele Azure-machines en gekoppelde resources verplaatst, kunt u de doel instellingen wijzigen. 

- We raden u aan de doel instellingen alleen te wijzigen nadat de verzameling is gevalideerd.
- We raden u aan instellingen aan te passen voordat u de resources voorbereidt, omdat sommige doel eigenschappen mogelijk niet beschikbaar zijn voor bewerken nadat de voor bereiding is voltooid.

Indien
- Als u de bron resource verplaatst, kunt u de doel instellingen doorgaans wijzigen totdat u het proces initiëren start.
- Als u een bestaande resource in de bron regio toewijst, kunt u de doel instellingen wijzigen totdat het door voeren is voltooid.

### <a name="settings-you-can-modify"></a>Instellingen die u kunt wijzigen

De configuratie-instellingen die u kunt wijzigen, worden in de tabel samenvatten.

**Resource** | **Opties** 
--- | --- | --- 
**VM-naam** | Opties:<br/><br/> -Maak een nieuwe virtuele machine met dezelfde naam in de doel regio.<br/><br/> -Maak een nieuwe virtuele machine met een andere naam in de doel regio.<br/><br/> -Gebruik een bestaande virtuele machine in de doel regio.<br/><br/> Als u een nieuwe virtuele machine maakt, met uitzonde ring van de instellingen die u wijzigt, krijgt de nieuwe doel-VM dezelfde instellingen als de bron.
**VM-beschikbaarheids zone** | De beschikbaarheids zone waarin de doel-VM wordt geplaatst. Selecteer **niet van toepassing** als u de bron instellingen niet wilt wijzigen of als u de virtuele machine niet in een beschikbaarheids zone wilt plaatsen.
**VM-SKU** | Het [VM-type](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (beschikbaar in de doel regio) dat wordt gebruikt voor de doel-VM.<br/><br/> De geselecteerde doel-VM mag niet kleiner zijn dan de bron-VM.
* * Beschik bare VM-set | De beschikbaarheidsset waarin de doel-VM wordt geplaatst. Selecteer **niet van toepassing**  dat u de bron instellingen niet wilt wijzigen of als u de virtuele machine niet in een beschikbaarheidsset wilt plaatsen.
**VM-sleutel kluis** | De bijbehorende sleutel kluis wanneer u Azure Disk Encryption inschakelt voor een virtuele machine.
**Schijf versleuteling instellen** | De gekoppelde schijf versleuteling is ingesteld als de virtuele machine gebruikmaakt van een door de klant beheerde sleutel voor versleuteling aan de server zijde.
**Resourcegroep** | De resource groep waarin de doel-VM wordt geplaatst.
**Netwerk bronnen** | Opties voor netwerk interfaces, virtuele netwerken (VNets/) en netwerk beveiligings groepen/netwerk interfaces:<br/><br/> -Maak een nieuwe resource met dezelfde naam in de doel regio.<br/><br/> -Maak een nieuwe resource met een andere naam in de doel regio.<br/><br/> -Gebruik een bestaande netwerk bron in de doel regio.<br/><br/> Als u een nieuwe doel bron maakt, met uitzonde ring van de instellingen die u wijzigt, worden dezelfde instellingen toegewezen als voor de bron resource.
**Naam van openbaar IP-adres, SKU en zone** | Hiermee geeft u de naam, [SKU](../virtual-network/public-ip-addresses.md#sku)en [zone](../virtual-network/public-ip-addresses.md#standard) voor standaard open bare IP-adressen op.<br/><br/> Als u het zone redundant wilt maken, voert u in als **zone redundant**.
* * Naam van Load Balancer, SKU en zone * * | Hiermee geeft u de naam, SKU (Basic of Standard) en de zone voor de load balancer op.<br/><br/> We raden u aan om standaard-sKU te gebruiken.<br/><br/> Als u wilt dat het zone redundant is, geeft u als **zone redundante** op.
**Bronafhankelijkheden** | Opties voor elke afhankelijkheid:<br/><br/>-De resource maakt gebruik van bron afhankelijke resources die worden verplaatst naar de doel regio.<br/><br/> -De resource gebruikt verschillende afhankelijke resources die zich in de doel regio bevinden. In dit geval kunt u kiezen uit vergelijk bare resources in de doel regio.

### <a name="edit-vm-target-settings"></a>VM-doel instellingen bewerken

Als u geen afhankelijke resources van de bron regio wilt naar het doel, hebt u een aantal andere opties:

- Maak een nieuwe resource in de doel regio. Tenzij u andere instellingen opgeeft, heeft de nieuwe resource dezelfde instellingen als de bron resource.
- Gebruik een bestaande resource in de doel regio.

Het precieze gedrag is afhankelijk van het resource type. [Meer informatie](modify-target-settings.md) over het wijzigen van doel instellingen.

U wijzigt de doel instellingen voor een resource met behulp van de **doel configuratie** vermelding in de verzameling Resource verplaatsen. 

Een instelling wijzigen: 

1. Klik in de pagina **over verschillende regio's** > **doel configuratie** kolom op de koppeling voor de bron vermelding.
2. In **configuratie-instellingen** kunt u een nieuwe virtuele machine in de doel regio maken.
3. Wijs een nieuwe beschikbaarheids zone, beschikbaarheidsset of SKU toe aan de doel-VM. **Beschikbaarheids zone** en **SKU**.

Wijzigingen worden alleen aangebracht voor de resource die u wilt bewerken. U moet een afhankelijke resource afzonderlijk bijwerken.


## <a name="modify-sql-settings"></a>SQL-instellingen wijzigen

Wanneer u Azure SQL Database resources verplaatst, kunt u de doel instellingen voor de verplaatsing wijzigen. 

- Voor SQL database:
    - U wordt aangeraden de configuratie-instellingen voor het doel te wijzigen voordat u ze voorbereidt voor verplaatsen.
    - U kunt de instellingen voor de doel database wijzigen en zone redundantie voor de data base.
- Voor elastische Pools:
    -  U kunt de doel configuratie op elk gewenst moment wijzigen voordat de verplaatsing wordt gestart.
    - U kunt de elastische doel groep wijzigen en zone redundantie voor de groep. 

### <a name="sql-settings-you-can-modify"></a>SQL-instellingen die u kunt wijzigen

**Instelling** | **SQL database** | **Elastische pool**
--- | --- | ---
**Naam** | Maak een nieuwe Data Base met dezelfde naam in de doel regio.<br/><br/> Maak een nieuwe Data Base met een andere naam in de doel regio.<br/><br/> Gebruik een bestaande data base in de doel regio. | Maak een nieuwe elastische pool met dezelfde naam in de doel regio.<br/><br/> Maak een nieuwe elastische pool met een andere naam in de doel regio.<br/><br/> Gebruik een bestaande elastische pool in de doel regio.
**Zoneredundantie** | Als u wilt overstappen van een regio die zone redundantie ondersteunt naar een regio die niet, typt u **uitschakelen** in de zone-instelling.<br/><br/> Als u wilt overstappen van een regio die geen zone redundantie ondersteunt naar een regio die wel, typt u **Enable** in de zone-instelling. | Als u wilt overstappen van een regio die zone redundantie ondersteunt naar een regio die niet, typt u **uitschakelen** in de zone-instelling.<br/><br/> Als u wilt overstappen van een regio die geen zone redundantie ondersteunt naar een regio die wel, typt u **Enable** in de zone-instelling.

### <a name="edit-sql-target-settings"></a>SQL-doel instellingen bewerken

U wijzigt de doel instellingen voor een Azure SQL Database resource als volgt: 

1. Klik in **meerdere regio's** voor de resource die u wilt wijzigen, op de **doel configuratie** vermelding.
2. Geef in **configuratie-instellingen** de doel instellingen op die in de bovenstaande tabel worden beschreven.

## <a name="next-steps"></a>Volgende stappen

[Verplaats een Azure VM](tutorial-move-region-virtual-machines.md) naar een andere regio.
---
title: Een pool inrichten in een virtueel netwerk
description: Het maken van een batch-pool in een virtueel Azure-netwerk, zodat reken knooppunten veilig kunnen communiceren met andere virtuele machines in het netwerk, zoals een bestands server.
ms.topic: how-to
ms.date: 03/26/2021
ms.custom: seodec18
ms.openlocfilehash: 7213637e89cfccd1352861002c47a696d942d30f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629305"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Een Azure Batch groep maken in een virtueel netwerk

Wanneer u een Azure Batch groep maakt, kunt u de groep inrichten in een subnet van een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) (VNet) dat u opgeeft. In dit artikel wordt uitgelegd hoe u een batch-pool in een VNet instelt.

## <a name="why-use-a-vnet"></a>Waarom een VNet gebruiken?

Reken knooppunten in een pool kunnen met elkaar communiceren, zoals het uitvoeren van taken voor meerdere instanties, zonder dat hiervoor een afzonderlijk VNet is vereist. Knoop punten in een pool kunnen echter standaard niet communiceren met virtuele machines die zich buiten de groep bevinden, zoals licentie servers of bestands servers.

Als u wilt toestaan dat reken knooppunten veilig communiceren met andere virtuele machines of met een on-premises netwerk, kunt u de groep inrichten in een subnet van een Azure-VNet.

## <a name="prerequisites"></a>Vereisten

- **Verificatie**. Het gebruik van een Azure VNet vereist dat de Batch-client-API gebruikmaakt van Azure Active Directory-verificatie (AD). Azure Batch-ondersteuning voor Azure AD wordt beschreven in [Oplossingen van Batch-service verifiëren met Active Directory](batch-aad-auth.md).

- **Een Azure-VNet**. Raadpleeg de volgende sectie voor VNet-vereisten en-configuratie. Als u een VNet met een of meer subnetten vooraf wilt voorbereiden, kunt u de Azure Portal, Azure PowerShell, de Azure Command-Line-interface (CLI) of een andere methode gebruiken.
  - Zie [een virtueel netwerk maken](../virtual-network/manage-virtual-network.md#create-a-virtual-network)voor het maken van een op Azure Resource Manager gebaseerde VNet. Een VNet op basis van Resource Manager wordt aanbevolen voor nieuwe implementaties en wordt alleen ondersteund op Pools die gebruikmaken van de configuratie van virtuele machines.
  - Zie [een virtueel netwerk (klassiek) met meerdere subnetten maken](/previous-versions/azure/virtual-network/create-virtual-network-classic)om een klassiek VNet te maken. Een klassiek VNet wordt alleen ondersteund voor Pools die gebruikmaken van Cloud Services configuratie.

## <a name="vnet-requirements"></a>Vereisten voor VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Een pool maken met een VNet in de Azure Portal

Wanneer u uw VNet hebt gemaakt en hieraan een subnet hebt toegewezen, kunt u een batch-pool met dat VNet maken. Volg deze stappen om een groep te maken op basis van de Azure Portal: 

1. Ga in Azure Portal naar uw Batch-account. Dit account moet zich in hetzelfde abonnement en dezelfde regio bevinden als de resource groep die het VNet bevat dat u wilt gebruiken.
2. Selecteer in het venster **instellingen** aan de linkerkant de menu opdracht **groepen** .
3. Selecteer in het venster **groepen** de optie **toevoegen**.
4. Selecteer in het venster **groep toevoegen** de optie die u wilt gebruiken in de vervolg keuzelijst **afbeeldings type** .
5. Selecteer de juiste **Uitgever/aanbieding/SKU** voor uw aangepaste installatie kopie.
6. Geef de overige vereiste instellingen op, zoals de **knooppunt grootte**, het **doel toegewezen knoop punten** en **knoop punten met lage prioriteit**, evenals de gewenste optionele instellingen.
7. Selecteer in **Virtual Network** het virtuele netwerk en het subnet dat u wilt gebruiken.

   ![Groep toevoegen met virtueel netwerk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Door de gebruiker gedefinieerde routes voor geforceerde tunneling

Mogelijk hebt u vereisten in uw organisatie om Internet-gebonden verkeer van het subnet terug te sturen naar uw on-premises locatie voor inspectie en logboek registratie. Daarnaast hebt u geforceerde tunneling ingeschakeld voor de subnetten in uw VNet.

Om ervoor te zorgen dat de knoop punten in uw pool werken in een VNet waarvoor geforceerde tunneling is ingeschakeld, moet u de volgende door de [gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md) (UDR) voor dat subnet toevoegen:

- De batch-service moet communiceren met knoop punten voor het plannen van taken. Als u deze communicatie wilt inschakelen, voegt u een UDR toe voor elk IP-adres dat wordt gebruikt door de batch-service in de regio waar uw batch-account zich bevindt. Zie [on-premises service Tags](../virtual-network/service-tags-overview.md)voor een lijst met IP-adressen van de batch-service.

- Zorg ervoor dat uitgaand verkeer naar Azure Storage (met name Url's van het formulier `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` en `<account>.blob.core.windows.net` ) niet wordt geblokkeerd door uw on-premises netwerk.

- Als u virtuele bestands koppelingen gebruikt, controleert u de [netwerk vereisten](virtual-file-mount.md#networking-requirements) en zorgt u ervoor dat er geen vereist verkeer wordt geblokkeerd.

Wanneer u een UDR toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het **type volgende hop** in op **Internet**.

![Door de gebruiker gedefinieerde route](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> De IP-adressen van de Batch-service kunnen na verloop van tijd worden gewijzigd. Als u storingen wilt voor komen door een wijziging in het IP-adres, maakt u een proces voor het automatisch vernieuwen van de IP-adressen van de batch-service en blijft u deze actueel in uw route tabel.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over het [maken van een door de gebruiker gedefinieerde route in de Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).

---
title: Vereiste subnet-grootte bepalen & bereik
titleSuffix: Azure SQL Managed Instance
description: In dit onderwerp wordt beschreven hoe u de grootte berekent van het subnet waar Azure SQL Managed instance wordt geïmplementeerd.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012444"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Vereiste subnet grootte bepalen & bereik voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../../virtual-network/virtual-networks-overview.md).

Het aantal beheerde instanties dat kan worden geïmplementeerd in het subnet van een VNet, is afhankelijk van de grootte van het subnet (subnetmasker).

Wanneer u een beheerd exemplaar maakt, wijst Azure een aantal virtuele machines toe, afhankelijk van de laag die u hebt geselecteerd tijdens het inrichten. Omdat deze virtuele machines zijn gekoppeld aan uw subnet, zijn er IP-adressen nodig. Azure kan extra virtuele machines toewijzen om hoge Beschik baarheid te garanderen tijdens reguliere bewerkingen en onderhoud van services. Als gevolg hiervan is het aantal vereiste IP-adressen in een subnet groter dan het aantal beheerde exemplaren in dat subnet.

Een beheerd exemplaar heeft standaard minimaal 32 IP-adressen in een subnet nodig. Als gevolg hiervan kunt u het minimale subnetmasker van /27 gebruiken bij het definiëren van de IP-adresbereiken voor uw subnet. Een zorgvuldige planning van de subnetgrootte voor uw implementaties van beheerde exemplaren wordt aanbevolen. De invoer die tijdens de planning in aanmerking moet worden genomen, is als volgt:

- Aantal beheerde exemplaren, inclusief de volgende instantie parameters:
  - servicelaag
  - hardware genereren
  - aantal vCores
- Plannen voor het omhoog/omlaag schalen of het wijzigen van de servicelaag

> [!IMPORTANT]
> Een subnet-grootte met 16 IP-adressen (subnetmasker/28) staat het implementeren van het beheerde exemplaar binnen het toe, maar moet alleen worden gebruikt voor het implementeren van één instantie die wordt gebruikt voor evaluatie of in dev/test-scenario's, waarbij het schalen van bewerkingen niet wordt uitgevoerd.

## <a name="determine-subnet-size"></a>De grootte van het subnet bepalen

Grootte van uw subnet op basis van de toekomstige implementatie en schaal aanpassing. De volgende para meters kunnen u helpen bij het maken van een berekening:

- Azure gebruikt vijf IP-adressen in het subnet voor eigen behoeften
- Elk virtueel cluster wijst een extra aantal adressen toe 
- Elk beheerd exemplaar gebruikt het aantal adressen dat afhankelijk is van de prijs categorie en de generatie van hardware

> [!IMPORTANT]
> Het is niet mogelijk om het adresbereik van het subnet te wijzigen als er een resource in het subnet bestaat. Het is ook niet mogelijk om beheerde exemplaren van het ene subnet naar het andere te verplaatsen. Als dat mogelijk is, kunt u grotere subnetten gebruiken in plaats van kleiner om problemen in de toekomst te voor komen.

GP = algemeen gebruik; BC = bedrijfs kritiek; VC = virtueel cluster

| **Hardware-gen** | **Prijscategorie** | **Azure-gebruik** | **Gebruik van VC** | **Exemplaar gebruik** | **Eind*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| GEN5 | GP | 5 | 6 | 3 | 14 |
| GEN5 | BC | 5 | 6 | 5 | 16 |

  \* In het kolom totaal wordt het aantal adressen weer gegeven dat moet worden genomen wanneer één exemplaar in het subnet wordt geïmplementeerd. Elk extra exemplaar in het subnet voegt het aantal adressen toe dat wordt weer gegeven in de kolom exemplaar gebruik. Adressen die worden weer gegeven met de Azure-gebruiks kolom worden gedeeld door meerdere virtuele clusters, terwijl adressen die worden weer gegeven met de kolom VC Usage worden gedeeld tussen instanties die in dat virtuele cluster worden geplaatst.

Voor de update bewerking moet de grootte van het virtuele cluster doorgaans worden gewijzigd. In sommige gevallen is het maken van een virtueel cluster vereist voor een update bewerking (Zie het [artikel beheer bewerkingen](sql-managed-instance-paas-overview.md#management-operations)voor meer informatie). In het geval van het maken van een virtueel cluster is het aantal vereiste extra adressen gelijk aan het aantal adressen dat wordt weer gegeven door de kolom VC usage, die is vereist voor instanties die worden geplaatst in dat virtuele cluster (kolom exemplaar gebruik).

**Voor beeld 1**: u bent van plan één Managed instance voor algemeen gebruik (Gen4-hardware) en één bedrijfs kritiek beheerd exemplaar (GEN5-hardware) te hebben. Dit betekent dat u Mini maal 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP-adressen nodig hebt om te kunnen implementeren. Omdat IP-bereiken worden gedefinieerd in de kracht van 2, vereist het subnet mini maal IP-bereik van 32 (2 ^ 5) voor deze implementatie.<br><br>
Zoals hierboven vermeld, moet u in sommige gevallen een virtueel cluster maken voor de update bewerking. Dit betekent dat als voor beeld in het geval van een update voor het GEN5 Business Critical Managed instance waarvoor het maken van een virtueel cluster vereist is, extra 6 + 5 = 11 IP-adressen beschikbaar moeten zijn. Omdat u al 22 van de 32-adressen gebruikt, zijn er geen beschik bare adressen voor deze bewerking. Daarom moet u het subnet reserveren met subnetmasker/26 (64 adressen).

**Voor beeld 2**: u bent van plan drie algemene doel einden (GEN5-hardware) en twee bedrijfskritische beheerde instanties (GEN5-hardware) in hetzelfde subnet te hebben geplaatst. Dit betekent dat u 5 + 6 + 3 * 3 + 2 * 5 = 30 IP-adressen nodig hebt. Daarom moet u het subnet reserveren met subnetmasker/26. Als u een subnetmasker van/27 selecteert, wordt het resterende aantal adressen ingesteld op 2 (32 – 30). Dit zou voor komen dat er update bewerkingen voor alle exemplaren worden uitgevoerd omdat er in het subnet extra adressen zijn vereist voor het schalen van het exemplaar.

**Voor beeld 3**: u bent van plan één Managed instance voor algemeen gebruik (GEN5-hardware) te hebben en van tijd tot tijd een vCores-update bewerking uit te voeren. Dit betekent dat u 5 + 6 + 1 * 3 + 3 = 17 IP-adressen nodig hebt. Als IP-bereiken worden gedefinieerd in de macht van 2, hebt u het IP-bereik van 32 (2 ^ 5) IP-adressen nodig. Daarom moet u het subnet reserveren met subnetmasker/27.

### <a name="address-requirements-for-update-scenarios"></a>Adres vereisten voor update scenario's

Bij het schalen van bewerkings instanties is er tijdelijk extra IP-capaciteit vereist die afhankelijk is van de prijs categorie en het genereren van hardware

| **Hardware-gen** | **Prijscategorie** | **Scenario** | **Aanvullende adressen*** |
| --- | --- | --- | --- |
| Gen4 | GP of BC | VCores schalen | 5 |
| Gen4 | GP of BC | Opslag ruimte schalen | 5 |
| Gen4 | GP of BC | Overschakelen van GP naar BC of BC naar GP | 5 |
| Gen4 | GP | Overschakelen naar GEN5 * | 9 |
| Gen4 | BC | Overschakelen naar GEN5 * | 11 |
| GEN5 | GP | VCores schalen | 3 |
| GEN5 | GP | Opslag ruimte schalen | 0 |
| GEN5 | GP | Overschakelen naar BC | 5 |
| GEN5 | BC | VCores schalen | 5 |
| GEN5 | BC | Opslag ruimte schalen | 5 |
| GEN5 | BC | Overschakelen naar GP | 3 |

  \* Gen4-hardware wordt gefaseerd uitgevoerd en is niet meer beschikbaar voor nieuwe implementaties. Werk de hardware-generatie van Gen4 naar GEN5 om te profiteren van de mogelijkheden die specifiek zijn voor het genereren van GEN5-hardware.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over de [connectiviteits architectuur voor een door SQL beheerd exemplaar](connectivity-architecture-overview.md).
- Zie hoe [u een VNet maakt waarin u een door SQL beheerd exemplaar gaat implementeren](virtual-network-subnet-create-arm-template.md).
- Zie [Configure a Custom DNS](custom-dns-configure.md)(Engelstalig) voor DNS-problemen.

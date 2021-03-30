---
title: Scenario's voor privé zones-Azure DNS
description: In dit artikel vindt u informatie over algemene scenario's voor het gebruik van Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76939346"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenario's met persoonlijke zones Azure DNS

Azure DNS Private Zones naam omzetting bieden binnen een virtueel netwerk en tussen virtuele netwerken. In dit artikel kijken we naar enkele veelvoorkomende scenario's die kunnen worden gerealiseerd met behulp van deze functie.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: naam omzetting binnen het bereik van één virtueel netwerk
In dit scenario hebt u een virtueel netwerk in azure met een aantal Azure-resources, inclusief virtual machines (Vm's). U de resources wilt omzetten vanuit het virtuele netwerk via een specifieke domein naam (DNS-zone) en u wilt dat de naam omzetting privé is en niet toegankelijk is via internet. Voor de virtuele machines binnen het VNET hebt u bovendien Azure nodig om deze automatisch te registreren bij de DNS-zone. 

Dit scenario wordt hieronder weer gegeven. Virtual Network met de naam ' A ' bevat twee virtuele machines (VNETA-VM1 en VNETA-VM2). Aan elk van deze zijn privé Ip's gekoppeld. Wanneer u een persoonlijke zone met de naam contoso.com maakt en dit virtuele netwerk koppelt als een virtuele registratie, worden er in Azure DNS automatisch twee records gemaakt in de zone. Nu kunnen DNS-query's van VNETA-VM1 om VNETA-VM2.contoso.com op te lossen, een DNS-antwoord ontvangen dat de privé-IP van VNETA-VM2 bevat. Daarnaast ontvangt een reverse DNS-query (PTR) voor het privé IP-adres van VNETA-VM1 (10.0.0.1) dat is uitgegeven door VNETA-VM2 een DNS-antwoord dat de naam VNETA-VM1 bevat, zoals verwacht. 

![Oplossing voor één virtueel netwerk](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: naam omzetting in virtuele netwerken

Dit scenario is het meest voorkomende geval wanneer u een persoonlijke zone wilt koppelen aan meerdere virtuele netwerken. Dit scenario kan architecturen hebben, zoals het hub-en-spoke-model, waarbij er sprake is van een centraal hub-netwerk waarmee meerdere andere spoke-virtuele netwerken zijn verbonden. Het virtuele netwerk van de centrale hub kan worden gekoppeld als het virtuele netwerk van de registratie aan een privé zone en de spoke virtuele netwerken kunnen worden gekoppeld als virtuele omzettings netwerken. 

In het volgende diagram ziet u een eenvoudige versie van dit scenario met slechts twee virtuele netwerken: A en B. A wordt aangewezen als een virtuele registratie-netwerk en B wordt aangeduid als een virtueel omzettings netwerk. De bedoeling is voor beide virtuele netwerken om een gemeen schappelijke zone contoso.com te delen. Wanneer de zone wordt gemaakt en de virtuele netwerken voor omzetting en registratie zijn gekoppeld aan de zone, registreert Azure automatisch DNS-records voor de Vm's (VNETA-VM1 en VNETA-VM2) van het virtuele netwerk A. U kunt ook hand matig DNS-records toevoegen aan de zone voor Vm's in het virtuele netwerk oplossing B. Met deze instelling ziet u het volgende gedrag voor het door sturen en omkeren van DNS-query's:
* Een DNS-query van VNETB-VM1 in het virtuele netwerk van de resolutie voor VNETA-VM1.contoso.com, ontvangt een DNS-antwoord met het privé-IP-adres VNETA-VM1.
* Een reverse DNS-query (PTR) van VNETB-VM2 in het virtuele netwerk met de resolutie B voor 10.1.0.1, ontvangt een DNS-antwoord met de FQDN-VNETB-VM1.contoso.com.  
* Een reverse DNS-query (PTR) van VNETB-VM3 in het virtuele netwerk van de resolutie voor 10.0.0.1 wordt NXDOMAIN ontvangen. De reden hiervoor is dat omgekeerde DNS-query's alleen binnen het bereik van hetzelfde virtuele netwerk vallen. 


![Meerdere virtuele netwerk resoluties](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Split-Horizon functionaliteit

In dit scenario hebt u een use-case waarbij u verschillende DNS-omzettings gedrag wilt realiseren, afhankelijk van waar de client zich bevindt (binnen Azure of op Internet), voor dezelfde DNS-zone. U hebt bijvoorbeeld een persoonlijke en open bare versie van uw toepassing met verschillende functionaliteit of gedrag, maar u wilt dezelfde domein naam gebruiken voor beide versies. Dit scenario kan worden gerealiseerd met Azure DNS door een open bare DNS-zone en een persoonlijke zone te maken, met dezelfde naam.

In het volgende diagram ziet u dit scenario. U hebt een virtueel netwerk A met twee virtuele machines (VNETA-VM1 en VNETA-VM2) waarvoor zowel privé Ip's als open bare IP-adressen zijn toegewezen. U maakt een open bare DNS-zone met de naam contoso.com en registreert de open bare Ip's voor deze virtuele machines als DNS-records binnen de zone. U maakt ook een Privé-DNS zone met de naam contoso.com die een als het virtuele netwerk voor registratie opgeeft. Azure registreert de Vm's automatisch als een record in de privé zone en wijst naar hun persoonlijke Ip's.

Wanneer een Internet-client nu een DNS-query voor het opzoeken van VNETA-VM1.contoso.com uitgeeft, retourneert Azure de open bare IP-record uit de open bare zone. Als dezelfde DNS-query wordt uitgegeven van een andere VM (bijvoorbeeld: VNETA-VM2) in hetzelfde virtuele netwerk A, wordt de privé-IP-record vanuit de privé zone door Azure geretourneerd. 

![Brian-resolutie splitsen](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over privé-DNS-zones raadpleegt u [Using Azure DNS for private domains](private-dns-overview.md) (Azure DNS gebruiken voor privédomeinen).

Meer informatie over het [maken van een privé-DNS-zone](./private-dns-getstarted-powershell.md) in azure DNS.

Ga voor meer informatie over DNS-zones en-records naar: [DNS-zones en records-overzicht](dns-zones-records.md).

Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.


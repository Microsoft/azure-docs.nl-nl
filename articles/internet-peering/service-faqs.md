---
title: Peering-Service-Veelgestelde vragen
titleSuffix: Azure
description: Peering-Service-Veelgestelde vragen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775470"
---
# <a name="peering-service---faqs"></a>Peering-Service-Veelgestelde vragen

Hieronder vindt u een overzicht van de informatie over algemene vragen.

**Kan een provider met behulp van hun bestaande rechtstreekse peering met micro soft ondersteuning bieden voor de peering-service?**

Ja, een transporteur kan gebruikmaken van de bestaande PNI ter ondersteuning van de peering-service. Het PNI van een peering service vereist diversiteit in de ondersteuning van HA. Als bestaande PNI al diversiteit heeft, is er geen nieuwe infra structuur vereist. Als de bestaande PNI diversiteit nodig heeft, kan deze worden uitgebreid.

**Kan een provider nieuwe directe peering met micro soft gebruiken ter ondersteuning van de peering-service?**

Ja, dat is ook mogelijk. Micro soft werkt met de provider om nieuwe directe peering te maken ter ondersteuning van de peering-service.  

**Waarom is directe peering een vereiste ter ondersteuning van de peering-service?**

Een van de primaire Stuur Programma's achter de peering-service is het bieden van connectiviteit met micro soft onlineservices via een goed verbonden SP. PNI zijn altijd in Gbps assortiment en daarom een fundamenteel bouw blok voor connectiviteit met hoge door Voer tussen de transporteur en micro soft.

**Wat zijn de diversiteits vereisten op directe peering voor de ondersteuning van de peering-service?**

Een PNI moet lokale redundantie en geo-redundantie ondersteunen. Lokale-redundantie wordt gedefinieerd als twee verschillende sets paden in een bepaalde peering-site. Voor geo-redundantie moet de provider extra connectiviteit hebben op een andere micro soft Edge-site, voor het geval de primaire site mislukt. Voor de korte provider voor fout duur kan verkeer worden gerouteerd via de back-upsite.

**De provider biedt al een SLA en Internet op het niveau van de Enter prise-versie, hoe is dit een ander aanbod?**

Sommige vervoerders bieden SLA-en Enter prise-Internet op hun eigen deel van het netwerk. In de peering-service biedt micro soft SLA-aanbod verkeer op micro soft-onderdeel van het netwerk. Als u een peering service-klant selecteert, wordt er end-to-end SLA weer geven. SLA van hun site naar micro soft Edge op ISP-netwerk kan worden gedekt door de Internet provider. SLA in het wereld wijde netwerk van micro soft Edge naar eind gebruikers wordt nu gedekt door micro soft.

**Als een service provider al is gekoppeld aan micro soft met behulp van PNI, welke soort wijzigingen zijn vereist voor de ondersteuning van de peering-service?**

* Software wijzigingen om een peering service gebruiker en het bijbehorende verkeer te identificeren. Het is mogelijk dat routerings beleidsregels moeten worden gewijzigd om het verkeer van een gebruiker op de dichtstbijzijnde micro soft Edge via de peering-service verbinding te wisselen.
* Zorg ervoor dat de connectiviteit lokale-redundantie en geo-redundantie heeft.

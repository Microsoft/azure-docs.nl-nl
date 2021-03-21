---
title: Internet peering-Veelgestelde vragen
titleSuffix: Azure
description: Internet peering-Veelgestelde vragen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775496"
---
# <a name="internet-peering---faqs"></a>Internet peering-Veelgestelde vragen

Hieronder vindt u een overzicht van de informatie over algemene vragen.

**Wat is het verschil tussen Internet peering en peering service?**

Peering-service is een service die de open bare IP-connectiviteit van een onderneming aan micro soft biedt voor haar zakelijke klanten. Het Internet van een onderneming bevat connectiviteit via Isp's met hoge doorvoer connectiviteit voor micro soft en redundantie voor een HA-verbinding. Daarnaast wordt gebruikers verkeer geoptimaliseerd voor latentie bij de dichtstbijzijnde micro soft Edge. Peering-service bouwt voort op peering-connectiviteit met partner carrier. De peering-verbinding met de partner moet direct peering zijn, in tegens telling tot Exchange-peering. Directe peering moet lokale en geo-redundantie hebben.

**Wat is verouderde peering?**

Peering-verbinding die is ingesteld met behulp van Azure PowerShell, wordt beheerd als een Azure-resource. Peering-verbindingen die in het verleden zijn ingesteld, worden in ons systeem opgeslagen als verouderde peering, die u eventueel kunt converteren om te beheren als een Azure-resource.

**Welke IP-adressen worden er aan micro soft-en peer-apparaten gegeven wanneer New-AzPeeringDirectConnectionObject wordt aangeroepen?**

Bij het aanroepen van New-AzPeeringDirectConnectionObject cmdlet, wordt een/31-adres (a. b. c. d/31) of een/30-adres (a. b. c. d/30) ingevoerd. Het eerste IP-adres (a. b. c. d + 0) wordt gegeven aan het apparaat van de peer en het tweede IP-adres (a. b. c. d + 1) wordt gegeven aan micro soft-apparaat.

**Wat zijn MaxPrefixesAdvertisedIPv4-en MaxPrefixesAdvertisedIPv6-para meters in New-AzPeeringDirectConnectionObject-cmdlet?**

De para meters MaxPrefixesAdvertisedIPv4 en MaxPrefixesAdvertisedIPv6 vertegenwoordigen het maximum aantal IPv4-en IPv6-voor voegsels dat door micro soft wordt geaccepteerd. Deze para meters kunnen op elk gewenst moment worden gewijzigd.
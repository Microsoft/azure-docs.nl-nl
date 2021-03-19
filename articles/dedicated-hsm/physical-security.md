---
title: Fysieke HSM-beveiliging-Azure dedicated HSM | Microsoft Docs
description: Informatie over de fysieke beveiliging van de toegewezen HSM-apparaten van Azure in data centers
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70881026"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Exclusieve fysieke beveiliging van de HSM van Azure

Met de speciale HSM van Azure kunt u voldoen aan de geavanceerde beveiligings vereisten voor sleutel opslag. De IT-afdeling kan de volgende strikte beveiligings procedures gedurende de volledige levens duur worden beheerd om te voldoen aan de behoeften van klanten.

## <a name="security-through-procurement"></a>Beveiliging via aanschaf

Micro soft volgt een veilig aankoop proces. We beheren de keten van de bewaring en zorgen ervoor dat het apparaat dat wordt geleverd bij onze data centers, het specifieke apparaat besteld en verzonden is. De apparaten bevinden zich in een back-up van onrecht matige gebeurtenis plastic. Ze worden opgeslagen in een beveiligde opslag ruimte totdat ze in de data galerie van het Data Center worden uitgevoerd.  De racks met de HSM-apparaten worden beschouwd als een hoge impact op het bedrijf (HBI). De apparaten zijn op alle tijden van de voor-en achterkant vergrendeld en onder video toezicht.

## <a name="security-through-deployment"></a>Beveiliging via implementatie

Hsm's worden in racks samen met gekoppelde netwerk onderdelen geïnstalleerd. Na de installatie moeten ze worden geconfigureerd voordat ze beschikbaar worden gemaakt als onderdeel van de toegewezen HSM-service van Azure. Deze configuratie activiteit wordt uitgevoerd door werk nemers van micro soft die een achtergrond controle hebben ondergaan. Een just-in-time-beheer (JIT) wordt gebruikt om de toegang tot alleen de juiste werk nemers te beperken en alleen voor de tijd die nodig is om toegang te krijgen. De procedures en systemen die worden gebruikt, zorgen er ook voor dat alle activiteiten met betrekking tot de HSM-apparaten worden geregistreerd.

## <a name="security-in-operations"></a>Beveiliging in bewerkingen

Hsm's zijn hardware-apparaten (de werkelijke HSM is een PCI-kaart in het apparaat), zodat er problemen kunnen ontstaan met het onderdeel niveau. Mogelijke problemen zijn onder andere, maar zijn niet beperkt tot ventilatoren en stroom toestanden. Voor dit type gebeurtenis moet onderhoud worden ondergaan of moeten activiteiten worden verholpen om eventuele wissel bare onderdelen te vervangen.

### <a name="component-replacement"></a>Onderdeel vervanging

Nadat een apparaat is ingericht en onder het beheer van klanten staat, is de hot-swappable voeding de enige onderdelen die zouden worden vervangen. Dit onderdeel bevindt zich buiten de beveiligings grens en veroorzaakt geen knoei gebeurtenis. Een ticket systeem wordt gebruikt voor het autoriseren van een micro soft-engineer om toegang te krijgen tot de achterkant van het HBI-rek. Wanneer het ticket wordt verwerkt, wordt er een tijdelijke fysieke sleutel uitgegeven. Met deze sleutel krijgt de engineer toegang tot het apparaat en kunnen ze het betrokken onderdeel vervangen. Alle andere toegang (dat wil zeggen dat de gebeurtenis onrecht matig wordt veroorzaakt) zou worden uitgevoerd wanneer een apparaat niet aan een klant wordt toegewezen, waardoor het risico op beveiliging en beschik baarheid wordt geminimaliseerd.  

### <a name="device-replacement"></a>Vervanging van het apparaat

In het geval van een totale apparaatfout, wordt een proces gevolgd dat vergelijkbaar is met de fout die tijdens het mislukken van een onderdeel werd gebruikt. Als een klant het apparaat niet kan zeroize of als het apparaat een onbekende status heeft, worden de apparaten van de gegevens drager verwijderd en in een in-rek-vernietigings bin geplaatst. Apparaten die in de bin worden geplaatst, worden op een gecontroleerde en veilige manier vernietigd. Gegevens die geen apparaten van een HBI-rek hebben, hebben een micro soft-Data Center.

### <a name="other-rack-access-activities"></a>Andere Rack toegangs activiteiten

Als een micro soft-Engineer toegang moet hebben tot het rek dat door HSM-apparaten wordt gebruikt (bijvoorbeeld onderhoud van het netwerk apparaat), worden standaard beveiligings procedures gebruikt om toegang te krijgen tot het beveiligde HBI-rack. Alle toegang wordt onder video bewaking weer geven. De HSM-apparaten worden gevalideerd tot [FIPS 140-2 level 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) , zodat ongeoorloofde toegang tot de HSM-apparaten wordt gesignaleerd aan de klant en de gegevens worden nul.

## <a name="logical-level-security-considerations"></a>Beveiligings overwegingen voor logisch niveau

Hsm's worden ingericht in een virtueel netwerk dat door de klant is gemaakt. Dit is de persoonlijke IUP-adres ruimte van de klant.  Deze configuratie biedt een waardevolle isolatie op logisch netwerk niveau en garandeert alleen toegang door de klant. Dit betekent dat alle beveiligings controles op logisch niveau de verantwoordelijkheid van de klant zijn.

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen om alle belang rijke concepten van de service, zoals hoge Beschik baarheid en beveiliging en ondersteuning, bijvoorbeeld goed te begrijpen vóór het inrichten van apparaten, het ontwerpen of implementeren van toepassingen.

* [Hoge beschikbaarheid](high-availability.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
* [Implementatie architectuur](deployment-architecture.md)
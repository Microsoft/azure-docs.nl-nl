---
title: Wat is er nieuw in azure Defender voor IoT
description: In dit artikel kunt u zien wat er nieuw is in de meest recente versie van Defender voor IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: shhazam
ms.openlocfilehash: a8f4b96b27eb09443c2644fd63a8783faaa610e4
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809504"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Wat is er nieuw in azure Defender voor IoT?

In dit artikel vindt u een overzicht van nieuwe functies en functie verbeteringen voor Defender voor IoT.

Genoteerde functies zijn een PREVIEW-versie. De [Aanvullende voorwaarden voor Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.
## <a name="february-2021"></a>Februari 2021

### <a name="enhanced-custom-alert-rules"></a>Uitgebreide aangepaste waarschuwings regels

U kunt nu aangepaste waarschuwings regels maken op basis van de dag, groep dagen en tijds periode netwerk activiteit is gedetecteerd.  Het werken met de regels voor de dag-en tijd regel is nuttig, bijvoorbeeld in gevallen waarbij de ernst van de waarschuwing wordt bepaald door het tijdstip waarop de waarschuwings gebeurtenis plaatsvindt. Maak bijvoorbeeld een aangepaste regel waarmee een waarschuwing met hoge ernst wordt geactiveerd wanneer netwerk activiteit wordt gedetecteerd in een weekend of 's avonds.

Deze functie is beschikbaar op de sensor met de release van versie 10,1.

### <a name="export-alerts-from-on-premises-management-console"></a>Waarschuwingen exporteren vanuit een on-premises beheer console

Waarschuwings informatie kan nu worden geëxporteerd naar een CSV-bestand vanuit de on-premises beheer console. U kunt informatie over alle gedetecteerde waarschuwingen exporteren of informatie exporteren op basis van de gefilterde weer gave.

Deze functie is beschikbaar op de on-premises beheer console met de release van versie 10,1.
### <a name="device-builder---new-micro-agent-public-preview"></a>Apparaat Builder-nieuwe micro agent (open bare preview)

Er is een nieuwe Device Builder-module beschikbaar. De module, die een micro agent wordt genoemd, biedt de volgende voor bereidingen:

- **Integratie met azure IOT hub en Azure Defender voor IOT** : bouw sterker betere eindpunt beveiliging in uw IOT-apparaten door de service te integreren met de bewakings optie van zowel Azure IOT hub als Azure Defender voor IOT.
- **Flexibele implementatie opties met ondersteuning voor standaard IOT-besturings systemen** : kunnen worden geïmplementeerd als een binair pakket of als aanpas bare bron code, met ondersteuning voor standaard IOT-besturings systemen zoals Linux en Azure rto's.
- **Minimale resource vereisten zonder afhankelijkheden van de kernel van het besturings systeem** , weinig CPU-verbruik en geen afhankelijkheden van de kernel van het besturings systeem.
- **Security postuur Management** : de beveiligings-postuur van uw IOT-apparaten proactief controleren.
- **Continue, realtime IOT/a-bedreigings detectie** : Detecteer bedreigingen zoals botnets, beveiligings pogingen, crypto-Miners en verdachte netwerk activiteit

De afgeschafte documentatie over de beveiligings module wordt verplaatst naar de map klassiek.

Deze functieset is beschikbaar in de huidige open bare preview-Cloud versie.

## <a name="january-2021"></a>Januari 2021

- [Beveiliging](#security)
- [Onboarding](#onboarding)
- [Betrouwbaarheid](#usability)
- [Andere Updates](#other-updates)
### <a name="security"></a>Beveiliging

Er zijn verbeteringen aangebracht in het certificaat en wacht woord herstellen voor deze versie.

#### <a name="certificates"></a>Certificaten
  
Met deze versie kunt u het volgende doen:

- Upload SSL-certificaten rechtstreeks naar de Sens oren en on-premises beheer consoles.
- Validatie uitvoeren tussen de on-premises beheer console en verbonden Sens oren en tussen een beheer console en een beheer console met hoge Beschik baarheid. Validatie is gebaseerd op de verval datum, de authenticiteit van de basis certificerings instantie en de intrekkings lijsten voor certificaten.  Als de validatie mislukt, wordt de sessie niet voortgezet.

Voor upgrades:

- Er is geen wijziging in het SSL-certificaat of de validatie functionaliteit tijdens de upgrade.
- Na de upgrade kunnen gebruikers met beheerders rechten voor de sensor en on-premises beheer console SSL-certificaten vervangen of SSL-certificaat validatie activeren vanuit het venster systeem instellingen, het SSL-certificaat.  

Voor nieuwe installaties:

- Tijdens de eerste keer aanmelden moeten gebruikers een SSL-certificaat (aanbevolen) of een lokaal gegenereerd zelfondertekend certificaat gebruiken (niet aanbevolen)
- Certificaat validatie is standaard ingeschakeld voor nieuwe installaties.

#### <a name="password-recovery"></a>Wachtwoord herstel
  
Gebruikers met beheerders rechten voor de sensor en on-premises beheer console kunnen nu wacht woorden herstellen vanuit de Azure Defender voor IoT-Portal. Wacht woord is eerder vereist voor de interventie van het ondersteunings team.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>On-premises beheer console-toegewezen apparaten

De volgende keer dat u zich aanmeldt bij de on-premises beheer console, zijn gebruikers nu verplicht een activerings bestand te uploaden. Het bestand bevat het cumulatieve aantal apparaten dat op het netwerk van de organisatie moet worden bewaakt. Dit aantal wordt aangeduid als het aantal toegewezen apparaten.
Toegewezen apparaten worden gedefinieerd tijdens het voorbereidings proces in de Azure Defender voor IoT-Portal, waar het activerings bestand wordt gegenereerd.
De eerste keer dat gebruikers en gebruikers worden bijgewerkt, moeten ze het activerings bestand uploaden.
Na de eerste activering kan het aantal apparaten dat op het netwerk is gedetecteerd het aantal toegewezen apparaten overschrijden. Deze gebeurtenis kan bijvoorbeeld optreden als u meer Sens oren verbindt met de beheer console. Als er een verschil is tussen het aantal gedetecteerde apparaten en het aantal toegewezen apparaten, wordt er een waarschuwing weer gegeven in de beheer console. Als deze gebeurtenis zich voordoet, moet u een nieuw activerings bestand uploaden.

#### <a name="pricing-page-options"></a>Opties voor prijs pagina

Met de pagina met prijzen kunt u nieuwe abonnementen op Azure Defender voor IoT vrijgeven en vastgelegde apparaten in uw netwerk definiëren.  
Daarnaast kunt u met de pagina met prijzen nu bestaande abonnementen beheren die zijn gekoppeld aan een sensor en de toezeg ging van een apparaat bijwerken.

#### <a name="view-and-manage-onboarded-sensors"></a>Onboarded Sens oren weer geven en beheren

Met een nieuwe site-en Sens oren-portal pagina kunt u:

- Beschrijvende informatie over de sensor toevoegen. Bijvoorbeeld een zone die is gekoppeld aan de sensor of de tags voor vrije tekst.
- Sensor gegevens weer geven en filteren. Bekijk bijvoorbeeld details over Sens oren die zijn verbonden met de Cloud of lokaal beheerd of Bekijk informatie over Sens oren in een specifieke zone.  

### <a name="usability"></a>Bruikbaarheid

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel New connector-pagina

De pagina Azure Defender voor IoT Data Connector in azure Sentinel is opnieuw ontworpen. De gegevens connector is nu gebaseerd op abonnementen in plaats van IoT-hubs. klanten toestaan hun configuratie verbinding beter te beheren met Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal machtigings updates  

Er is ondersteuning toegevoegd voor de beveiligings lezer en beveiligings beheerder.

### <a name="other-updates"></a>Andere Updates

#### <a name="access-group---zone-permissions"></a>Toegang tot groeps zone machtigingen
  
De regels van de on-premises beheer console-toegang bevatten niet de optie om toegang tot een specifieke zone te verlenen. Er zijn geen wijzigingen in het definiëren van regels die gebruikmaken van sites, regio's en bedrijfs eenheden.   Na de upgrade worden toegangs groepen met regels die toegang tot bepaalde zones toestaan, gewijzigd om toegang tot de bovenliggende site, inclusief alle zones, toe te staan.

#### <a name="terminology-changes"></a>Terminologiewijzigingen

De term Asset heeft de naam apparaat gewijzigd in de sensor en on-premises beheer console, rapporten en andere oplossings interfaces.
In de sensor-en on-premises beheer console waarschuwingen is de term beheer voor deze gebeurtenis herstels tappen genoemd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met Defender voor IoT](getting-started.md)

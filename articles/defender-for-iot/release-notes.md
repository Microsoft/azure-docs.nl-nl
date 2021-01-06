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
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937635"
---
# <a name="whats-new"></a>Nieuwe functies

Defender voor IoT 10,0 biedt functie verbeteringen waarmee de beveiliging, het beheer en de bruikbaarheid worden verbeterd.

## <a name="security"></a>Beveiliging

Er zijn verbeteringen aangebracht in het certificaat en wacht woord herstellen voor deze versie.

### <a name="certificates"></a>Certificaten
  
Met deze versie kunt u het volgende doen:

- Upload SSL-certificaten rechtstreeks naar de Sens oren en on-premises beheer consoles.
- Validatie uitvoeren tussen de on-premises beheer console en verbonden Sens oren en tussen een beheer console en een beheer console met hoge Beschik baarheid. Validatie is gebaseerd op de verval datum, de authenticiteit van de basis certificerings instantie en de intrekkings lijsten voor certificaten.  Als de validatie mislukt, wordt de sessie niet voortgezet.

Voor upgrades:

- Er is geen wijziging in het SSL-certificaat of de validatie functionaliteit tijdens de upgrade.
- Na de upgrade kunnen gebruikers met beheerders rechten voor de sensor en on-premises beheer console SSL-certificaten vervangen of SSL-certificaat validatie activeren vanuit het venster systeem instellingen, het SSL-certificaat.  

Voor nieuwe installaties:

- Tijdens de eerste keer aanmelden moeten gebruikers een SSL-certificaat (aanbevolen) of een lokaal gegenereerd zelfondertekend certificaat gebruiken (niet aanbevolen)
- Certificaat validatie is standaard ingeschakeld voor nieuwe installaties.

### <a name="password-recovery"></a>Wachtwoord herstel
  
Gebruikers met beheerders rechten voor de sensor en on-premises beheer console kunnen nu wacht woorden herstellen vanuit de Azure Defender voor IoT-Portal. Wacht woord is eerder vereist voor de interventie van het ondersteunings team.

## <a name="onboarding"></a>Onboarding

### <a name="on-premises-management-console---committed-devices"></a>On-premises beheer console-toegewezen apparaten

De volgende keer dat u zich aanmeldt bij de on-premises beheer console, zijn gebruikers nu verplicht een activerings bestand te uploaden. Het bestand bevat het cumulatieve aantal apparaten dat op het netwerk van de organisatie moet worden bewaakt. Dit aantal wordt aangeduid als het aantal toegewezen apparaten.
Toegewezen apparaten worden gedefinieerd tijdens het voorbereidings proces in de Azure Defender voor IoT-Portal, waar het activerings bestand wordt gegenereerd.
De eerste keer dat gebruikers en gebruikers worden bijgewerkt, moeten ze het activerings bestand uploaden.
Na de eerste activering kan het aantal apparaten dat op het netwerk is gedetecteerd het aantal toegewezen apparaten overschrijden. Deze gebeurtenis kan bijvoorbeeld optreden als u meer Sens oren verbindt met de beheer console. Als er een verschil is tussen het aantal gedetecteerde apparaten en het aantal toegewezen apparaten, wordt er een waarschuwing weer gegeven in de beheer console. Als deze gebeurtenis zich voordoet, moet u een nieuw activerings bestand uploaden.

### <a name="pricing-page-options"></a>Opties voor prijs pagina

Met de pagina met prijzen kunt u nieuwe abonnementen op Azure Defender voor IoT vrijgeven en vastgelegde apparaten in uw netwerk definiëren.  
Daarnaast kunt u met de pagina met prijzen nu bestaande abonnementen beheren die zijn gekoppeld aan een sensor en de toezeg ging van een apparaat bijwerken.

### <a name="view-and-manage-onboarded-sensors"></a>Onboarded Sens oren weer geven en beheren

Met een nieuwe site-en Sens oren-portal pagina kunt u:

- Beschrijvende informatie over de sensor toevoegen. Bijvoorbeeld een zone die is gekoppeld aan de sensor of de tags voor vrije tekst.
- Sensor gegevens weer geven en filteren. Bekijk bijvoorbeeld details over Sens oren die zijn verbonden met de Cloud of lokaal beheerd of Bekijk informatie over Sens oren in een specifieke zone.  

## <a name="usability"></a>Bruikbaarheid

### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel New connector-pagina

De pagina Azure Defender voor IoT Data Connector in azure Sentinel is opnieuw ontworpen. De gegevens connector is nu gebaseerd op abonnementen in plaats van IoT-hubs. klanten toestaan hun configuratie verbinding beter te beheren met Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>Azure Portal machtigings updates  

Er is ondersteuning toegevoegd voor de beveiligings lezer en beveiligings beheerder.

## <a name="other-updates"></a>Andere Updates

### <a name="access-group---zone-permissions"></a>Toegang tot groeps zone machtigingen
  
De regels van de on-premises beheer console-toegang bevatten niet de optie om toegang tot een specifieke zone te verlenen. Er zijn geen wijzigingen in het definiëren van regels die gebruikmaken van sites, regio's en bedrijfs eenheden.   Na de upgrade worden toegangs groepen met regels die toegang tot bepaalde zones toestaan, gewijzigd om toegang tot de bovenliggende site, inclusief alle zones, toe te staan.

### <a name="terminology-changes"></a>Terminologiewijzigingen

De term Asset heeft het apparaat een andere naam gegeven in de sensor en on-premises beheer console, rapporten en andere oplossings interfaces.
In de sensor-en on-premises beheer console waarschuwingen is de term beheer voor deze gebeurtenis herstels tappen genoemd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met Defender voor IoT](getting-started.md)

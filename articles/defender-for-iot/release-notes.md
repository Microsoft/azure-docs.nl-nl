---
title: Wat is er nieuw in Azure Defender for IoT
description: In dit artikel wordt beschreven wat er nieuw is in de nieuwste versie van Defender for IoT.
ms.topic: overview
ms.date: 04/19/2021
ms.openlocfilehash: da5358ccf0f69ca2ba8f5722b75889b6b7c92c07
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752602"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Wat is er nieuw in Azure Defender for IoT?

In dit artikel vindt u nieuwe functies en functieverbeteringen voor Defender for IoT.

Genoteerde functies zijn beschikbaar in preview. De [Aanvullende voorwaarden voor Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

## <a name="april-2021"></a>April 2021

### <a name="work-with-automatic-threat-intelligence-updates-public-preview"></a>Werken met updates voor automatische bedreigingsinformatie (openbare preview)

Nieuwe bedreigingsinformatiepakketten kunnen nu automatisch worden pushen naar sensoren die zijn verbonden met de cloud wanneer ze worden vrijgegeven door Microsoft Defender for IoT. Dit is een aanvulling op het downloaden van bedreigingsinformatiepakketten en deze vervolgens uploaden naar sensoren.

Het werken met automatische updates vermindert de operationele inspanningen en zorgt voor een betere beveiliging. Schakel automatisch bijwerken in door de met de cloud verbonden  sensor in de Defender for IoT-portal te onboarden met de schakelknop Automatische updates voor bedreigingsinformatie ingeschakeld.

Als u uw bedreigingsinformatiegegevens voorzichtiger wilt bijwerken, kunt u pakketten handmatig vanuit de Azure Defender for IoT-portal pushen naar sensoren die zijn verbonden met de cloud wanneer dat nodig is.
Dit biedt u de mogelijkheid om te bepalen wanneer een pakket wordt geïnstalleerd, zonder dat u het hoeft te downloaden en vervolgens te uploaden naar uw sensoren. Push updates handmatig naar sensoren vanaf de pagina Sites en **sensoren** van Defender for IoT.

U kunt ook de volgende informatie over bedreigingsinformatiepakketten bekijken:

- Geïnstalleerde pakketversie
- Updatemodus bedreigingsinformatie 
- Updatestatus van bedreigingsinformatie

### <a name="view-cloud-connected-sensor-information-public-preview"></a>Sensorinformatie over verbonden cloud weergeven (openbare preview)

Bekijk belangrijke operationele informatie over sensoren die zijn verbonden met de cloud op **de pagina Sites en sensoren.**

- De geïnstalleerde sensorversie
- De verbindingsstatus van de sensor naar de cloud.
- De laatste keer dat de sensor verbinding met de cloud heeft gedetecteerd.

### <a name="alert-api-enhancements"></a>Verbeteringen van waarschuwings-API

Er zijn nieuwe velden beschikbaar voor gebruikers die werken met waarschuwings-API's.

**On-premises beheerconsole**

- Bron- en doeladres
- Herstelstappen
- De naam van de sensor die door de gebruiker is gedefinieerd
- De naam van de zone die is gekoppeld aan de sensor 
- De naam van de site die is gekoppeld aan de sensor

**Sensor**

- Bron- en doeladres
- Herstelstappen

API-versie 2 is vereist bij het werken met de nieuwe velden.

### <a name="features-delivered-as-generally-available-ga"></a>Functies geleverd als algemeen beschikbaar (GA)

De volgende functies waren voorheen beschikbaar voor openbare preview en zijn nu algemeen beschikbaar(GA)-functies:

- Sensor - verbeterde aangepaste waarschuwingsregels
- On-premises beheerconsole - waarschuwingen exporteren
- Tweede netwerkinterface toevoegen aan on-premises beheerconsole
- Apparaatbouwer - nieuwe microagent

## <a name="march-2021"></a>Maart 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Sensor : verbeterde aangepaste waarschuwingsregels (openbare preview)

U kunt nu aangepaste waarschuwingsregels maken op basis van de dag, de groep dagen en de tijdsperiode waarin netwerkactiviteit is gedetecteerd.  Werken met regelvoorwaarden voor dag en tijd is handig, bijvoorbeeld in gevallen waarin de ernst van de waarschuwing wordt afgeleid door het tijdstip waarop de waarschuwingsgebeurtenis plaatsvindt. Maak bijvoorbeeld een aangepaste regel die een waarschuwing met hoge urgentie activeert wanneer netwerkactiviteit wordt gedetecteerd in een weekend of 's avonds.

Deze functie is beschikbaar op de sensor met versie 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>On-premises beheerconsole: waarschuwingen exporteren (openbare preview)

Waarschuwingsinformatie kan nu vanuit de on-premises beheerconsole worden geëxporteerd naar een CSV-bestand. U kunt gegevens van alle gedetecteerde waarschuwingen exporteren of gegevens exporteren op basis van de gefilterde weergave.

Deze functie is beschikbaar op de on-premises beheerconsole met versie 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Tweede netwerkinterface toevoegen aan on-premises beheerconsole (openbare preview)

U kunt nu de beveiliging van uw implementatie verbeteren door een tweede netwerkinterface toe te voegen aan uw on-premises beheerconsole. Met deze functie kunnen de sensoren van uw on-premises beheer zijn verbonden op één beveiligd netwerk, terwijl uw gebruikers toegang hebben tot de on-premises beheerconsole via een tweede afzonderlijke netwerkinterface.

Deze functie is beschikbaar op de on-premises beheerconsole met versie 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Tweede netwerkinterface toevoegen aan on-premises beheerconsole (openbare preview)

U kunt nu de beveiliging van uw implementatie verbeteren door een tweede netwerkinterface toe te voegen aan uw on-premises beheerconsole. Met deze functie kunnen de sensoren van uw on-premises beheer zijn verbonden op één beveiligd netwerk, terwijl uw gebruikers toegang hebben tot de on-premises beheerconsole via een tweede afzonderlijke netwerkinterface.

Deze functie is beschikbaar op de on-premises beheerconsole met versie 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Apparaatbouwer - nieuwe microagent (openbare preview)

Er is een nieuwe apparaatbouwermodule beschikbaar. Met de module, ook wel een microagent genoemd, kunt u het volgende doen:

- **Integratie met Azure IoT Hub** en Azure Defender for IoT: bouw sterkere eindpuntbeveiliging rechtstreeks in uw IoT-apparaten door deze te integreren met de bewakingsoptie van zowel de Azure IoT Hub als Azure Defender for IoT.
- Flexibele implementatieopties met ondersteuning voor **standaard-IoT-besturingssystemen:** kunnen worden geïmplementeerd als een binair pakket of als aanpasbare broncode, met ondersteuning voor standaard IoT-besturingssystemen zoals Linux en Azure RTOS.
- **Minimale resourcevereisten zonder kernelafhankelijkheden van het besturingssysteem:** kleine footprint, laag CPU-verbruik en geen afhankelijkheden van besturingssysteemkernel.
- **Beheer van beveiligingsstatus:** controleer proactief de beveiligingsstatus van uw IoT-apparaten.
- **Continue, realtime detectie van IoT-/OT-bedreigingen:** bedreigingen zoals botnets, brute force-pogingen, cryptominers en verdachte netwerkactiviteit detecteren

De afgeschafte documentatie voor Defender-IoT-micro-agent wordt verplaatst naar de op agent gebaseerde oplossing voor apparaatbouwers>*klassieke* map.

Deze functieset is beschikbaar bij de huidige openbare preview-cloudversie.

## <a name="january-2021"></a>Januari 2021

- [Beveiliging](#security)
- [Onboarding](#onboarding)
- [Bruikbaarheid](#usability)
- [Andere Updates](#other-updates)
### <a name="security"></a>Beveiliging

Er zijn verbeteringen aangebracht voor het herstellen van certificaten en wachtwoorden voor deze release.

#### <a name="certificates"></a>Certificaten
  
Met deze versie kunt u:

- Upload SSL-certificaten rechtstreeks naar de sensoren en on-premises beheerconsoles.
- Voer validatie uit tussen de on-premises beheerconsole en verbonden sensoren, en tussen een beheerconsole en een beheerconsole met hoge beschikbaarheid. Validatie is gebaseerd op vervaldatums, echtheid van de basis-CA en certificaat intrekken lijsten.  Als de validatie mislukt, wordt de sessie niet voortgezet.

Voor upgrades:

- Er is geen wijziging in de SSL-certificaat- of validatiefunctionaliteit tijdens de upgrade.
- Na de upgrade kunnen gebruikers met een sensor en on-premises beheerconsole SSL-certificaten vervangen of SSL-certificaatvalidatie activeren vanuit het venster Systeeminstellingen, SSL-certificaat.  

Voor nieuwe installaties:

- Tijdens de eerste keer aanmelden moeten gebruikers een SSL-certificaat gebruiken (aanbevolen) of een lokaal gegenereerd zelf-ondertekend certificaat (niet aanbevolen)
- Certificaatvalidatie is standaard ingeschakeld voor nieuwe installaties.

#### <a name="password-recovery"></a>Wachtwoordherstel
  
Sensor- en on-premises beheerconsoleGebruikers met beheerdersnamen kunnen nu wachtwoorden herstellen vanuit de Azure Defender for IoT portal. Voorheen vereiste wachtwoordherstel interventie van het ondersteuningsteam.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>On-premises beheerconsole - vastgelegde apparaten

Na de eerste aanmelding bij de on-premises beheerconsole moeten gebruikers nu een activeringsbestand uploaden. Het bestand bevat het cumulatief aantal apparaten dat in het organisatienetwerk moet worden bewaakt. Dit aantal wordt het aantal vastgelegde apparaten genoemd.
Vastgelegde apparaten worden gedefinieerd tijdens het onboardingproces op Azure Defender for IoT portal, waar het activeringsbestand wordt gegenereerd.
Gebruikers en gebruikers die voor de eerste keer een upgrade uitvoeren, moeten het activeringsbestand uploaden.
Na de eerste activering kan het aantal apparaten dat in het netwerk is gedetecteerd, het aantal vastgelegde apparaten overschrijden. Deze gebeurtenis kan bijvoorbeeld plaatsvinden als u meer sensoren verbindt met de beheerconsole. Als er een discrepantie is tussen het aantal gedetecteerde apparaten en het aantal vastgelegde apparaten, wordt er een waarschuwing weergegeven in de beheerconsole. Als deze gebeurtenis zich voordoet, moet u een nieuw activeringsbestand uploaden.

#### <a name="pricing-page-options"></a>Opties op de pagina Prijzen

Met de pagina Met prijzen kunt u nieuwe abonnementen onboarden voor Azure Defender for IoT en definieer vastgelegde apparaten in uw netwerk.  
Daarnaast kunt u op de pagina Prijzen nu bestaande abonnementen beheren die zijn gekoppeld aan een sensor en apparaat commitment bijwerken.

#### <a name="view-and-manage-onboarded-sensors"></a>Sensoren met onboarding weergeven en beheren

Met een nieuwe portalpagina site en sensoren kunt u het volgende doen:

- Voeg beschrijvende informatie over de sensor toe. Bijvoorbeeld een zone die is gekoppeld aan de sensor of tags voor vrije tekst.
- Sensorgegevens weergeven en filteren. Bekijk bijvoorbeeld details over sensoren die zijn verbonden met de cloud of lokaal worden beheerd, of bekijk informatie over sensoren in een specifieke zone.  

### <a name="usability"></a>Bruikbaarheid

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel nieuwe connectorpagina

De pagina Azure Defender for IoT gegevensconnector in Azure Sentinel is opnieuw ontworpen. De gegevensconnector is nu gebaseerd op abonnementen in plaats van IoT Hubs; zodat klanten hun configuratieverbinding met de Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal-machtigingen  

Ondersteuning voor beveiligingslezers en beveiligingsbeheerders is toegevoegd.

### <a name="other-updates"></a>Andere Updates

#### <a name="access-group---zone-permissions"></a>Toegangsgroep - zonemachtigingen
  
De toegangsgroepsregels van de on-premises beheerconsole bevatten niet de optie om toegang te verlenen tot een specifieke zone. Het definiëren van regels die gebruikmaken van sites, regio's en bedrijfseenheden verandert niet.   Na de upgrade worden toegangsgroepen met regels voor toegang tot specifieke zones gewijzigd om toegang tot de bovenliggende site toe te staan, inclusief alle zones.

#### <a name="terminology-changes"></a>Terminologiewijzigingen

De naam van het apparaat is gewijzigd in de sensor- en on-premises beheerconsole, rapporten en andere oplossingsinterfaces.
In waarschuwingen in de sensor- en on-premises beheerconsole heeft de term Deze gebeurtenis beheren de naam Herstelstappen.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met Defender for IoT](getting-started.md)

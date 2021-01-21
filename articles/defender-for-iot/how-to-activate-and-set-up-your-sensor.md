---
title: Uw sensor activeren en instellen
description: In dit artikel wordt beschreven hoe u zich aanmeldt en een sensor console activeert.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 908460bd0a034e21524b6ea6d3042f362cc810d4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623586"
---
# <a name="activate-and-set-up-your-sensor"></a>Uw sensor activeren en instellen

In dit artikel wordt beschreven hoe u een sensor activeert en de eerste Setup uitvoert.

Gebruikers met beheerders rechten worden geactiveerd wanneer ze zich voor de eerste keer aanmeldt en wanneer activerings beheer is vereist. Setup zorgt ervoor dat de sensor zo is geconfigureerd dat deze optimaal wordt gedetecteerd en gewaarschuwd.

Beveiligings analisten en alleen-lezen gebruikers kunnen een sensor niet activeren of een nieuw wacht woord genereren.

## <a name="sign-in-and-activation-for-administrator-users"></a>Aanmelden en activeren voor gebruikers met beheerders rechten

Beheerders die zich voor de eerste keer aanmelden, moeten controleren of ze toegang hebben tot de activerings-en wachtwoord herstel bestanden die zijn gedownload tijdens het voorbereiden van de sensor. Als dat niet het geval is, moeten ze beschikken over de machtigingen Azure Security Administrator, mede werker van het abonnement of eigenaar van het abonnement om deze bestanden te genereren in de portal van Azure Defender voor IoT.

### <a name="first-time-sign-in-and-activation-checklist"></a>Controle lijst voor de eerste keer aanmelden en activeren

Voordat gebruikers zich aanmelden bij de sensor console, hebben ze toegang tot:

- Het IP-adres van de sensor dat tijdens de installatie is gedefinieerd.

- Aanmeldings referenties voor de gebruiker voor de sensor. Als u een ISO voor de sensor hebt gedownload, gebruikt u de standaard referenties die u hebt ontvangen tijdens de installatie. U wordt aangeraden een nieuwe *beheerders* gebruiker te maken na activering.

- Een eerste wacht woord. Als u een vooraf geconfigureerde sensor van pijl hebt aangeschaft, moet u een wacht woord genereren wanneer u zich voor de eerste keer aanmeldt.

- Het activerings bestand dat aan deze sensor is gekoppeld. Het bestand is gegenereerd en gedownload tijdens de voor bereiding op de Defender voor IoT-Portal.

- Een door de certificerings instantie ondertekend SSL/TLS-certificaat dat uw bedrijf nodig heeft.

### <a name="about-activation-files"></a>Over activerings bestanden

Uw sensor is in een specifieke beheer modus onboardd naar Azure Defender voor IoT:

| Type modus | Description |
|--|--|
| **Modus verbonden met de Cloud** | Gegevens die door de sensor worden gedetecteerd, worden weer gegeven in de sensor console. Waarschuwings gegevens worden ook geleverd via de IoT-hub en kunnen worden gedeeld met andere Azure-Services, zoals Azure Sentinel. |
| **Lokaal verbonden modus** | Gegevens die door de sensor worden gedetecteerd, worden weer gegeven in de sensor console. Detectie gegevens worden ook gedeeld met de on-premises beheer console als de sensor hieraan is gekoppeld. |

Er is een lokaal verbonden of met de Cloud verbonden activerings bestand gegenereerd en gedownload voor deze sensor tijdens het voorbereiden. Het activerings bestand bevat instructies voor de beheer modus van de sensor. *Er moet een uniek activerings bestand worden geüpload naar elke sensor die u implementeert.*  De eerste keer dat u zich aanmeldt, moet u het relevante activerings bestand voor deze sensor uploaden.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender voor IoT-Portal, onboard sensor.":::

### <a name="about-certificates"></a>Over certificaten

Na installatie van de sensor wordt een lokaal zelfondertekend certificaat gegenereerd en gebruikt om toegang te krijgen tot de sensor console. Wanneer een beheerder zich voor de eerste keer aanmeldt bij de-console, wordt de gebruiker gevraagd een SSL/TLS-certificaat toe te staan.

Er zijn twee beveiligings niveaus beschikbaar:

- Voldoen aan specifieke vereisten voor het certificaat en de versleuteling die door uw organisatie zijn aangevraagd door het door de certificerings instantie ondertekende certificaat te uploaden.
- Validatie toestaan tussen de beheer console en verbonden Sens oren. Validatie wordt geëvalueerd op basis van een certificaatintrekkingslijst en de verval datum van het certificaat. *Als de validatie mislukt, wordt de communicatie tussen de beheer console en de sensor gestopt en wordt er een validatie fout weer gegeven in de console.* Deze optie is standaard ingeschakeld na de installatie.  

De-console ondersteunt de volgende certificaat typen:

- Persoonlijke en bedrijfs sleutel infrastructuur (persoonlijke PKI)

- Open bare-sleutel infrastructuur (open bare PKI)

- Lokaal gegenereerd op het apparaat (lokaal zelf ondertekend) 

  > [!IMPORTANT]
  > U wordt aangeraden niet het standaard zelfondertekende certificaat te gebruiken. Het certificaat is niet beveiligd en moet alleen worden gebruikt voor test omgevingen. De eigenaar van het certificaat kan niet worden gevalideerd en de beveiliging van uw systeem kan niet worden gehandhaafd. Gebruik deze optie nooit voor productie netwerken.

### <a name="sign-in-and-activate-the-sensor"></a>Meld u aan en activeer de sensor

Aanmelden en activeren:

1. Ga vanuit uw browser naar de sensor console met behulp van het IP-adres dat tijdens de installatie is gedefinieerd. Het dialoog venster Aanmelden wordt geopend.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender voor IoT-sensor.":::

1. Voer de referenties in die tijdens de installatie van de sensor zijn gedefinieerd. Als u een vooraf geconfigureerde sensor van pijl hebt aangeschaft, moet u eerst een wacht woord genereren. Voor meer informatie over wachtwoord herstel raadpleegt u [wachtwoord fout onderzoeken bij de eerste aanmelding](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Nadat u zich hebt aangemeld, wordt het dialoog venster **Activering** geopend. Selecteer **uploaden** en ga naar het activerings bestand dat u hebt gedownload tijdens het voorbereiden van de sensor.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Selecteer uploaden en ga naar het activerings bestand.":::

1. Selecteer de configuratie koppeling van het **sensor netwerk** als u de configuratie van het sensor netwerk vóór activering wilt wijzigen. Zie [sensor netwerk configuratie bijwerken vóór activering](#update-sensor-network-configuration-before-activation).

1. Accepteer de voorwaarden.

1. Selecteer **Activate**. Het dialoog venster SSL/TLS-certificaat wordt geopend.

1. Definieer een certificaat naam.
1. Upload de CRT-en Key-bestanden.
1. Voer een wachtwoordzin in en upload indien nodig een PEM-bestand.
1. Selecteer **Next**. Het scherm validatie wordt geopend. Validatie tussen de beheer console en verbonden Sens oren is standaard ingeschakeld.
1. Schakel de schakel optie voor het op het **systeem brede validatie inschakelen** uit om validatie uit te scha kelen. U wordt aangeraden om validatie in te scha kelen.
1. Selecteer **Opslaan**.  

Mogelijk moet u het scherm vernieuwen na het uploaden van het certificaat dat door de certificerings instantie is ondertekend.

Zie voor meer informatie over het uploaden van een nieuw certificaat, ondersteunde certificaat parameters en werken met CLI-certificaat opdrachten [beheren afzonderlijke Sens oren](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Sensor netwerk configuratie bijwerken vóór activering  

De para meters voor de netwerk configuratie van de sensor zijn gedefinieerd tijdens de installatie van de software of wanneer u een vooraf geconfigureerde sensor hebt aangeschaft. De volgende parameters zijn gedefinieerd:

- Het IP-adres
- DNS
- Standaardgateway
- Subnetmasker
- Hostnaam

Mogelijk wilt u deze gegevens bijwerken voordat u de sensor activeert. U moet bijvoorbeeld de vooraf geconfigureerde para meters die door de pijl zijn gedefinieerd, wijzigen. U kunt ook proxy-instellingen definiëren voordat u de sensor activeert.

De para meters van de sensor netwerk configuratie bijwerken:

1. Selecteer de **sensor netwerk configuratie** koppeling in het dialoog venster **Activering** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Netwerk configuratie van sensor.":::

2. De parameters die tijdens de installatie zijn gedefinieerd, worden weergegeven. De optie voor het definiëren van de proxy is ook beschikbaar. Werk de instellingen indien nodig bij en selecteer **Opslaan**.

### <a name="subsequent-sign-ins"></a>Volgende aanmeldingen

Nadat u de eerste keer hebt geactiveerd, wordt de Azure Defender voor IoT-sensor console geopend na het aanmelden zonder dat een activerings bestand vereist is. U hebt alleen uw aanmeldings referenties nodig.

Nadat u zich hebt aangemeld, wordt de Azure Defender voor IoT-console geopend.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender voor IoT-console.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Eerste installatie en Learning (voor beheerders)

Na uw eerste aanmelding wordt de Azure Defender voor IoT-sensor automatisch gestart om uw netwerk te controleren. Netwerk apparaten worden weer gegeven in de secties Apparaatbeheer en apparaat inventaris. Azure Defender voor IoT detecteert en waarschuwt u voor alle beveiligings-en operationele incidenten die zich in uw netwerk voordoen. U kunt vervolgens rapporten en query's maken op basis van de gedetecteerde informatie.

In eerste instantie wordt deze activiteit uitgevoerd in de leer modus, waardoor uw sensor de gebruikelijke activiteiten van uw netwerk kan ontdekken. De sensor leert bijvoorbeeld apparaten die in uw netwerk zijn gedetecteerd, protocollen die in het netwerk zijn gedetecteerd en bestands overdrachten die tussen specifieke apparaten plaatsvinden. Deze activiteit wordt de basislijn activiteit van uw netwerk.

### <a name="review-and-update-basic-system-settings"></a>Basis systeem instellingen controleren en bijwerken

Controleer de systeem instellingen van de sensor om te controleren of de sensor zo is geconfigureerd dat deze optimaal wordt gedetecteerd en gewaarschuwd.

Definieer de systeem instellingen van de sensor. Bijvoorbeeld:

- Geef ICS (of IoT) en gescheiden subnetten op.

- Geef poort aliassen voor sitespecifieke protocollen op.

- Geef VLAN'S en namen op die in gebruik zijn.

- Als DHCP wordt gebruikt, definieert u legitieme DHCP-bereiken.

- Integratie met Active Directory en e-mail servers definiëren.

### <a name="disable-learning-mode"></a>Leer modus uitschakelen

Nadat u de systeem instellingen hebt aangepast, kunt u de Azure Defender voor IoT-sensor uitvoeren in de leer modus totdat u denkt dat systeem detecties nauw keurig overeenkomen met uw netwerk activiteit.

De leer modus moet ongeveer 2 tot 6 weken worden uitgevoerd, afhankelijk van de grootte en complexiteit van uw netwerk. Wanneer u de leer modus uitschakelt, wordt er een waarschuwing geactiveerd voor elke activiteit die verschilt van de basislijn activiteit.

De leer modus uitschakelen:

- Selecteer **systeem instellingen** en schakel de optie voor het **leren** uit.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Aanmelden voor de eerste keer voor beveiligings analisten en alleen-lezen gebruikers

Voordat u zich aanmeldt, controleert u of u het volgende hebt:

- Het IP-adres van de sensor.
- Aanmeldings referenties die door de beheerder zijn verschaft.

## <a name="console-tools-overview"></a>Console hulpprogramma's: overzicht

U opent console hulpprogramma's vanuit het menu aan de zijkant.

**Navigatie** 

| Venster | Pictogram | Description |
| -----------|--|--|
| Dashboard | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Bekijk een intuïtieve moment opname van de status van de beveiliging van het netwerk. |
| Apparaattoewijzing | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Bekijk de netwerk apparaten, de verbindingen van apparaten en de apparaateigenschappen in een kaart. Er zijn verschillende opties voor inzoomen, markeren en filteren beschikbaar om uw netwerk weer te geven. |
| Inventaris van apparaten | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | De apparaat-inventaris toont een uitgebreid scala aan kenmerken van apparaten die deze sensor detecteert. Opties zijn beschikbaar voor: <br /> -De gegevens filteren op basis van de tabel velden en de gefilterde gegevens weer geven. <br /> -Informatie exporteren naar een CSV-bestand. <br /> -Details van het Windows-REGI ster importeren.|
| Waarschuwingen | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Waarschuwingen weer geven als er schendingen van het beleid optreden, worden er afwijkingen van het basislijn gedrag optreden of wordt een wille keurig type verdachte activiteit in het netwerk gedetecteerd. |
| Rapporten | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Rapporten weer geven die zijn gebaseerd op query's voor gegevens analyse. |

**Bepaling**

| Venster| Pictogram | Description |
|---|---|---|
| Tijd lijn van gebeurtenis | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Bekijk een tijd lijn met informatie over waarschuwingen, netwerk gebeurtenissen (informatief) en gebruikers bewerkingen, zoals aanmeldingen van gebruikers en het verwijderen van gebruikers.|

**Navigatie**

| Venster | Pictogram | Description |
|---|---|---|
| Gegevens analyse | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Uitgebreide en gedetailleerde informatie over de apparaten van uw netwerk op verschillende lagen genereren. |
| Trends en statistieken | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Bekijk trends en statistieken in een uitgebreid scala aan widgets. |
| Risico-evaluatie | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Het venster **beveiligings problemen** weer geven. |

**Beheerder**

| Venster | Pictogram | Description |
|---|---|---|
| Gebruikers | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Definieer gebruikers en rollen met verschillende toegangs niveaus. |
| Doorsturen | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Stuur waarschuwings gegevens door naar partners die zijn geïntegreerd met Defender voor IoT, e-mail adressen, webhook-servers en meer. <br /> Zie [informatie over het door sturen van waarschuwingen](how-to-forward-alert-information-to-partners.md) voor meer informatie. |
| Systeeminstellingen | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Configureer de systeem instellingen. U kunt bijvoorbeeld de DHCP-instellingen definiëren, de details van de e-mail server opgeven of poort aliassen maken. |
| Importinstellingen | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Het venster **import instellingen** weer geven. U kunt hand matige wijzigingen in de gegevens van een apparaat uitvoeren.<br /> Zie [informatie](how-to-import-device-information.md) over het importeren van apparaten voor meer informatie. |

**Ondersteuning**

| Venster| Pictogram | Description |
|----|---|---|
| Ondersteuning | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/) voor hulp. |

### <a name="see-also"></a>Zie ook

[Een sensor onboarden](getting-started.md#4-onboard-a-sensor)

[Activerings bestanden voor de sensor beheren](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Controleren welk verkeer wordt bewaakt](how-to-control-what-traffic-is-monitored.md)

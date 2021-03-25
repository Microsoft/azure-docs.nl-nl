---
title: De licentie voor micro soft Defender voor eind punten gebruiken die is opgenomen in Azure Security Center
description: Meer informatie over micro soft Defender voor eind punt en de implementatie van Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/08/2021
ms.author: memildin
ms.openlocfilehash: 085f3a5295d60b83536683a57a34b51abccd3067
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043016"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Bescherm uw eind punten met de geïntegreerde EDR-oplossing van Security Center: micro soft Defender voor eind punt

Micro soft Defender voor eind punt is een holistische, Cloud geleverde endpoint-beveiligings oplossing. De belangrijkste functies zijn:

- Beheer en evaluatie van beveiligings problemen op basis van Risico's 
- Kwetsbaarheid voor aanvallen verminderen
- Op basis van gedrag en beveiliging in de Cloud
- Eindpunt detectie en-antwoord (EDR)
- Automatisch onderzoek en herstel
- Beheerde jacht-Services

> [!TIP]
> Dit eindpunt detectie-en respons product (EDR) is oorspronkelijk gestart als **Windows Defender ATP**, de naam van dit eind punt voor het detecteren en beantwoorden 2019 van een **micro soft Defender**.
>
> Bij Ignite 2020 is de [micro soft Defender XDR-Suite](https://www.microsoft.com/security/business/threat-protection) gestart en is de naam van dit EDR-onderdeel gewijzigd **in micro soft Defender voor het eind punt**.


## <a name="availability"></a>Beschikbaarheid

| Aspect                          | Details                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Releasestatus:                  | Algemeen verkrijgbaar (GA)                                                                                                                                                                                                                                                                                      |
| Prijzen:                        | [Azure Defender voor servers](defender-for-servers-introduction.md) is vereist                                                                                                                                                                                                                                             |
| Ondersteunde platformen:            |  • Azure-machines met Windows<br> • Azure-Arc-machines met Windows|
| Ondersteunde versies van Windows:  |  • **Algemene Beschik baarheid (ga)-** detectie op Windows Server 2016, 2012 r2 en 2008 R2 SP1<br> • **Preview-** detectie op windows server 2019, [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)en [Windows 10 Enter prise multi-session](../virtual-desktop/windows-10-multisession-faq.md) (voorheen Enter PRISE voor Virtual Bureau bladen (EVD)|
| Niet-ondersteunde besturings systemen:  |  • Windows 10 (met uitzonde ring van EVD of WVD)<br> • Linux|
| Vereiste rollen en machtigingen: | De integratie: **beveiligings beheerder** of **eigenaar** inschakelen/uitschakelen<br>MDATP-waarschuwingen weer geven in Security Center: **beveiligings lezer**, **lezer**, **Inzender voor resource groep**, **eigenaar van resource groep**, **beveiligings beheerder**, **abonnements eigenaar** of **mede werker** van het abonnement|
| Clouds:                         | ![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Functies van micro soft Defender voor endpoint in Security Center

Micro soft Defender voor eind punt biedt:

- **Geavanceerde detectie Sens oren na schending**. De Sens oren van het eind punt voor Windows-computers verzamelen een grote reeks gedrags signalen.

- **Detectie op basis van analyses, na schending van de Cloud**. Defender voor eind punt wordt snel aangepast aan veranderende bedreigingen. Het maakt gebruik van geavanceerde analyses en big data. Het wordt versterkt door de kracht van de Intelligent Security Graph met signalen over Windows, Azure en Office om onbekende bedreigingen te detecteren. Het biedt actie bare waarschuwingen en stelt u in staat snel te reageren.

- **Informatie over bedreigingen**. Met Defender voor endpoint worden er waarschuwingen gegenereerd wanneer de aanvaller hulpprogram ma's, technieken en procedures identificeert. Het maakt gebruik van gegevens die zijn gegenereerd door micro soft Threat jagers en beveiligings teams, uitgebreid door Intelligence van partners.

Door Defender voor het eind punt met Security Center te integreren, profiteert u van de volgende aanvullende mogelijkheden:

- **Automatische onboarding**. Security Center schakelt automatisch de micro soft Defender for Endpoint-sensor in voor alle Windows-servers die worden bewaakt door Security Center.

- **Eén venster glas**. In de Security Center-console worden micro soft Defender voor eindpunt waarschuwingen weer gegeven. Als u verder wilt onderzoeken, gebruikt u de eigen Portal pagina's van micro soft Defender voor het eind punt waar u aanvullende informatie ziet, zoals de structuur van het waarschuwings proces en de incidenten grafiek. U kunt ook een gedetailleerde computer tijdlijn bekijken waarin elk gedrag wordt weer gegeven voor een historische periode van Maxi maal zes maanden.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="De eigen Security Center van micro soft Defender voor het eind punt" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Locatie van micro soft Defender voor endpoint-Tenant

Wanneer u Azure Security Center gebruikt om uw servers te bewaken, wordt automatisch een micro soft Defender for Endpoint-Tenant gemaakt. Gegevens die worden verzameld door Defender voor het eind punt, worden opgeslagen in de geo-locatie van de Tenant, zoals wordt geïdentificeerd tijdens het inrichten. Klant gegevens: in pseudoniem vorm-kan ook worden opgeslagen in de centrale opslag-en verwerkings systemen in de Verenigde Staten. 

Nadat u de locatie hebt geconfigureerd, kunt u deze niet meer wijzigen. Als u uw eigen licentie voor micro soft Defender voor het eind punt hebt en u uw gegevens naar een andere locatie wilt verplaatsen, neemt u contact op met Microsoft Ondersteuning om de Tenant opnieuw in te stellen.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>De integratie van micro soft Defender voor endpoint inschakelen

1. Controleren of uw computer voldoet aan de vereiste vereisten voor Defender voor eind punt:

    - Voor **alle versies van Windows**:
        - Configureer de netwerk instellingen die worden beschreven in [instellingen voor apparaat proxy en Internet connectiviteit configureren](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
        - Als u Defender naar een eind punt implementeert op een on-premises computer, verbindt u deze met Azure Arc zoals uitgelegd in [hybride computers verbinden met servers met Azure-Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - Voor **Windows Server 2019-computers** moet u bovendien controleren of ze een geldige agent uitvoeren en de MicrosoftMonitoringAgent-extensie hebben

1. Schakel **Azure Defender voor servers** in. Zie [Quick Start: Azure Defender inschakelen](enable-azure-defender.md).

1. Als u al een gelicentieerde en geïmplementeerde versie van micro soft Defender voor eind punten op uw servers hebt, verwijdert u deze met behulp van de procedure die wordt beschreven in [niet meer vrijgeven Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
1. Selecteer het abonnement dat u wilt wijzigen.
1. Selecteer **Detectie van dreigingen**.
1. Selecteer **micro soft Defender voor eind punt toestaan om toegang te krijgen tot mijn gegevens** en selecteer **Opslaan**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="De integratie tussen Azure Security Center en de EDR-oplossing van micro soft inschakelen, micro soft Defender voor eind punt":::

    Azure Security Center worden uw servers automatisch op micro soft Defender voor eind punten geboardd. Het kan tot 24 uur duren voordat de onboarding is uitgevoerd.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Toegang tot de micro soft Defender voor endpoint-Portal

1. Zorg ervoor dat het gebruikers account over de benodigde machtigingen beschikt. Meer informatie over [het toewijzen van gebruikers toegang tot micro soft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Controleer of u een proxy of firewall hebt die anonieme verkeer blokkeert. De Defender for Endpoint-sensor maakt verbinding vanaf de systeem context, zodat anonieme verkeer moet worden toegestaan. Volg de instructies in [Enable Access to service URLs in the proxy server](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)om een onbelemmerde toegang tot de Defender voor endpoint portal te garanderen.

1. Open de [micro soft Defender Security Center-Portal](https://securitycenter.windows.com/). Meer informatie over de functies en pictogrammen van de portal vindt u in het [overzicht van micro soft Defender Security Center Portal](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Een test waarschuwing verzenden

Een goed aardige micro soft Defender for Endpoint-test waarschuwing genereren:

1. Maak een map ' C:\test-MDATP-test '.
1. Gebruik Extern bureaublad om toegang te krijgen tot uw computer.
1. Open een opdrachtregelvenster.
1. Kopieer de volgende opdracht bij de prompt en voer deze uit. Het opdracht prompt venster wordt automatisch gesloten.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Een opdracht prompt venster met de opdracht om een test waarschuwing te genereren.":::

1. Als de opdracht is geslaagd, ziet u een nieuwe waarschuwing in het dash board Azure Security Center en in de portal van micro soft Defender voor eind punt. Het kan enkele minuten duren voordat deze waarschuwing wordt weer gegeven.
1. Als u de waarschuwing in Security Center wilt bekijken, gaat u naar **beveiligings waarschuwingen**  >  **verdachte Power shell**-opdracht regel.
1. Selecteer in het venster onderzoek de koppeling om naar de micro soft Defender for Endpoint-portal te gaan.

    > [!TIP]
    > De waarschuwing wordt geactiveerd met de ernst van de **informatie** .

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Veelgestelde vragen over het geïntegreerde micro soft Defender voor eind punt van Security Center

- [Wat zijn de licentie vereisten voor micro soft Defender voor eind punt?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hoe kan ik overschakelen van een EDR-hulp programma van derden?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Wat zijn de licentie vereisten voor micro soft Defender voor eind punt?
Defender voor het eind punt is zonder extra kosten inbegrepen bij **Azure Defender voor servers**. U kunt dit ook afzonderlijk aanschaffen voor 50-computers of meer.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?
Als u al een licentie hebt voor Microsoft Defender for Endpoint, hoeft u niet te betalen voor dat deel van uw Azure Defender-licentie.

Neem contact op met het ondersteuningsteam van Security Center en geef de relevante werkruimte-ID, regio en licentiegegevens van elke relevante licentie op om uw korting te bevestigen.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hoe kan ik overschakelen van een EDR-hulp programma van derden?
Volledige instructies voor het overschakelen van een niet-micro soft-eindpunt oplossing vindt u in de documentatie van micro soft Defender voor endpoint: [migratie overzicht](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Volgende stappen

- [Platforms en functies die worden ondersteund door Azure Security Center](security-center-os-coverage.md)
- [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md): Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
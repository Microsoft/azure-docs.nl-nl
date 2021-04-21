---
title: De Microsoft Defender for Endpoint-licentie gebruiken die is opgenomen in Azure Security Center
description: Meer informatie over Microsoft Defender for Endpoint en het implementeren vanuit Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829868"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Uw eindpunten beveiligen met Security Center geïntegreerde EDR-oplossing: Microsoft Defender for Endpoint

Microsoft Defender for Endpoint is een holistische, cloudoplossing voor eindpuntbeveiliging. De belangrijkste functies zijn:

- Op risico'vulnerability management en evaluatie 
- Kwetsbaarheid voor aanvallen verminderen
- Beveiliging op basis van gedrag en beveiliging in de cloud
- Eindpuntdetectie en -respons (EDR)
- Automatisch onderzoek en herstel
- Beheerde hunting-services

> [!TIP]
> Oorspronkelijk gestart als **Windows Defender ATP,** is de naam van dit EDR-product (Endpoint Detection and Response) in 2019 gewijzigd in **Microsoft Defender ATP.**
>
> Tijdens Ignite 2020 hebben we de [Microsoft Defender XDR-suite](https://www.microsoft.com/security/business/threat-protection) gelanceerd en is de naam van dit EDR-onderdeel gewijzigd in **Microsoft Defender for Endpoint**.


## <a name="availability"></a>Beschikbaarheid

| Aspect                          | Details                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Releasestatus:                  | Algemeen verkrijgbaar (GA)                                                                                                                                                                                                                                                                                      |
| Prijzen:                        | [Azure Defender voor servers](defender-for-servers-introduction.md) is vereist                                                                                                                                                                                                                                             |
| Ondersteunde platformen:            |  • Azure-machines met Windows<br> • Azure Arc machines met Windows|
| Ondersteunde versies van Windows voor detectie:  |  • Windows Server 2019, 2016, 2012 R2 en 2008 R2 SP1<br> • [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise meerdere sessies](../virtual-desktop/windows-10-multisession-faq.yml) (voorheen Enterprise for Virtual Desktops (EVD)|
| Niet-ondersteunde besturingssystemen:  |  • Windows 10 (andere dan EVD of WVD)<br> • Linux|
| Vereiste rollen en machtigingen: | De integratie in- of uitschakelen: **Beveiligingsbeheerder** of **Eigenaar**<br>MDATP-waarschuwingen weergeven in Security Center: **Beveiligingslezer,** **Lezer,** **Inzender voor resourcegroep,** **Resourcegroepeigenaar,** Beveiligingsbeheerder, Abonnementseigenaar of **Inzender voor abonnementen** |
| Clouds:                         | ![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Functies van Microsoft Defender voor eindpunten in Security Center

Microsoft Defender for Endpoint biedt:

- **Geavanceerde detectiesensoren na inbreuk.** De sensoren van Defender for Endpoint voor Windows-machines verzamelen een groot aantal gedragssignalen.

- **Op analyses gebaseerde detectie na een inbreuk in** de cloud. Defender for Endpoint past zich snel aan veranderende bedreigingen aan. Er wordt gebruikgemaakt van geavanceerde analyses en big data. Het wordt versterkt door de kracht van de Intelligent Security Graph met signalen in Windows, Azure en Office om onbekende bedreigingen te detecteren. Het biedt waarschuwingen waarmee actie kan worden ondernomen en u kunt snel reageren.

- **Informatie over bedreigingen**. Defender for Endpoint genereert waarschuwingen wanneer er hulpprogramma's, technieken en procedures voor aanvallers worden geïdentificeerd. Het maakt gebruik van gegevens die worden gegenereerd door microsoft-bedreigings- en beveiligingsteams, uitgebreid met informatie die wordt geleverd door partners.

Door Defender for Endpoint te integreren met Security Center, profiteert u van de volgende extra mogelijkheden:

- **Geautomatiseerde onboarding.** Security Center schakelt automatisch de Microsoft Defender for Endpoint-sensor in voor alle Windows-servers die worden bewaakt door Security Center.

- **Eén deelvenster van het venster**. In Security Center console wordt Microsoft Defender voor eindpuntwaarschuwingen weergegeven. Als u verder wilt onderzoeken, gebruikt u de eigen portalpagina's van Microsoft Defender voor eindpunten, waar u aanvullende informatie ziet, zoals de waarschuwingsprocesstructuur en de incidentgrafiek. U kunt ook een gedetailleerde tijdlijn voor de machine bekijken waarin elk gedrag voor een historische periode van maximaal zes maanden wordt weergegeven.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint's eigen Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Wat zijn de vereisten voor de Microsoft Defender for Endpoint-tenant?

Wanneer u Azure Security Center servers bewaakt, wordt er automatisch een Microsoft Defender for Endpoint-tenant gemaakt. 

- **Locatie:** Gegevens die door Defender for Endpoint worden verzameld, worden opgeslagen op de geografische locatie van de tenant, zoals aangegeven tijdens het inrichten. Klantgegevens , in gepseudonimiseerde vorm, kunnen ook worden opgeslagen in de centrale opslag- en verwerkingssystemen in de Verenigde Staten. Nadat u de locatie hebt geconfigureerd, kunt u deze niet meer wijzigen. Als u uw eigen licentie voor Microsoft Defender voor eindpunt hebt en uw gegevens naar een andere locatie wilt verplaatsen, neem dan contact op met Microsoft-ondersteuning tenant opnieuw in te stellen.
- **Abonnementen verplaatsen:** Als u uw Azure-abonnement hebt verplaatst tussen Azure-tenants, zijn enkele handmatige voorbereidende stappen vereist voordat Security Center Defender for Endpoint implementeert. Neem contact op met [Microsoft Ondersteuning voor volledige informatie.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>De integratie van Microsoft Defender for Endpoint inschakelen

### <a name="prerequisites"></a>Vereisten

Controleer of uw computer voldoet aan de vereisten voor Defender for Endpoint:

1. Zorg ervoor dat de machine, indien nodig, is verbonden met Azure:

    - Configureer **voor Windows-servers** de netwerkinstellingen die worden beschreven in [Instellingen voor apparaatproxy en internetverbinding configureren](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
    - Voor **on-premises** machines verbindt u deze met Azure Arc zoals uitgelegd in Hybride [machines verbinden met Azure Arc ingeschakelde servers](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - Voor **Windows Server 2019-** en [Windows Virtual Desktop-machines (WVD)](../virtual-desktop/overview.md) controleert u of op uw computers de Log Analytics-agent wordt uitgevoerd en dat u de extensie MicrosoftMonitoringAgent hebt.
    
1. Schakel **Azure Defender voor servers in.** Zie [Quickstart: Azure Defender.](enable-azure-defender.md)
1. Als u Microsoft Defender voor eindpunten al hebt gelicentieerd en geïmplementeerd op uw servers, verwijdert u deze met behulp van de procedure die wordt beschreven in [Offboard Windows-servers.](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)
1. Als u uw abonnement hebt verplaatst tussen Azure-tenants, zijn ook enkele handmatige voorbereidende stappen vereist. Neem contact op met [Microsoft Ondersteuning voor volledige informatie.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>De integratie inschakelen
1. Selecteer Security Center in het menu prijzen **&** instellingen en selecteer het abonnement dat u wilt wijzigen.
1. Selecteer **Detectie van dreigingen**.
1. Selecteer **Microsoft Defender voor eindpunt toegang geven tot mijn gegevens** en selecteer **Opslaan.**

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="De integratie inschakelen tussen Azure Security Center en de EDR-oplossing van Microsoft, Microsoft Defender for Endpoint":::

    Azure Security Center uw servers automatisch onboarden naar Microsoft Defender for Endpoint. Onboarding kan tot 24 uur duren.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Toegang tot de Microsoft Defender for Endpoint-portal

1. Zorg ervoor dat het gebruikersaccount de benodigde machtigingen heeft. Meer informatie in [Gebruikerstoegang toewijzen aan Microsoft Defender-beveiligingscentrum.](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

1. Controleer of u een proxy of firewall hebt die anoniem verkeer blokkeert. De Defender for Endpoint-sensor maakt verbinding vanuit de systeemcontext, dus anoniem verkeer moet zijn toegestaan. Volg de instructies in Toegang tot service-URL's [inschakelen op](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)de proxyserver om ongebeheerde toegang tot de Defender for Endpoint-portal te garanderen.

1. Open de [Microsoft Defender-beveiligingscentrum portal](https://securitycenter.windows.com/). Meer informatie over de functies en pictogrammen van de portal kunt u vinden in [Microsoft Defender-beveiligingscentrum overzicht van de portal.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Een testwaarschuwing verzenden

Een goedaardige testwaarschuwing voor Microsoft Defender for Endpoint genereren:

1. Maak een map C:\test-MDATP-test.
1. Gebruik Extern bureaublad toegang tot uw computer.
1. Open een opdrachtregelvenster.
1. Kopieer bij de prompt en voer de volgende opdracht uit. Het opdrachtpromptvenster wordt automatisch gesloten.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Een opdrachtpromptvenster met de opdracht om een testwaarschuwing te genereren.":::

1. Als de opdracht is geslaagd, ziet u een nieuwe waarschuwing op het Azure Security Center dashboard en de Microsoft Defender for Endpoint-portal. Het kan enkele minuten duren voordat deze waarschuwing wordt weergegeven.
1. Als u de waarschuwing in Security Center, gaat u naar **Beveiligingswaarschuwingen**  >  **Suspicious PowerShell CommandLine**.
1. Selecteer in het onderzoekvenster de koppeling om naar de Microsoft Defender for Endpoint-portal te gaan.

    > [!TIP]
    > De waarschuwing wordt geactiveerd met **de ernst** Informatie.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Veelgestelde vragen Security Center geïntegreerde Microsoft Defender for Endpoint van uw netwerk

- [Wat zijn de licentievereisten voor Microsoft Defender for Endpoint?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Hoe kan ik overstappen van een EDR-hulpprogramma van derden?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Wat zijn de licentievereisten voor Microsoft Defender for Endpoint?
Defender for Endpoint is zonder extra kosten opgenomen met **Azure Defender voor servers**. U kunt deze ook afzonderlijk aanschaffen voor 50 machines of meer.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?
Als u al een licentie hebt voor Microsoft Defender for Endpoint, hoeft u niet te betalen voor dat deel van uw Azure Defender-licentie.

Neem contact op met het ondersteuningsteam van Security Center en geef de relevante werkruimte-ID, regio en licentiegegevens van elke relevante licentie op om uw korting te bevestigen.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hoe kan ik overstappen van een EDR-hulpprogramma van derden?
Volledige instructies voor het overstappen van een niet-Microsoft-eindpuntoplossing zijn beschikbaar in de microsoft Defender for Endpoint-documentatie: [Migratieoverzicht.](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)
  


## <a name="next-steps"></a>Volgende stappen

- [Platforms en functies die worden ondersteund door Azure Security Center](security-center-os-coverage.md)
- [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md)meer informatie over hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
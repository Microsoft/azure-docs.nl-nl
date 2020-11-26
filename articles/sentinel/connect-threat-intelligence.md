---
title: Bedreigings informatie gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van bedreigings informatie gegevens met Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121749"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Verbinding maken met gegevens van Threat Intelligence-providers

> [!IMPORTANT]
> De Threat Intelligence-gegevens connectors in azure Sentinel zijn momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Sentinel kunt u de bedreigings indicatoren importeren die uw organisatie gebruikt, waardoor de mogelijkheid van uw beveiligings analisten kan worden uitgebreid om bekende bedreigingen te detecteren en te priori teren. Verschillende functies van Azure Sentinel worden beschikbaar of zijn verbeterd:

- **Analytics** bevat een reeks geplande regel sjablonen die u kunt inschakelen om waarschuwingen en incidenten te genereren op basis van overeenkomsten met logboek gebeurtenissen van uw bedreigings indicatoren.

- **Werkmappen** bieden een overzicht van de bedreigings indicatoren die in azure Sentinel zijn geïmporteerd en eventuele waarschuwingen die worden gegenereerd door analyse regels die overeenkomen met uw bedreigings indicatoren.

- Met **jagers** query's kunnen beveiligings onderzoekers risico indicatoren gebruiken binnen de context van veelvoorkomende jacht-scenario's.

- **Notebooks** kunnen bedreigings indicatoren gebruiken bij het onderzoeken van afwijkingen en het zoeken naar schadelijke gedragingen.

U kunt bedreigings indicatoren streamen naar Azure Sentinel door gebruik te maken van een van de TIP-producten (Integrated Threat Intelligence platform) die worden weer gegeven in de volgende sectie, verbinding te maken met TAXII-servers of door directe integratie met de [Microsoft Graph Security tiIndicators-API](/graph/api/resources/tiindicator)te gebruiken.

## <a name="integrated-threat-intelligence-platform-products"></a>Geïntegreerde platform producten voor bedreigings informatie

- [MISP open source Threat Intelligence-platform](https://www.misp-project.org/)
    
    Voor een voorbeeld script dat clients bevat met MISP-instanties voor het migreren van bedreigings indicatoren naar de Microsoft Graph-beveiligings-API, raadpleegt [u het MISP to Microsoft Graph Security script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Ga naar de pagina [ThreatStream down loads](https://ui.threatstream.com/downloads) om ThreatStream integrator en uitbrei dingen te downloaden en de instructies voor het verbinden van ThreatStream Intelligence met de BEVEILIGINGS-API van Microsoft Graph.

- [Palo Alto-netwerken MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Zie [IOCs verzenden naar de Microsoft Graph-beveiligings-API met behulp van MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)voor begeleide instructies.

- [ThreatConnect-platform](https://threatconnect.com/solution/)

    Zie [ThreatConnect-integraties](https://threatconnect.com/integrations/) en zoek naar Microsoft Graph-BEVEILIGINGS-API op de pagina voor meer informatie.

- [EclecticIQ-platform](https://www.eclecticiq.com/solutions)

- [ThreatQ Threat Intelligence-platform](https://www.threatq.com/)

    Zie [micro soft Sentinel connector voor ThreatQ-integratie](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview)voor informatie en begeleide instructies.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure-Sentinel verbinden met uw Threat Intelligence-platform

### <a name="prerequisites"></a>Vereisten  

- De Azure AD-rol van globale beheerder of beveiligings beheerder om machtigingen te verlenen aan uw TIP-product of een aangepaste toepassing die gebruikmaakt van directe integratie met de Microsoft Graph Security tiIndicators API.

- Lees-en schrijf machtigingen voor de Azure Sentinel-werk ruimte om uw bedreigings indicatoren op te slaan.

### <a name="instructions"></a>Instructies

1. [Registreer een toepassing](/graph/auth-v2-service#1-register-your-app) in azure Active Directory om een toepassings-id, toepassings geheim en Azure Active Directory Tenant-id op te halen. U hebt deze waarden nodig voor wanneer u uw geïntegreerde TIP-product of-app configureert die gebruikmaakt van directe integratie met Microsoft Graph Security tiIndicators-API.

2. [API-machtigingen](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) voor de geregistreerde toepassing configureren: voeg de Microsoft Graph toepassings machtiging **ThreatIndicators. readwrite. OwnedBy** toe aan uw geregistreerde toepassing.

3. Vraag uw Azure Active Directory Tenant beheerder om toestemming van de beheerder te verlenen aan de geregistreerde toepassing voor uw organisatie. Vanuit de Azure portal: **Azure Active Directory**  >  **app-registraties**  >  **\<_app name_>**  >  **weer geven van API-machtigingen**  >  **verlenen \<_tenant name_> beheerders toestemming voor**.

4. Configureer uw TIP-product of-app die gebruikmaakt van directe integratie met Microsoft Graph Security tiIndicators API om indica toren naar Azure Sentinel te verzenden door het volgende op te geven:
    
    a. De waarden voor de geregistreerde toepassings-ID, het geheim en de Tenant-ID.
    
    b. Geef voor het doel product Azure Sentinel op.
    
    c. Geef voor de actie waarschuwing op.

5. Navigeer in het Azure Portal naar **Azure Sentinel**  >  **Data connectors** en selecteer vervolgens de connector **Threat Intelligence platforms (preview)** .

6. Selecteer de **pagina connector openen** en vervolgens **verbinding maken**.

7. Als u de risico indicatoren wilt weer geven die in azure Sentinel zijn geïmporteerd, gaat u naar **Azure Sentinel-logs-logboeken**  >  **SecurityInsights** en vouwt u vervolgens **ThreatIntelligenceIndicator** uit.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Azure-Sentinel verbinden met TAXII-servers

### <a name="prerequisites"></a>Vereisten

- Lees-en schrijf machtigingen voor de Azure Sentinel-werk ruimte om uw bedreigings indicatoren op te slaan.

- TAXII 2,0-server-URI en verzameling-ID.

### <a name="instructions"></a>Instructies

1. Navigeer in het Azure Portal naar **Azure Sentinel**  >  **Data connectors** en selecteer vervolgens de connector **Threat Intelligence-TAXII (preview)** .

2. Selecteer de **pagina connector openen**.

3. Geef de vereiste en optionele informatie op in de tekst vakken.

4. Selecteer **toevoegen** om de verbinding met de TAXII 2,0-server in te scha kelen.

5. Als u over extra TAXII 2,0-servers beschikt: Herhaal stap 3 en 4.

6. Als u de risico indicatoren wilt weer geven die in azure Sentinel zijn geïmporteerd, gaat u naar **Azure Sentinel-logs-logboeken**  >  **SecurityInsights** en vouwt u vervolgens **ThreatIntelligenceIndicator** uit.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u uw Threat Intelligence-provider verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over de Azure-Sentinel.

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).
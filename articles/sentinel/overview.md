---
title: Wat is Azure Sentinel? | Microsoft Docs
description: Meer informatie over Azure Sentinel, een schaalbaar, cloudoplossing voor Security Information Event Management (SIEM) en Security Orchestration Automated Response (SOAR).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570492"
---
# <a name="what-is-azure-sentinel"></a>Wat is Azure Sentinel?

Microsoft Azure Sentinel is een schaalbaar, cloudeigen oplossing voor **Security Information Event Management (SIEM)** en **Security Orchestration Automated Response (SOAR)** . Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie voor de hele onderneming en is daardoor één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve opsporing en reactie op bedreigingen. 

Met Azure Sentinel hebt u overzicht over de hele onderneming, waardoor u zich niet meer druk hoeft te maken over steeds geavanceerdere aanvallen, toenemende hoeveelheden waarschuwingen en de lange duur voordat oplossingen worden gevonden.

- **Verzamel gegevens op cloudschaal** voor alle gebruikers, apparaten, toepassingen en infrastructuur, zowel on-premises als in meerdere clouds. 

- **Detecteer bedreigingen die eerder niet werden gedetecteerd** en beperk het aantal fout-positieven met de analyses en ongeëvenaarde bedreigingsinformatie van Microsoft. 

- **Onderzoek bedreigingen met kunstmatige intelligentie** en spoor verdachte activiteiten op schaal op met gebruik van de cyberbeveiliging waar Microsoft al jaren aan werkt. 

- **Reageer snel op incidenten** met ingebouwde indeling en automatisering van algemene taken.

![Kernfunctionaliteit van Azure Sentinel](./media/overview/core-capabilities.png)

Azure Sentinel bouwt voort op het volledige scala aan bestaande Azure-services en bevat systeemeigen, bewezen toepassingen, zoals Log Analytics en Logic Apps. Azure Sentinel breidt uw onderzoeks- en detectiemogelijkheden uit met AI en bevat alle bedreigingsinformatie van Microsoft. Bovendien kunt u uw eigen bedreigingsinformatie integreren. 

## <a name="connect-to-all-your-data"></a>Verbinden met al uw gegevens

U moet eerst [verbinding maken met uw beveiligingsbronnen](connect-data-sources.md) om Azure Sentinel te onboarden. Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die direct beschikbaar zijn en realtime integratie bieden, inclusief Microsoft 365 Defender (voorheen Microsoft Threat Protection)-oplossingen en Microsoft 365-bronnen, waaronder Office 365, Azure AD, Microsoft Defender for Identity (voorheen Azure ATP), Microsoft Cloud App Security en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U kunt ook Common Event Format, Syslog of REST-API gebruiken om uw gegevensbronnen met Azure Sentinel te verbinden. 

![Gegevensverzamelaars](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Werkmappen

Nadat u [uw gegevensbronnen hebt verbonden](quickstart-onboard.md) met Azure Sentinel, kunt u de gegevens bewaken met behulp van de integratie van Azure Sentinel met Azure Monitor Workbooks, waarmee u veelzijdige, aangepaste werkmappen kunt maken. Hoewel werkmappen anders worden weergegeven in Azure-Sentinel, kan het handig zijn om te zien hoe u [interactieve rapporten kunt maken met Azure Monitor Workbooks](../azure-monitor/visualize/workbooks-overview.md). Met Azure Sentinel kunt u aangepaste werkmappen maken voor uw gegevens. Daarnaast bevat dit ingebouwde werkmapsjablonen, zodat u snel inzicht kunt krijgen in uw gegevens zodra u verbinding maakt met een gegevensbron.

![Dashboards](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analyse

Om de ruis te verminderen en het aantal waarschuwingen te beperken dat u moet beoordelen en onderzoeken, gebruikt Azure Sentinel [analyses om waarschuwingen te relateren aan incidenten](tutorial-detect-threats-built-in.md). **Incidenten** zijn groepen gerelateerde waarschuwingen die samen een mogelijke bedreiging vormen die kan worden onderzocht en opgelost. Gebruik de ingebouwde correlatieregels zoals ze worden geleverd of gebruik ze als beginpunt om uw eigen correlatieregels te maken. Azure Sentinel biedt ook regels voor machine learning om uw netwerkgedrag in kaart te brengen en vervolgens naar afwijkingen in de bronnen te zoeken. Deze analyses leggen een link door waarschuwingen met een lage betrouwbaarheid over verschillende entiteiten te combineren tot mogelijke beveiligingsincidenten met een hoge betrouwbaarheid.

![Incidenten](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Beveiligingsautomatisering en -indeling

Automatiseer uw algemene taken en [vereenvoudig de beveiligingsindeling met playbooks](tutorial-respond-threats-playbook.md) die kunnen worden geïntegreerd met Azure-services en uw bestaande hulpprogramma's. De automatiserings- en indelingsoplossing van Azure Sentinel is ontworpen op basis van Azure Logic Apps en biedt een zeer uitbreidbare architectuur waarmee automatisering kan worden geschaald op basis van nieuwe technologieën en bedreigingen. Als u playbooks wilt maken met Azure Logic Apps, kunt u kiezen uit een steeds groter wordende galerie met ingebouwde playbooks. Dit zijn onder andere [meer dan 200 connectors](../connectors/apis-list.md) voor services zoals Azure-functies. Met de connectors kunt u elke aangepaste logica toepassen in code, ServiceNow, Jira, Zendesk, HTTP-aanvragen, Microsoft Teams, Slack, Windows Defender ATP en Cloud App Security.

Als u bijvoorbeeld het ServiceNow-ticketsysteem gebruikt, kunt u de meegeleverde hulpprogramma's gebruiken om met Azure Logic Apps uw werkstromen te automatiseren en een ticket in ServiceNow te openen telkens wanneer een bepaalde gebeurtenis wordt gedetecteerd.

![Playbooks](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Onderzoek

Met de hulpprogramma's van Azure Sentinel voor [uitgebreid onderzoek](tutorial-investigate-cases.md), momenteel in preview-versie, krijgt u inzicht in de omvang en de hoofdoorzaak van een potentiële beveiligingsdreiging. U kunt een entiteit in de interactieve grafiek kiezen om interessante vragen te stellen voor een specifieke entiteit en inzoomen op deze entiteit en de bijbehorende verbindingen om de hoofdoorzaak van de bedreiging te achterhalen. 

![Onderzoek](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Zoeken

Gebruik de [krachtige opsporingsprogramma's voor zoeken en query's uitvoeren van Azure Sentinel](hunting.md), gebaseerd op het MITRE-framework, om proactief beveiligingsrisico's op te sporen in de gegevensbronnen van uw organisatie, voordat een waarschuwing wordt geactiveerd. Nadat u hebt ontdekt welke opsporingsquery waardevolle inzichten biedt in mogelijke aanvallen, kunt u ook aangepaste detectieregels maken op basis van uw query en deze inzichten weergeven als waarschuwingen voor de beantwoorders van uw beveiligingsincidenten. Tijdens het opsporen kunt u bladwijzers maken voor interessante gebeurtenissen, zodat u er later naar kunt terugkeren, deze met anderen kunt delen en ze kunt groeperen met andere gerelateerde gebeurtenissen om een duidelijk incident te maken voor onderzoek.

![Overzicht van opsporingsfunctie](./media/overview/hunting.png)

## <a name="community"></a>Community

De Azure Sentinel-community is een belangrijke bron voor detectie en automatisering van bedreigingen. Onze Microsoft-beveiligingsanalisten werken voortdurend aan het maken en toevoegen van nieuwe werkmappen, playbooks, opsporingsquery's en meer en plaatsen deze in de community zodat u ze in uw omgeving kunt gebruiken. U kunt voorbeeldinhoud downloaden van de GitHub[-opslagplaats](https://aka.ms/asicommunity) van de privé-community om aangepaste werkmappen, opsporingsquery's, notebooks en playbooks voor Azure Sentinel te maken. 

![De gebruikerscommunity verkennen](./media/overview/community.png)

## <a name="next-steps"></a>Volgende stappen

- U moet een Microsoft Azure-abonnement hebben om met Azure Sentinel aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Leer hoe u [uw gegevens kunt onboarden in Azure Sentinel](quickstart-onboard.md) en [krijg inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
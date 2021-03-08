---
title: Veelgebruikte Azure-werkmappen voor Sentinel | Microsoft Docs
description: Meer informatie over de meest gebruikte werkmappen voor het gebruik van populaire, ingebouwde Azure-Sentinel-resources.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: a37501498a9222025860702a7f29dccc9abfc8f7
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102450061"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Veelgebruikte Azure Sentinel-werkmappen

De volgende tabel geeft een lijst van de meest gebruikte, ingebouwde Azure Sentinel-werkmappen.

Open werkmappen in azure-Sentinel onder **Threat Management**  >  -**werkmappen** aan de linkerkant en zoek vervolgens de werkmap die u wilt gebruiken. Zie [zelf studie: uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)voor meer informatie.

|Werkmap naam  |Description  |
|---------|---------|
|**Efficiëntie van analyse**     |  Biedt inzicht in de effectiviteit van uw analyse-regels om u te helpen bij het verkrijgen van betere prestaties. <br><br>Zie [de Toolkit voor Data-Driven SOCs](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)voor meer informatie.|
|**Azure-activiteit**     |     Voorziet in uitgebreid inzicht in de Azure-activiteit van uw organisatie door het analyseren en correleren van alle bewerkingen en gebeurtenissen van de gebruiker. <br><br>Zie [controle met Azure-activiteiten logboeken](audit-sentinel-data.md#auditing-with-azure-activity-logs)voor meer informatie.    |
|**Audit logboeken van Azure AD**     |  Maakt gebruik van Azure Active Directory audit Logboeken om inzicht te krijgen in azure AD-scenario's. <br><br>Zie  [Quick Start: aan de slag met Azure Sentinel](quickstart-get-visibility.md)voor meer informatie.     |
|**Azure AD-controle, activiteit en aanmeldings logboeken**     |   Biedt inzicht in Azure Active Directory controle, activiteit en aanmeldings gegevens met één werkmap. Deze werkmap kan worden gebruikt door zowel beveiliging als Azure-beheerders.      |
|**Aanmeldingslogboeken voor Azure AD**     | Maakt gebruik van de logboeken van Azure AD-aanmelding om inzicht te krijgen in azure AD-scenario's.        |
|**Cyber beveiliging rijpheid (CMMC)**     |   Biedt een mechanisme voor het weer geven van logboek query's die zijn afgestemd op CMMC-besturings elementen op de micro soft-Port Folio, waaronder micro soft-beveiligings aanbiedingen, Office 365, teams, intune, Windows virtueel bureau blad, enzovoort. <br><br>Zie [Cyber beveiliging (CMMC)-werkmap in de open bare preview](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184)voor meer informatie.|
|**Status controle van gegevens verzameling**     |   Geeft inzicht in de status van de gegevens opname van uw werk ruimte. Bekijk monitors en Detecteer afwijkingen om u te helpen bij het bepalen van de status van de gegevens verzameling voor werk ruimten.  <br><br>Zie [de status van uw gegevens connectors controleren met deze Azure Sentinel-werkmap](monitor-data-connector-health.md)voor meer informatie.    |
|**Event Analyzer**     |  Hiermee kunt u de analyse van het Windows-gebeurtenis logboek verkennen, controleren en versnellen, inclusief alle gebeurtenis Details en kenmerken, zoals beveiliging, toepassing, systeem, installatie, Directory service, DNS, enzovoort.       |
|**Exchange Online**     |Biedt inzicht in micro soft Exchange Online door alle Exchange-bewerkingen en gebruikers activiteiten te traceren en te analyseren.         |
|**Identiteit en toegang**     |   Biedt inzicht in identiteits-en toegangs bewerkingen in het gebruik van micro soft-producten, via beveiligings logboeken die controle-en aanmeldings logboeken bevatten.     |
|**Overzicht van incidenten**     |   Is ontworpen om u te helpen bij sorteren en onderzoek door uitgebreide informatie over een incident op te geven, waaronder algemene informatie, entiteits gegevens, sorteren tijd, beperkende tijd en opmerkingen. <br><br>Zie [de Toolkit voor Data-Driven SOCs](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)voor meer informatie.      |
|**Onderzoek inzichten**     | Biedt analisten inzicht in incident-, Blade-en entiteits gegevens. Algemene query's en gedetailleerde visualisaties kunnen analisten helpen verdachte activiteiten te onderzoeken.       |
|**Logboeken voor Microsoft Cloud App Security detectie**     |   Bevat details over de Cloud-apps die in uw organisatie worden gebruikt, en inzichten uit gebruiks trends en inzoomen op gegevens voor specifieke gebruikers en toepassingen.  <br><br>Zie [verbinding maken tussen gegevens van Microsoft Cloud app Security](connect-cloud-app-security.md)voor meer informatie.|
|**MITRE ATT&verzonken werkmap**     |   Bevat informatie over de MITRE ATT&voor de Azure Sentinel-dekking.      |
|**Office 365**     |  Biedt inzicht in Office 365 door alle bewerkingen en activiteiten te traceren en te analyseren. Inzoomen op de share point-, OneDrive-en Exchange-gegevens.       |
|**Beveiligings waarschuwingen**     |  Biedt een dash board voor beveiligings waarschuwingen voor waarschuwingen in uw Azure-Sentinel-omgeving. <br><br>Zie voor meer informatie [automatisch incidenten maken op basis van beveiligings waarschuwingen van micro soft](create-incidents-from-alerts.md).      |
|**Efficiëntie van beveiligings bewerkingen**     |  Is bedoeld voor SOC-managers (Security Operations Center) om algemene efficiëntie gegevens en metingen te bekijken met betrekking tot de prestaties van hun team. <br><br>Zie [uw Soc beter beheren met metrische gegevens over incidenten](manage-soc-with-incident-metrics.md)voor meer informatie.  |
|**Bedreigingsinformatie**     | Biedt inzicht in bedreigings indicatoren, waaronder het type en de ernst van bedreigingen, de bedreigings activiteiten gedurende een periode en correlatie met andere gegevens bronnen, waaronder Office 365 en firewalls.  <br><br>Zie [Threat Intelligence importeren in azure Sentinel](import-threat-intelligence.md)voor meer informatie.      |
|**Werkruimte controle**     |  Biedt een controle rapport voor werk ruimten waarmee u inzicht krijgt in de query uitvoeringen in uw werk ruimte.   <br><br>Zie Azure-Sentinel- [query's en-activiteiten controleren](audit-sentinel-data.md)voor meer informatie.  |
|     |         |


---
title: Advanced Threat Protection-Azure Portal-Azure Database for MySQL
description: Meer informatie over het configureren van geavanceerde beveiliging tegen bedreigingen voor het detecteren van afwijkende database activiteiten die duiden op mogelijke beveiligings dreigingen voor de data base.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: b30bd36dca6f866b8f3e6e8a0b133a6dd61b239b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012660"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Advanced Threat Protection voor Azure Database for MySQL

Advanced Threat Protection for Azure Database for MySQL detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot databases of om deze aan te vallen.

Advanced Threat Protection maakt deel uit van de Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com) en is momenteel beschikbaar als preview-versie.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US gov-Iowa, VS, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-Oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.
>

> [!NOTE]
> Deze functie is beschikbaar in alle regio's van Azure waar Azure Database for MySQL wordt geïmplementeerd voor Algemeen en servers die zijn geoptimaliseerd voor geheugen.

## <a name="set-up-threat-detection"></a>Detectie van bedreigingen instellen
1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com) .
2. Ga naar de configuratie pagina van de Azure Database for MySQL-server die u wilt beveiligen. Selecteer in de beveiligings instellingen **Advanced Threat Protection (preview)**.
3. Op de configuratie pagina **Advanced Threat Protection (preview)** :

   - Geavanceerde beveiliging tegen bedreigingen inschakelen op de-server.
   - Geef in het tekstvak **waarschuwingen verzenden naar** een lijst met e-mail berichten op voor het ontvangen van beveiligings waarschuwingen bij de detectie van afwijkende database activiteiten in de **instellingen voor geavanceerde beveiliging tegen bedreigingen**.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Detectie van bedreigingen instellen":::

## <a name="explore-anomalous-database-activities"></a>Afwijkende database activiteiten verkennen

U ontvangt een e-mail melding wanneer er afwijkende database activiteiten worden gedetecteerd. Het e-mail bericht bevat informatie over de verdachte beveiligings gebeurtenis, inclusief de aard van de afwijkende activiteiten, de database naam, de server naam, de toepassings naam en de tijd van de gebeurtenis. Daarnaast bevat het e-mail bericht informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van de mogelijke bedreiging voor de data base.
 
1. Klik op de koppeling **recente waarschuwingen weer geven** in het e-mail bericht om de Azure portal te starten en de pagina Azure Security Center waarschuwingen weer te geven. Deze bevat een overzicht van actieve bedreigingen die zijn gedetecteerd op de SQL database.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Rapport afwijkende activiteiten":::

    Actieve bedreigingen weer geven:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Actieve bedreigingen":::

2. Klik op een specifieke waarschuwing voor aanvullende details en acties voor het onderzoeken van deze dreiging en het oplossen van toekomstige bedreigingen.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Specifieke waarschuwing":::

## <a name="explore-threat-detection-alerts"></a>Waarschuwingen voor detectie van dreigingen verkennen

SQL Database detectie van bedreigingen integreert de waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/). Een live SQL Threat-detectie tegel houdt de status van actieve bedreigingen bij op de data base en de SQL-ATP-pagina's in de Azure Portal.

Klik op **waarschuwing bedreigingen detectie** om de pagina Azure Security Center waarschuwingen te starten en een overzicht te krijgen van actieve SQL-bedreigingen die zijn gedetecteerd op de data base.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Waarschuwing voor detectie van bedreigingen":::
   

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](../security-center/security-center-introduction.md)
* Zie de [pagina met prijzen voor Azure database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie over prijzen.
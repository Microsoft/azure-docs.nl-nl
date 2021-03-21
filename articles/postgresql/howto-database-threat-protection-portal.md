---
title: Advanced Threat Protection gebruiken-Azure Database for PostgreSQL-één server
description: Bedreigings beveiliging detecteert afwijkende database activiteiten die duiden op mogelijke beveiligings dreigingen voor de data base.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 5583e8423f0909936d9e55c6d87593835eded8f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489894"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Advanced Threat Protection voor Azure Database for PostgreSQL-één server

Advanced Threat Protection for Azure Database for PostgreSQL detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot databases of om deze aan te vallen.

Advanced Threat Protection maakt deel uit van de Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com) en is momenteel beschikbaar als preview-versie.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US gov-Iowa, VS, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-Oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.
>

> [!NOTE]
> Deze functie is beschikbaar in alle regio's van Azure waar Azure Database for PostgreSQL wordt geïmplementeerd voor Algemeen en servers die zijn geoptimaliseerd voor geheugen.

## <a name="set-up-threat-detection"></a>Detectie van bedreigingen instellen
1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com) .
2. Ga naar de configuratie pagina van de Azure Database for PostgreSQL-server die u wilt beveiligen. Selecteer in de beveiligings instellingen **Advanced Threat Protection (preview)**.
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

Geavanceerde beveiliging tegen bedreigingen integreert de waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Klik op **beveiligings waarschuwingen** onder **bedreigings beveiliging** om de pagina Azure Security Center waarschuwingen te starten en een overzicht te krijgen van actieve SQL-bedreigingen die zijn gedetecteerd op de data base.

  :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Beveiliging tegen bedreigingen ASC":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](../security-center/security-center-introduction.md)
* Zie de [pagina met prijzen voor Azure database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie over prijzen.
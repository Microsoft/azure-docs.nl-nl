---
title: Advanced Threat Protection-Azure Portal-Azure Database for MariaDB
description: Bedreigings beveiliging voor Azure Database for MariaDB detecteert afwijkende database activiteiten die duiden op mogelijke beveiligings dreigingen voor de data base.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665103"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Advanced Threat Protection voor Azure Database for MariaDB

Advanced Threat Protection voor Azure Database for MariaDB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Advanced Threat Protection maakt deel uit van de Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Advanced Threat Protection bevindt zich in de open bare preview. Deze functie is beschikbaar in alle regio's van Azure waar Azure Database for MariaDB wordt geïmplementeerd voor Algemeen en servers die zijn geoptimaliseerd voor geheugen.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US gov-Iowa, VS, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-Oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.

## <a name="set-up-threat-detection"></a>Detectie van bedreigingen instellen
1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com) .
2. Ga naar de configuratie pagina van de Azure Database for MariaDB-server die u wilt beveiligen. Selecteer in de beveiligings instellingen **Advanced Threat Protection (preview)**.
3. Op de configuratie pagina **Advanced Threat Protection (preview)** :

   - Geavanceerde beveiliging tegen bedreigingen inschakelen op de-server.
   - Geef in het tekstvak **waarschuwingen verzenden naar** een lijst met e-mail berichten op voor het ontvangen van beveiligings waarschuwingen bij de detectie van afwijkende database activiteiten in de **instellingen voor geavanceerde beveiliging tegen bedreigingen**.
  
   ![Detectie van bedreigingen instellen](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Afwijkende database activiteiten verkennen

U ontvangt een e-mail melding wanneer er afwijkende database activiteiten worden gedetecteerd. Het e-mail bericht bevat informatie over de verdachte beveiligings gebeurtenis, inclusief de aard van de afwijkende activiteiten, de database naam, de server naam, de toepassings naam en de tijd van de gebeurtenis. Daarnaast bevat het e-mail bericht informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van de mogelijke bedreiging voor de data base.
 
1. Klik op de koppeling **recente waarschuwingen weer geven** in het e-mail bericht om de Azure portal te starten en de pagina Azure Security Center waarschuwingen weer te geven. Deze bevat een overzicht van actieve bedreigingen die zijn gedetecteerd op de SQL database.
    
    ![Rapport afwijkende activiteiten](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Actieve bedreigingen weer geven:

    ![Actieve bedreigingen](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klik op een specifieke waarschuwing voor aanvullende details en acties voor het onderzoeken van deze dreiging en het oplossen van toekomstige bedreigingen.
    
    ![Specifieke waarschuwing](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Waarschuwingen voor detectie van dreigingen verkennen

SQL Database detectie van bedreigingen integreert de waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/). Een live SQL-bedreigings detectie tegels in de data base en SQL-ATP-blades in de Azure Portal houdt de status van actieve bedreigingen bij.

Klik op **waarschuwing bedreigingen detectie** om de pagina Azure Security Center waarschuwingen te starten en een overzicht te krijgen van actieve SQL-bedreigingen die zijn gedetecteerd op de data base.

   ![Waarschuwing voor detectie van bedreigingen](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](../security-center/security-center-introduction.md)
* Zie de [pagina met prijzen voor Azure database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) voor meer informatie over prijzen.
---
title: Advanced Threat Protection-Azure Database for MariaDB
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die duiden op mogelijke beveiligings dreigingen voor de data base.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661616"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Advanced Threat Protection

Advanced Threat Protection voor Azure Database for MariaDB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

> [!IMPORTANT]
> Advanced Threat Protection bevindt zich in de open bare preview.

Advanced Threat Protection maakt deel uit van de Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com). De functie is beschikbaar voor servers met Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US gov-Iowa, VS, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-Oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.


## <a name="what-is-advanced-threat-protection"></a>Wat is Advanced Threat Protection?

Advanced Threat Protection voor Azure Database for MariaDB biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te bieden over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten en mogelijke beveiligings problemen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection voor Azure Database for MariaDB integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging. Geavanceerde beveiliging tegen bedreigingen voor Azure Database for MariaDB maakt het eenvoudig om mogelijke dreigingen naar de data base te verhelpen zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren. 

![Concept geavanceerde beveiliging tegen bedreigingen](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Geavanceerde beveiligings waarschuwingen 
Advanced Threat Protection voor Azure Database for MariaDB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases en de volgende waarschuwingen kunnen activeren:
- **Toegang vanaf ongebruikelijke locatie**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for MariaDB-server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de Azure database for MariaDB-server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang vanaf ongebruikelijk Azure Data Center**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for MariaDB-server, waarbij iemand zich bij de server heeft aangemeld vanuit een ongebruikelijk Azure Data Center dat tijdens de recente periode op deze server is gedetecteerd. In sommige gevallen detecteert de waarschuwing een legitieme actie (uw nieuwe toepassing in azure, Power BI). In andere gevallen detecteert de waarschuwing een schadelijke actie vanuit een Azure resource/service (voormalig werknemer, externe aanvaller).
- **Toegang vanaf een niet-vertrouwde Principal**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for MariaDB-server, waarbij iemand zich heeft aangemeld bij de server met behulp van een ongewone principal (Azure database for MariaDB gebruiker). In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval met behulp van gebruikelijk aanvalsprogramma’s.
- Beveiligings **verificatie Azure database for MariaDB referenties**: deze waarschuwing wordt geactiveerd wanneer er sprake is van een abnormaal groot aantal mislukte aanmeldingen met andere referenties. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een Brute Force-aanval.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](../security-center/security-center-introduction.md)
* Zie de [pagina met prijzen voor Azure database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) voor meer informatie over prijzen. 
* [Azure database for MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) configureren met behulp van de Azure Portal
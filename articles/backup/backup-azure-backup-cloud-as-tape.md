---
title: De tape-infra structuur vervangen
description: Meer informatie over het Azure Backup van tape-achtige semantiek waarmee u back-ups kunt maken en gegevens kunt herstellen in azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89378437"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Uw lange termijn opslag verplaatsen van tape naar de Azure-Cloud

Azure Backup en System Center Data Protection Manager klanten kunnen:

* Maak een back-up van gegevens in schema's die het beste aansluiten bij de behoeften van de organisatie.
* Bewaar de back-upgegevens gedurende langere Peri Oden.
* Maak Azure een deel van de vereisten voor de Bewaar periode voor de lange termijn (in plaats van tape).

In dit artikel wordt uitgelegd hoe klanten een back-up-en bewaar beleid kunnen inschakelen. Klanten die gebruikmaken van tapes, hebben nu een krachtig en levensvatbaar alternatief voor de beschik baarheid van deze functie. De functie is ingeschakeld in de meest recente versie van de Azure Backup (deze is [hier](https://aka.ms/azurebackup_agent)beschikbaar). System Center DPM-klanten moeten worden bijgewerkt naar ten minste DPM 2012 R2 UR5 voordat DPM met de Azure Backup-service wordt gebruikt.

## <a name="what-is-the-backup-schedule"></a>Wat is het back-upschema?

Het back-upschema geeft de frequentie van de back-upbewerking aan. De instellingen in het volgende scherm geven bijvoorbeeld aan dat back-ups dagelijks worden uitgevoerd op 18:00 uur en om middernacht.

![Dagelijks schema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klanten kunnen ook een wekelijkse back-up plannen. De instellingen in het volgende scherm geven bijvoorbeeld aan dat back-ups worden genomen op elke tweede zondag & woensdag om 9: om 9:30 uur en 1: am.

![Wekelijks schema](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Wat is het Bewaar beleid?

In het Bewaar beleid wordt de duur opgegeven waarvoor de back-up moet worden opgeslagen. In plaats van alleen een ' plat beleid ' voor alle back-uppunten op te geven, kunnen klanten een ander Bewaar beleid opgeven op basis van wanneer de back-up wordt gemaakt. Het back-uppunt dat bijvoorbeeld dagelijks is gemaakt en dat fungeert als operationeel herstel punt, blijft 90 dagen bewaard. Het back-uppunt dat wordt gemaakt aan het einde van elk kwar taal voor controle doeleinden, blijft bewaard gedurende een langere periode.

![Bewaarbeleid](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Het totale aantal "Bewaar punten" dat is opgegeven in dit beleid is 90 (dagelijks punt) + 40 (één kwar taal gedurende 10 jaar) = 130.

## <a name="example--putting-both-together"></a>Voor beeld: beide samen zetten

![Voorbeeld scherm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dagelijks Bewaar beleid**: back-ups die dagelijks worden gemaakt, worden gedurende zeven dagen bewaard.
2. **Wekelijks Bewaar beleid**: back-ups die zijn gemaakt om middernacht en 6 uur zaterdag worden vier weken bewaard.
3. **Maandelijks Bewaar beleid**: back-ups die zijn gemaakt om middernacht en 6 uur op de laatste zaterdag van elke maand blijven 12 maanden bewaard.
4. **Jaarlijks Bewaar beleid**: back-ups die zijn gemaakt om middernacht op de laatste zaterdag van elke maart worden tien jaar bewaard.

Het totale aantal Bewaar punten (punten van waaruit een klant gegevens kan herstellen) in het voor gaande diagram wordt als volgt berekend:

* twee punten per dag voor zeven dagen = 14 herstel punten
* twee punten per week voor vier weken = 8 herstel punten
* twee punten per maand gedurende 12 maanden = 24 herstel punten
* Eén punt per jaar per 10 jaar = 10 herstel punten

Het totale aantal herstel punten is 56.

> [!NOTE]
> Met Azure Backup kunt u Maxi maal 9999 herstel punten per beveiligd exemplaar maken. Een beveiligd exemplaar is een computer, een server (fysiek of virtueel) of een workload waarvan een back-up wordt gemaakt in Azure.
>

## <a name="advanced-configuration"></a>Geavanceerde configuratie

Door **wijzigen** te selecteren in het vorige scherm, hebben klanten nog meer flexibiliteit bij het opgeven van Bewaar schema's.

![Beleids venster wijzigen](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Backup raadpleegt u:

* [Kennismaking met Azure Backup](./backup-overview.md)
* [Azure Backup proberen](./backup-windows-with-mars-agent.md)

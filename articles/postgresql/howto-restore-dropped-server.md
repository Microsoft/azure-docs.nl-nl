---
title: Een verwijderde Azure Database for PostgreSQL server herstellen
description: In dit artikel wordt beschreven hoe u een verwijderde server in Azure Database for PostgreSQL kunt herstellen met behulp van de Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652922"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Een verwijderde Azure Database for PostgreSQL server herstellen

Wanneer een server wordt verwijderd, kan de back-up van de database server tot vijf dagen in de service worden bewaard. De back-up van de data base kan worden geopend en alleen worden teruggezet vanuit het Azure-abonnement waarop de server oorspronkelijk is gesideeerd. De volgende aanbevolen stappen kunnen worden gevolgd om een verwijderde PostgreSQL-server resource binnen vijf dagen te herstellen vanaf het moment dat de server wordt verwijderd. De aanbevolen stappen werken alleen als de back-up voor de server nog steeds beschikbaar is en niet is verwijderd uit het systeem. 

## <a name="pre-requisites"></a>Vereisten
Als u een verwijderde Azure Database for PostgreSQL server wilt herstellen, moet u het volgende doen:
- Naam van het Azure-abonnement dat als host fungeert voor de oorspronkelijke server
- Locatie waar de server is gemaakt

## <a name="steps-to-restore"></a>Te herstellen stappen

1. Blader naar [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selecteer de **Azure monitor** -service en selecteer vervolgens **activiteiten logboek**.

2. Klik in activiteiten logboek op **filter toevoegen** zoals wordt weer gegeven en stel de volgende filters in voor de volgende

    - **Abonnement** = uw abonnement dat als host fungeert voor de verwijderde server
    - **Resource type** = Azure database for PostgreSQL servers (micro soft. DBforPostgreSQL/servers)
    - **Operation** = postgresql-server verwijderen (micro soft. DBforPostgreSQL/servers/verwijderen)
 
    ![Activiteiten logboek gefilterd op de bewerking voor het verwijderen van de PostgreSQL-server](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selecteer de **postgresql-server gebeurtenis verwijderen** en selecteer vervolgens het **tabblad JSON**. Kopieer de `resourceId` `submissionTimestamp` kenmerken en in JSON-uitvoer. De resourceId heeft de volgende indeling: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Ga naar de [pagina postgresql maken Server rest API](/rest/api/PostgreSQL/servers/create) en selecteer het tabblad **try it is** gemarkeerd als groen. Meld u aan met uw Azure-account.

 5. Geef de **resourceGroupName**, **servername** (verwijderde server naam), **subscriptionId** -eigenschappen op op basis van de waarde van het kenmerk resourceId die is vastgelegd in de voor gaande stap 3. De eigenschap API-Version is vooraf ingevuld en kan worden bijgewerkt, zoals wordt weer gegeven in de volgende afbeelding.

    ![Een server maken met REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Schuif hieronder op de sectie aanvraag hoofdtekst en plak de volgende Vervang de ' verwijderde server locatie ', ' submissionTimestamp ' en ' resourceId '. Geef voor ' restorePointInTime ' de waarde ' submissionTimestamp ' min **15 minuten** op om ervoor te zorgen dat de opdracht niet fout wordt uitgevoerd.
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    Als bijvoorbeeld de huidige tijd 2020-11-02T23:59:59.0000000 Z is, raden we u aan mini maal 15 minuten voorafgaand aan het herstel punt in de tijd 2020-11-02T23:44:59.0000000 Z.

    > [!Important]
    > Er is een tijds limiet van vijf dagen na het verwijderen van de server. Na vijf dagen wordt er een fout verwacht omdat het back-upbestand niet kan worden gevonden.
    
7. Als u antwoord code 201 of 202 ziet, wordt de herstel aanvraag verzonden. 

    Het maken van de server kan enige tijd duren, afhankelijk van de grootte van de data base en de reken resources die zijn ingericht op de oorspronkelijke server. De herstel status kan worden bewaakt vanuit het activiteiten logboek door te filteren op 
   - **Abonnement** = uw abonnement
   - **Resource type** = Azure database for PostgreSQL servers (micro soft. DBforPostgreSQL/servers) 
   - **Operational** = update postgresql server Create

## <a name="next-steps"></a>Volgende stappen
- Als u een server binnen vijf dagen wilt herstellen en er nog steeds een fout melding wordt weer gegeven nadat u de eerder beschreven stappen hebt uitgevoerd, opent u een ondersteunings incident voor hulp. Als u een verwijderde server na vijf dagen probeert te herstellen, wordt een fout verwacht omdat het back-upbestand niet kan worden gevonden. Open in dit scenario geen ondersteunings ticket. Het ondersteunings team kan geen ondersteuning bieden als de back-up wordt verwijderd uit het systeem. 
- Om het onbedoeld verwijderen van servers te voor komen, raden we u ten zeerste aan [resource vergrendeling](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)te gebruiken.

---
title: Een verwijderde Azure Database for PostgreSQL herstellen
description: In dit artikel wordt beschreven hoe u een verwijderde server in Azure Database for PostgreSQL herstellen met behulp van Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366518"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Een verwijderde Azure Database for PostgreSQL herstellen

Wanneer een server wordt verwijderd, kan de back-up van de databaseserver maximaal vijf dagen in de service worden bewaard. De back-up van de database kan alleen worden gebruikt en hersteld vanuit het Azure-abonnement waarin de server zich oorspronkelijk bevindt. De volgende aanbevolen stappen kunnen worden gevolgd om een verwijderde PostgreSQL-serverresource te herstellen binnen vijf dagen na het moment van verwijdering van de server. De aanbevolen stappen werken alleen als de back-up voor de server nog steeds beschikbaar is en niet is verwijderd uit het systeem. 

## <a name="pre-requisites"></a>Vereisten
Als u een verwijderde Azure Database for PostgreSQL wilt herstellen, hebt u het volgende nodig:
- Azure-abonnementsnaam die als host voor de oorspronkelijke server wordt gebruikt
- Locatie waar de server is gemaakt

## <a name="steps-to-restore"></a>Stappen om te herstellen

1. Blader naar [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selecteer de **Azure Monitor service** en selecteer vervolgens **Activiteitenlogboek.**

2. Klik in activiteitenlogboek op **Filter toevoegen** zoals wordt weergegeven en stel de volgende filters in voor het volgende

    - **Abonnement** = Uw abonnement dat als host voor de verwijderde server wordt gebruikt
    - **Resourcetype** = Azure Database for PostgreSQL servers (Microsoft.DBforPostgreSQL/servers)
    - **Bewerking** = PostgreSQL-server verwijderen (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![Activiteitenlogboek gefilterd om PostgreSQL-serverbewerking te verwijderen](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selecteer de **gebeurtenis PostgreSQL Server** verwijderen en selecteer vervolgens het **tabblad JSON.** Kopieer de `resourceId` kenmerken en in de `submissionTimestamp` JSON-uitvoer. De resourceId heeft de volgende indeling: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Blader naar de pagina PostgreSQL [Create Server REST API en](/rest/api/PostgreSQL/servers/create) selecteer het tabblad Try It **gemarkeerd** in het groen. Meld u aan met uw Azure-account.

 5. Geef de **eigenschappen resourceGroupName,** **serverName** (naam van verwijderde server) en **subscriptionId** op op basis van de JSON-waarde van het kenmerk resourceId die in de vorige stap 3 is vastgelegd. De eigenschap api-version is vooraf ingevuld en kan in de staat worden gelaten, zoals wordt weergegeven in de volgende afbeelding.

    ![Server maken met REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Schuif hieronder in de sectie Aanvraag body en plak het volgende, ter vervanging van 'Dropped server Location'(bijvoorbeeld CentralUS, EastUS enzovoort), 'submissionTimestamp' en 'resourceId'. Geef voor restorePointInTime een waarde van 'submissionTimestamp' min **15** minuten op om ervoor te zorgen dat de opdracht geen fout bevat.
    
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

    Als de huidige tijd bijvoorbeeld 2020-11-02T23:59:59.0000000Z is, wordt een minimum van 15 minuten voorafgaand aan het herstelpunt in de tijd 2020-11-02T23:44:59.000000Z aangeraden.

    > [!Important]
    > Er is een tijdslimiet van vijf dagen nadat de server is verwijderd. Na vijf dagen wordt er een fout verwacht omdat het back-upbestand niet kan worden gevonden.
    
7. Als u antwoordcode 201 of 202 ziet, wordt de herstelaanvraag verzonden. 

    Het maken van de server kan even duren, afhankelijk van de grootte van de database en de rekenbronnen die op de oorspronkelijke server zijn ingericht. De herstelstatus kan worden gecontroleerd vanuit het activiteitenlogboek door te filteren op 
   - **Abonnement** = uw abonnement
   - **Resourcetype** = Azure Database for PostgreSQL servers (Microsoft.DBforPostgreSQL/servers) 
   - **Bewerking** = Maken van PostgreSQL-server bijwerken

## <a name="next-steps"></a>Volgende stappen
- Als u een server binnen vijf dagen probeert te herstellen en nog steeds een foutmelding ontvangt nadat u de eerder besproken stappen nauwkeurig hebt uitgevoerd, opent u een ondersteuningsincident voor hulp. Als u een verwijderde server na vijf dagen probeert te herstellen, wordt er een fout verwacht omdat het back-upbestand niet kan worden gevonden. Open geen ondersteuningsticket in dit scenario. Het ondersteuningsteam kan geen hulp bieden als de back-up uit het systeem wordt verwijderd. 
- We raden u ten zeerste aan resourcevergrendelingen te gebruiken om onbedoelde verwijdering van servers [te voorkomen.](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)

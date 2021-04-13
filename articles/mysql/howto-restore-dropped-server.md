---
title: Een verwijderde Azure Database for MySQL server herstellen
description: In dit artikel wordt beschreven hoe u een verwijderde server in Azure Database for MySQL kunt herstellen met behulp van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 5fc1ab1b3dfbc324668873749c143846c2015cd4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306276"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>Een verwijderde Azure Database for MySQL server herstellen

Wanneer een server wordt verwijderd, kan de back-up van de database server Maxi maal vijf dagen in de service worden bewaard. De back-up van de data base kan worden geopend en alleen worden teruggezet vanuit het Azure-abonnement waarop de server oorspronkelijk is gesideeerd. De volgende aanbevolen stappen kunnen worden gevolgd om een verwijderde MySQL-Server bron te herstellen binnen 5 dagen vanaf het moment dat de server wordt verwijderd. De aanbevolen stappen werken alleen als de back-up voor de server nog steeds beschikbaar is en niet is verwijderd uit het systeem. 

## <a name="pre-requisites"></a>Vereisten
Als u een verwijderde Azure Database for MySQL server wilt herstellen, moet u het volgende doen:
- Naam van het Azure-abonnement dat als host fungeert voor de oorspronkelijke server
- Locatie waar de server is gemaakt

## <a name="steps-to-restore"></a>Te herstellen stappen

1. Ga naar het [activiteiten logboek](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) van de Blade bewaken in azure Portal. 

2. Klik in activiteiten logboek op **filter toevoegen** zoals wordt weer gegeven en stel de volgende filters in voor de 

    - **Abonnement** = uw abonnement dat als host fungeert voor de verwijderde server
    - **Resource type** = Azure database for MySQL servers (micro soft. DBforMySQL/servers) 
    - **Operational** = mysql-server verwijderen (micro soft. DBforMySQL/servers/verwijderen) 
 
     [![Het activiteiten logboek is gefilterd voor het verwijderen van de MySQL-server bewerking](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Dubbel klik op de gebeurtenis MySQL-server verwijderen en klik op het tabblad JSON en noteer de kenmerken ' resourceId ' en ' submissionTimestamp ' in JSON-uitvoer. De resourceId heeft de volgende indeling:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Ga naar [Server rest API pagina maken](/rest/api/mysql/servers/create) en klik op het tabblad ' try it ' is gemarkeerd als groen en meld u aan met uw Azure-account.
 
 5. Geef de resourceGroupName, servername (verwijderde server naam), subscriptionId, afgeleid van het kenmerk resourceId dat is vastgelegd in stap 3, terwijl de API-versie vooraf is ingevuld, zoals wordt weer gegeven in de afbeelding.
 
     [![Een server maken met REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Schuif hieronder op de sectie aanvraag tekst en plak het volgende:
 
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
7. Vervang de volgende waarden in de bovenstaande hoofd tekst van de aanvraag:
   * ' Verwijderde server locatie ' door de Azure-regio waar de verwijderde server oorspronkelijk is gemaakt
   * ' submissionTimestamp ' en ' resourceId ' door de waarden die zijn vastgelegd in stap 3. 
   * Geef voor ' restorePointInTime ' de waarde ' submissionTimestamp ' min **15 minuten** op om ervoor te zorgen dat de opdracht niet fout wordt uitgevoerd.
   
8. Als u antwoord code 201 of 202 ziet, wordt de herstel aanvraag verzonden. 

9. Het maken van de server kan enige tijd duren, afhankelijk van de grootte van de data base en de reken resources die zijn ingericht op de oorspronkelijke server. De herstel status kan worden bewaakt vanuit het activiteiten logboek door te filteren op 
   - **Abonnement** = uw abonnement
   - **Resource type** = Azure database for MySQL servers (micro soft. DBforMySQL/servers) 
   - **Bewerking** = mysql-server maken bijwerken

## <a name="next-steps"></a>Volgende stappen
- Als u een server binnen vijf dagen wilt herstellen en er nog steeds een fout melding wordt weer gegeven nadat u de eerder beschreven stappen hebt uitgevoerd, opent u een ondersteunings incident voor hulp. Als u een verwijderde server na vijf dagen probeert te herstellen, wordt een fout verwacht omdat het back-upbestand niet kan worden gevonden. Open in dit scenario geen ondersteunings ticket. Het ondersteunings team kan geen ondersteuning bieden als de back-up wordt verwijderd uit het systeem. 
- Om het onbedoeld verwijderen van servers te voor komen, raden we u ten zeerste aan [resource vergrendeling](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)te gebruiken.

---
title: Aanmeldings referenties in Azure Stream Analytics taken draaien
description: In dit artikel wordt beschreven hoe u de referenties van invoer-en uitvoer-sinks bijwerkt in Azure Stream Analytics taken.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015229"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Aanmeldings referenties voor invoer en uitvoer van een Stream Analytics-taak draaien

Wanneer u de referenties voor een invoer-of uitvoer van een Stream Analytics taak opnieuw genereert, moet u de taak bijwerken met nieuwe referenties. U moet de taak stoppen voordat u de referenties bijwerkt. u kunt de referenties niet vervangen wanneer de taak wordt uitgevoerd. Stream Analytics ondersteunt het hervatten van een taak uit de laatste uitvoer om de vertraging tussen het stoppen en opnieuw starten van de taak te verminderen. In dit onderwerp wordt het proces beschreven voor het draaien van de aanmeldings referenties en het opnieuw starten van de taak met nieuwe referenties.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Nieuwe referenties opnieuw genereren en uw taak bijwerken met de nieuwe referenties 

In deze sectie leert u hoe u de referenties voor Blob Storage, Event Hubs, SQL Database en Table Storage opnieuw moet genereren. 

### <a name="blob-storagetable-storage"></a>Blob-opslag/-tabel opslag
1. Meld u aan bij de Azure Portal > door het opslag account te bladeren dat u hebt gebruikt als invoer/uitvoer voor de Stream Analytics taak.    
2. Open **toegangs sleutels** in de sectie instellingen. Kies een van de twee standaard sleutels (Key1, Key2) die niet worden gebruikt door uw taak en Genereer deze opnieuw:  
   ![Sleutels voor het opslag account opnieuw genereren](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopieer de zojuist gegenereerde sleutel.    
4. Blader in het Azure Portal door de Stream Analytics-taak > Selecteer **stoppen** en wacht totdat de taak is gestopt.    
5. Zoek de invoer/uitvoer voor BLOB/TABLE-opslag waarvoor u de referenties wilt bijwerken.    
6. Zoek het sleutel veld voor het **opslag account** en plak de zojuist gegenereerde sleutel > Klik op **Opslaan**.    
7. Een verbindings test wordt automatisch gestart wanneer u de wijzigingen opslaat, maar u kunt deze bekijken op het tabblad meldingen. Er zijn twee meldingen: een komt overeen met het opslaan van de update en een andere overeenkomst voor het testen van de verbinding:  
   ![Meldingen na het bewerken van de sleutel](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Ga door met [het starten van de taak in de sectie laatste keer gestopt](#start-your-job-from-the-last-stopped-time) .

### <a name="event-hubs"></a>Event Hubs

1. Meld u aan bij de Azure Portal > door de Event hub te bladeren die u als invoer/uitvoer voor de Stream Analytics taak hebt gebruikt.    
2. Open in de sectie instellingen de optie **beleid voor gedeelde toegang** en selecteer het vereiste toegangs beleid. Kies een van de **primaire** en **secundaire sleutel** die niet wordt gebruikt door uw taak en Genereer deze opnieuw:  
   ![Sleutels opnieuw genereren voor Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopieer de zojuist gegenereerde sleutel.    
4. Blader in het Azure Portal door de Stream Analytics-taak > Selecteer **stoppen** en wacht totdat de taak is gestopt.    
5. Zoek naar de Event hubs-invoer/uitvoer waarvoor u de referenties wilt bijwerken.    
6. Zoek het sleutel veld van de **Event hub-beleid** en plak de zojuist gegenereerde sleutel > Klik op **Opslaan**.    
7. Een verbindings test wordt automatisch gestart wanneer u de wijzigingen opslaat, Controleer of deze is geslaagd.    
8. Ga door met [het starten van de taak in de sectie laatste keer gestopt](#start-your-job-from-the-last-stopped-time) .

### <a name="sql-database"></a>SQL Database

U moet verbinding maken met SQL Database om de aanmeldings referenties van een bestaande gebruiker bij te werken. U kunt referenties bijwerken met behulp van Azure Portal of een hulp programma aan de client zijde, zoals SQL Server Management Studio. In deze sectie wordt het proces voor het bijwerken van referenties met behulp van Azure Portal beschreven.

1. Meld u aan bij de Azure Portal > te bladeren door de SQL database die u als uitvoer voor de Stream Analytics-taak hebt gebruikt.    
2. Van **Data Explorer**, aanmelden/verbinding maken met uw data base > verificatie type selecteren als **SQL server-verificatie** > Typ uw **aanmeldings** -en **wachtwoord** gegevens > Selecteer **OK**.  
   ![Referenties voor SQL Database opnieuw genereren](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Wijzig op het tabblad query het wacht woord voor een van de gebruikers door de volgende query uit te voeren (Vervang door `<user_name>` uw gebruikers naam en `<new_password>` met het nieuwe wacht woord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Noteer het nieuwe wacht woord.    
5. Blader in het Azure Portal door de Stream Analytics-taak > Selecteer **stoppen** en wacht totdat de taak is gestopt.    
6. Zoek de SQL Database uitvoer waarvoor u de referenties wilt draaien. Werk het wacht woord bij en sla de wijzigingen op.    
7. Een verbindings test wordt automatisch gestart wanneer u de wijzigingen opslaat, Controleer of deze is geslaagd.    
8. Ga door met [het starten van de taak in de sectie laatste keer gestopt](#start-your-job-from-the-last-stopped-time) .

### <a name="power-bi"></a>Power BI
1. Meld u aan bij de Azure Portal > door uw Stream Analytics-taak te bladeren > Selecteer **stoppen** en wacht totdat de taak is gestopt.    
2. Ga naar de Power BI uitvoer waarvoor u de referenties wilt vernieuwen > Klik op de **vernieuwings autorisatie** (er wordt een bericht weer gegeven waarin wordt gemeld) > de wijzigingen op te **slaan** .    
3. Er wordt automatisch een verbindings test gestart wanneer u de wijzigingen opslaat, Controleer of deze is geslaagd.    
4. Ga door met [het starten van de taak in de sectie laatste keer gestopt](#start-your-job-from-the-last-stopped-time) .

## <a name="start-your-job-from-the-last-stopped-time"></a>Start uw taak vanaf de laatste keer dat deze is gestopt

1. Ga naar het deel venster **overzicht** van de taak > Selecteer **starten** om de taak te starten.    
2. Selecteer **wanneer de laatste keer** dat deze is gestopt > Klik op **Start**. Houd er rekening mee dat de optie bij laatst gestopt alleen wordt weer gegeven als u de taak eerder hebt uitgevoerd en een bepaalde uitvoer heeft gegenereerd. De taak wordt opnieuw gestart op basis van de tijd van de laatste uitvoer waarde.
   ![De Stream Analytics-taak starten](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
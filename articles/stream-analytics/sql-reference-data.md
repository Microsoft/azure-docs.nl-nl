---
title: SQL Database referentie gegevens gebruiken in een Azure Stream Analytics taak
description: In dit artikel wordt beschreven hoe u een SQL Database gebruikt als referentie gegevens invoer voor een Azure Stream Analytics taak in de Azure Portal en in Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016402"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Referentie gegevens van een SQL Database gebruiken voor een Azure Stream Analytics taak

Azure Stream Analytics ondersteunt Azure SQL Database als invoer bron voor referentie gegevens. U kunt SQL Database als referentie gegevens voor uw Stream Analytics-taak gebruiken in de Azure Portal en in Visual Studio met Stream Analytics-hulpprogram ma's. In dit artikel ziet u hoe u beide methoden kunt uitvoeren.

## <a name="azure-portal"></a>Azure Portal

Gebruik de volgende stappen om Azure SQL Database toe te voegen als een invoer bron voor verwijzingen met behulp van de Azure Portal:

### <a name="portal-prerequisites"></a>Vereisten voor de portal

1. Een Stream Analytics-taak maken.

2. Maak een opslag account dat moet worden gebruikt door de Stream Analytics taak.

3. Maak uw Azure SQL Database met een gegevensset die door de Stream Analytics taak moet worden gebruikt als referentie gegevens.

### <a name="define-sql-database-reference-data-input"></a>Invoer van SQL Database referentie gegevens definiëren

1. Selecteer in uw Stream Analytics-taak de optie **invoer** onder **taak topologie**. Klik op **referentie-invoer toevoegen** en kies **SQL database**.

   ![Invoer is geselecteerd in het navigatie deel venster links. Op invoer is + referentie-invoer toevoegen geselecteerd, waarna een vervolg keuzelijst wordt weer gegeven met de waarden van de Blob-opslag en de SQL Database.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Vul de Stream Analytics invoer configuraties in. Kies de database naam, de server naam, de gebruikers naam en het wacht woord. Als u wilt dat de invoer van referentie gegevens periodiek wordt vernieuwd, kiest u aan om de vernieuwings frequentie in DD: uu: MM op te geven. Als u grote gegevens sets met een kort vernieuwings frequentie hebt, kunt u een [Delta query](sql-reference-data.md#delta-query)gebruiken.

   ![Als SQL Database is geselecteerd, wordt de pagina SQL Database nieuwe invoer weer gegeven. Er bevindt zich een configuratie formulier in het linkerdeel venster en een query voor moment opnamen in het rechterdeel venster.](./media/sql-reference-data/sql-input-config.png)

3. Test de momentopname query in de SQL-query-editor. Zie [de SQL-query editor van het Azure Portal gebruiken om verbinding te maken en gegevens op te vragen](../azure-sql/database/connect-query-portal.md) voor meer informatie

### <a name="specify-storage-account-in-job-config"></a>Geef het opslag account op in de taak configuratie

Navigeer naar **instellingen voor het opslag account** onder **configureren** en selecteer **opslag account toevoegen**.

   ![De instellingen voor het opslag account zijn geselecteerd in het linkerdeel venster. Er bevindt zich een knop opslag account toevoegen in het rechterdeel venster.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Taak starten

Zodra u andere invoer, uitvoer en query hebt geconfigureerd, kunt u de Stream Analytics-taak starten.

## <a name="tools-for-visual-studio"></a>Hulpprogramma's voor Visual Studio

Gebruik de volgende stappen om Azure SQL Database toe te voegen als een invoer bron voor verwijzingen met Visual Studio:

### <a name="visual-studio-prerequisites"></a>Vereisten voor Visual Studio

1. [Installeer de stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md). De volgende versies van Visual Studio worden ondersteund:

   * Visual Studio 2015
   * Visual Studio 2019

2. Vertrouwd raken met de [Stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-quick-create-vs.md) Quick Start.

3. Een opslagaccount maken.

### <a name="create-a-sql-database-table"></a>Een SQL Database tabel maken

Gebruik SQL Server Management Studio om een tabel te maken voor het opslaan van de referentie gegevens. Zie [uw eerste Azure SQL database ontwerpen met behulp van SSMS](../azure-sql/database/design-first-database-tutorial.md) voor meer informatie.

De voorbeeld tabel die in het volgende voor beeld wordt gebruikt, is gemaakt op basis van de volgende instructie:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Kies uw abonnement

1. Selecteer in Visual Studio in het menu **Beeld** de optie **Server Explorer**.

2. Klik met de rechter muisknop op **Azure**, selecteer **verbinding maken met Microsoft Azure abonnement** en meld u aan met uw Azure-account.

### <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Selecteer **Bestand > Nieuw > Project**. 

2. Selecteer **Stream Analytics** in de sjablonenlijst aan de linkerkant en selecteer vervolgens **Azure Stream Analytics Application**. 

3. Voer de project **naam**, **locatie** en naam van de **oplossing** in en selecteer **OK**.

   ![De Stream Analytics sjabloon is geselecteerd, Azure Stream Analytics toepassing is geselecteerd en de vakken naam, locatie en oplossings namen zijn gemarkeerd.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Invoer van SQL Database referentie gegevens definiëren

1. Een nieuwe invoer maken.

   ![Bij nieuw item toevoegen is de optie invoer geselecteerd.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbel klik op **Input.js** in het **Solution Explorer**.

3. Vul de **Stream Analytics invoer configuratie** in. Kies de database naam, de server naam, het vernieuwings type en de vernieuwings frequentie. Geef de vernieuwings frequentie in de notatie op `DD:HH:MM` .

   ![In Stream Analytics invoer configuratie worden waarden in vervolg keuzelijsten ingevoerd of geselecteerd.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Als u ' alleen eenmaal uitvoeren ' of ' uitvoeren periodiek ' kiest, wordt één SQL CodeBehind-bestand met de naam **[invoer alias]. snap shot. SQL** gegenereerd in het project onder de **Input.jsop** het bestands knooppunt.

   ![Het SQL CodeBehind-bestand chemicaliën. snap shot. SQL is gemarkeerd.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Als u ' periodiek vernieuwen met Delta ' kiest, worden er twee SQL CodeBehind-bestanden gegenereerd: **[invoer alias]. snap shot. SQL** en **[invoer alias]. Delta. SQL**.

   ![De SQL CodeBehind-bestanden chemicaliën. Delta. SQL en chemicaliën. snap shot. SQL zijn gemarkeerd.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Open het SQL-bestand in de editor en schrijf de SQL-query.

5. Als u Visual Studio 2019 gebruikt en u SQL Server Data tools hebt geïnstalleerd, kunt u de query testen door te klikken op **uitvoeren**. Er wordt een pop-upvenster weer gegeven om u te helpen bij het maken van een verbinding met SQL Database en het query resultaat wordt onderaan in het venster geopend.

### <a name="specify-storage-account"></a>Opslag account opgeven

Open **JobConfig.jsin** om het opslag account op te geven voor het opslaan van SQL-referentie momentopnamen.

   ![Stream Analytics taak configuratie configureren wordt weer gegeven met standaard waarden. De instellingen voor globale opslag zijn gemarkeerd.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Lokaal testen en implementeren in azure

Voordat u de taak implementeert in azure, kunt u de query logica lokaal testen op Live invoer gegevens. Zie [Live-gegevens lokaal testen met Azure stream Analytics tools for Visual Studio (preview)](stream-analytics-live-data-local-testing.md)voor meer informatie over deze functie. Wanneer u klaar bent met testen, klikt **u op verzenden naar Azure**. Raadpleeg de Snelstartgids [Create a stream Analytics met de Azure stream Analytics-hulpprogram ma's voor Visual Studio voor](stream-analytics-quick-create-vs.md) meer informatie over het starten van de taak.

## <a name="delta-query"></a>Delta query

Wanneer u de Delta query gebruikt, worden [tijdelijke tabellen in Azure SQL database](../azure-sql/temporal-tables.md) aanbevolen.

1. Maak een tijdelijke tabel in Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Maak een auteur van de momentopname query. 

   Gebruik de para meter **\@ snapshotTime** om de stream Analytics runtime te instrueren de referentie gegevensverzameling te verkrijgen van SQL database tijdelijke tabel die op de systeem tijd geldig is. Als u deze para meter niet opgeeft, krijgt u een risico dat er een onnauwkeurige basis referentie gegevens worden ingesteld vanwege de klok scheefheid. Hieronder ziet u een voor beeld van een volledige momentopname query:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Ontwerp de Delta-query. 
   
   Met deze query worden alle rijen in SQL Database opgehaald die zijn ingevoegd of verwijderd in een begin tijd, **\@ deltaStartTime** en een eind tijd **\@ deltaEndTime**. De Delta query moet dezelfde kolommen retour neren als de momentopname query, evenals de kolom **_bewerking_**. Deze kolom definieert of de rij wordt ingevoegd of verwijderd tussen **\@ deltaStartTime** en **\@ deltaEndTime**. De resulterende rijen worden gemarkeerd als **1** als de records zijn ingevoegd, of **2** indien verwijderd. De query moet ook een **water merk** toevoegen uit de SQL Server-zijde om ervoor te zorgen dat alle updates in de Delta periode op de juiste wijze worden vastgelegd. Het gebruik van een Delta query zonder **water merk** kan resulteren in een onjuiste referentie gegevensset.  

   Voor records die zijn bijgewerkt, houdt de tijdelijke tabel boekingen toe door het invoegen en verwijderen van een bewerking. De resultaten van de Delta query worden vervolgens toegepast op de vorige moment opname om de referentie gegevens up-to-date te houden. Stream Analytics Hieronder ziet u een voor beeld van een Delta query:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Houd er rekening mee dat Stream Analytics runtime de momentopname query regel matig kan uitvoeren naast de Delta query voor het opslaan van controle punten.

## <a name="test-your-query"></a>De query testen
   Het is belang rijk om te controleren of uw query de verwachte gegevensset retourneert die de Stream Analytics taak als referentie gegevens zal gebruiken. Als u uw query wilt testen, gaat u naar invoer onder taak topologie in de portal. U kunt vervolgens voorbeeld gegevens selecteren op uw SQL Database referentie-invoer. Nadat het voor beeld beschikbaar is, kunt u het bestand downloaden en controleren of de gegevens die worden geretourneerd, naar verwachting worden weer gegeven. Als u uw ontwikkel-en test herhalingen wilt optimaliseren, kunt u het beste de [Stream Analytics-hulpprogram ma's voor Visual Studio](./stream-analytics-tools-for-visual-studio-install.md)gebruiken. U kunt ook een ander hulp programma van uw voor keur gebruiken om ervoor te zorgen dat de query de juiste resultaten retourneert uit Azure SQL Database en deze vervolgens gebruikt in uw Stream Analytics-taak. 

### <a name="test-your-query-with-visual-studio-code"></a>Uw query testen met Visual Studio code

   Installeer [Azure stream Analytics tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) en [SQL Server (Mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) op Visual Studio code en stel uw ASA-project in. Zie [Quick Start: een Azure stream Analytics-taak maken in Visual Studio code](./quick-create-visual-studio-code.md) en de [zelf studie over de SQL Server (Mssql)-extensie](/sql/tools/visual-studio-code/sql-server-develop-use-vscode)voor meer informatie.

1. Configureer de invoer van SQL-referentie gegevens.
   
   ![Een Visual Studio code-editor (tabblad) toont ReferenceSQLDatabase.jsop.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Selecteer het SQL Server pictogram en klik op **verbinding toevoegen**.
   
   ![+ Verbinding toevoegen wordt weer gegeven in het linkerdeel venster en is gemarkeerd.](./media/sql-reference-data/add-sql-connection.png)

3. Vul de verbindings gegevens in.
   
   ![De twee vakken voor data base-en server informatie zijn gemarkeerd.](./media/sql-reference-data/fill-connection-information.png)

4. Klik met de rechter muisknop in referentie SQL en selecteer **query uitvoeren**.
   
   ![Query uitvoeren is gemarkeerd in het context menu.](./media/sql-reference-data/execute-query.png)

5. Kies uw verbinding.
   
   ![Het dialoog venster bevat de tekst "een verbindings profiel maken in de onderstaande lijst" en de lijst heeft één vermelding: hightlighted.](./media/sql-reference-data/choose-connection.png)

6. Controleer en controleer het query resultaat.
   
   ![De zoek resultaten van de query bevinden zich op het tabblad versus code-editor.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Veelgestelde vragen

**Worden er extra kosten in rekening gebracht met behulp van SQL-referentie gegevens invoer in Azure Stream Analytics?**

Er zijn geen extra [kosten per streaming-eenheid](https://azure.microsoft.com/pricing/details/stream-analytics/) in de stream Analytics taak. De Stream Analytics-taak moet echter een gekoppeld Azure-opslag account hebben. Met de Stream Analytics taak wordt een query uitgevoerd op de SQL-data base (tijdens het begin-en Vernieuwings interval van de taak) om de gegevensset op te halen en de moment opname op te slaan in het opslag account Als u deze moment opnamen opslaat, worden er extra kosten in rekening gebracht op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/) voor Azure Storage-account.

**Hoe kan ik weet u dat er een query wordt uitgevoerd op een moment opname van de referentie gegevens vanuit SQL data base en wordt gebruikt in de Azure Stream Analytics-taak?**

Er zijn twee metrische gegevens die worden gefilterd op logische naam (onder metrische gegevens Azure Portal) die u kunt gebruiken om de status van de SQL Database referentie gegevens invoer te controleren.

   * InputEvents: deze metrische waarde meet het aantal records dat is geladen in vanuit de SQL Database referentie gegevensset.
   * InputEventBytes: deze meet waarde meet de grootte van de moment opname van de referentie gegevens die in het geheugen van de Stream Analytics taak wordt geladen. 

De combi natie van beide metrische gegevens kan worden gebruikt om te afleiden als de taak wordt doorzocht SQL Database om de referentie gegevensverzameling op te halen en deze vervolgens te laden in het geheugen.

**Moet ik een speciaal type Azure SQL Database?**

Azure Stream Analytics werkt met elk type Azure SQL Database. Het is echter belang rijk om te begrijpen dat de vernieuwings frequentie die is ingesteld voor de invoer van de referentie gegevens, van invloed kan zijn op de belasting van uw query. Als u de optie Delta-query wilt gebruiken, is het raadzaam om tijdelijke tabellen in Azure SQL Database te gebruiken.

**Waarom slaat Azure Stream Analytics moment opnamen op in Azure Storage-account?**

Stream Analytics garandeert Exactly-once-gebeurtenissenverwerking Least-once-levering van gebeurtenissen. In gevallen waarin tijdelijke problemen invloed hebben op uw taak, is een kleine hoeveelheid replay nodig om de status te herstellen. Als u het opnieuw afspelen wilt inschakelen, moeten deze moment opnamen zijn opgeslagen in een Azure Storage-account. Zie voor meer informatie over het opnieuw afspelen van het controle punt [en concepten voor opnieuw afspelen in azure stream Analytics taken](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Volgende stappen

* [Referentie gegevens gebruiken voor Zoek opdrachten in Stream Analytics](stream-analytics-use-reference-data.md)
* [Snelstart: Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio (preview)](stream-analytics-live-data-local-testing.md)
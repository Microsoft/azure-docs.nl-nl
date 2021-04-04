---
title: Een service voor splitsen en samenvoegen implementeren
description: Gebruik de Split-Merge-functie om gegevens te verplaatsen tussen Shard-data bases.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792069"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Een service voor splitsen en samen voegen implementeren om gegevens te verplaatsen tussen Shard-data bases
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met het gereedschap splitsen en samen voegen kunt u gegevens verplaatsen tussen Shard-data bases. Zie [gegevens verplaatsen tussen uitgeschaalde Cloud databases](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>De Split-Merge-pakketten downloaden

1. Down load de nieuwste versie van NuGet van [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Open een opdracht prompt en ga naar de map waarin u nuget.exe hebt gedownload. De down load bevat Power shell-opdrachten.

1. Down load het nieuwste Split-Merge-pakket naar de huidige map met de onderstaande opdracht:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

De bestanden worden geplaatst in een map met de naam **micro soft. Azure. SqlDatabase. ElasticScale. service. SplitMerge. x. x. xxx. x** waarbij *x. x. xxx. x* het versie nummer aangeeft. Zoek de service bestanden voor splitsen en samen voegen in de **content\splitmerge\service** -submap en de Split-Merge Power shell-scripts (en de vereiste client-dll's) in de **content\splitmerge\powershell** -submap.

## <a name="prerequisites"></a>Vereisten

1. Maak een Azure SQL Database-data base die wordt gebruikt als de Data Base voor splitsen en samen voegen. Ga naar de [Azure Portal](https://portal.azure.com). Maak een nieuwe **SQL database**. Geef de Data Base een naam en maak een nieuwe beheerder en een nieuw wacht woord. Zorg ervoor dat u de naam en het wacht woord noteert voor later gebruik.

1. Zorg ervoor dat de server met Azure-Services verbinding kan maken. Zorg er in de portal in de **firewall instellingen** voor dat de instelling **toegang tot Azure-Services toestaan** is ingesteld op **aan**. Klik op het pictogram opslaan.

1. Maak een Azure Storage-account voor diagnostische uitvoer.

1. Maak een Azure-Cloud service voor uw Split-Merge service.

## <a name="configure-your-split-merge-service"></a>Uw Split-Merge-service configureren

### <a name="split-merge-service-configuration"></a>Configuratie van Split-Merge-service

1. Maak in de map waarin u de Split-Merge assembly's hebt gedownload een kopie van het bestand *ServiceConfiguration. Temp late. cscfg* dat naast *SplitMergeService. cspkg* is geleverd en wijzig de naam *ServiceConfiguration. cscfg*.

1. Open *ServiceConfiguration. cscfg* in een tekst editor, zoals Visual Studio, waarmee invoer wordt gevalideerd, zoals de indeling van certificaat vingerafdrukken.

1. Maak een nieuwe data base of kies een bestaande data base die u als de status database voor Split-Merge bewerkingen wilt gebruiken en de connection string van die Data Base wilt ophalen.

   > [!IMPORTANT]
   > Op dit moment moet de status database de Latijnse sortering (SQL \_ Latin1 \_ General \_ CP1 \_ CI \_ as) gebruiken. Zie [Windows Collation name (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql)voor meer informatie.

   Met Azure SQL Database heeft de connection string doorgaans de volgende vorm:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Voer deze connection string in het *cscfg* -bestand in zowel de **SplitMergeWeb** -als **SplitMergeWorker** -rollen in de instelling ElasticScaleMetadata.

1. Voer voor de rol **SplitMergeWorker** een geldige connection string in voor Azure Storage voor de instelling **WorkerRoleSynchronizationStorageAccountConnectionString** .

### <a name="configure-security"></a>Beveiliging configureren

Raadpleeg de [beveiligings configuratie voor splitsen en samen voegen](elastic-scale-split-merge-security-configuration.md)voor gedetailleerde instructies voor het configureren van de beveiliging van de service.

Voor het doel van een eenvoudige test implementatie voor deze zelf studie wordt een minimale set configuratie stappen uitgevoerd om de service actief te maken. Met deze stappen wordt alleen de ene computer/het account ingeschakeld die deze uitvoert om te communiceren met de service.

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Maak een nieuwe map en voer vanuit deze map de volgende opdracht uit met behulp [van een opdracht prompt voor ontwikkel aars voor Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) -venster:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

U wordt gevraagd om een wacht woord om de persoonlijke sleutel te beveiligen. Voer een sterk wacht woord in en bevestig dit. Vervolgens wordt u gevraagd het wacht woord nog een keer te gebruiken. Klik op **Ja** aan het einde om het te importeren in het basis archief Vertrouwde certificerings instanties.

### <a name="create-a-pfx-file"></a>Een PFX-bestand maken

Voer de volgende opdracht uit vanuit hetzelfde venster waarin makecert is uitgevoerd. Gebruik hetzelfde wacht woord dat u hebt gebruikt om het certificaat te maken:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importeer het client certificaat in het persoonlijke archief

1. Dubbel klik in Windows Verkenner op *MyCert. pfx*.
2. Selecteer in de **wizard Certificaat importeren** de optie **huidige gebruiker** en klik op **volgende**.
3. Bevestig het bestandspad en klik op **volgende**.
4. Typ het wacht woord, behoud **alle uitgebreide eigenschappen zijn** ingeschakeld en klik op **volgende**.
5. **Selecteer het certificaat archief [...] niet automatisch selecteren** en klik op **volgende**.
6. Klik op **volt ooien** en **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Upload het PFX-bestand naar de Cloud service

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Selecteer **Cloud Services**.
3. Selecteer de Cloud service die u hierboven hebt gemaakt voor de service voor splitsen en samen voegen.
4. Klik op **certificaten** in het bovenste menu.
5. Klik op **uploaden** in de onderste balk.
6. Selecteer het PFX-bestand en voer hetzelfde wacht woord in als hierboven.
7. Als u klaar bent, kopieert u de vinger afdruk van het certificaat uit de nieuwe vermelding in de lijst.

### <a name="update-the-service-configuration-file"></a>Het service configuratie bestand bijwerken

Plak de gekopieerde certificaat vingerafdruk hierboven in het kenmerk vinger afdruk/waarde van deze instellingen.
Voor de worker-rol:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Voor de Web-rol:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Houd er rekening mee dat voor productie-implementaties afzonderlijke certificaten moeten worden gebruikt voor de CA, voor versleuteling, het server certificaat en client certificaten. Zie voor gedetailleerde instructies voor [Beveiliging configuratie](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Uw service implementeren

1. Ga naar de [Azure-portal](https://portal.azure.com)
2. Selecteer de Cloud service die u eerder hebt gemaakt.
3. Klik op **Overzicht**.
4. Kies de faserings omgeving en klik vervolgens op **uploaden**.
5. Voer in het dialoog venster een implementatie label in. Voor zowel ' package ' als ' configuratie ' klikt u op ' van lokale ' en kiest u het bestand *SplitMergeService. cspkg* en uw cscfg-bestand dat u eerder hebt geconfigureerd.
6. Zorg ervoor dat het selectie vakje is gemarkeerd **als geïmplementeerd, zelfs als een of meer functies één exemplaar bevatten** wordt gecontroleerd.
7. Klik op de knop met het maat streepje in de rechter benedenhoek om te beginnen met de implementatie. Het duurt enkele minuten voordat de bewerking is voltooid.

## <a name="troubleshoot-the-deployment"></a>Problemen met de implementatie oplossen

Als uw webfunctie niet online is, is er waarschijnlijk een probleem met de beveiligings configuratie. Controleer of TLS/SSL is geconfigureerd zoals hierboven wordt beschreven.

Als uw werknemersrol niet online kan worden gebracht, maar uw webrole slaagt, is er waarschijnlijk een probleem met het maken van verbinding met de status database die u eerder hebt gemaakt.

- Zorg ervoor dat de connection string in uw cscfg nauw keurig is.
- Controleer of de server en de data base bestaan en of de gebruikers-id en het wacht woord juist zijn.
- Voor Azure SQL Database moet het connection string de volgende indeling hebben:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Zorg ervoor dat de server naam niet begint met **https://**.
- Zorg ervoor dat de server met Azure-Services verbinding kan maken. U doet dit door uw data base te openen in de portal en ervoor te zorgen dat de instelling **toegang tot Azure-Services toestaan** is ingesteld op * * op * * * *.

## <a name="test-the-service-deployment"></a>De service-implementatie testen

### <a name="connect-with-a-web-browser"></a>Verbinding maken met een webbrowser

Bepaal het web-eind punt van uw Split-Merge service. U kunt dit vinden in de portal door naar het **overzicht** van uw Cloud service te gaan en aan de rechter kant onder **site-URL** te kijken. Vervang **http://** door **https://** omdat de standaard beveiligings instellingen het http-eind punt uitschakelen. Laad de pagina voor deze URL in uw browser.

### <a name="test-with-powershell-scripts"></a>Testen met Power shell-scripts

De implementatie en uw omgeving kunnen worden getest door de meegeleverde Power shell-voorbeeld scripts uit te voeren.

> [!IMPORTANT]
> De voorbeeld scripts worden uitgevoerd op Power shell 5,1. Ze worden op dit moment niet uitgevoerd op Power shell 6 of hoger.

De volgende script bestanden zijn opgenomen:

1. *SetupSampleSplitMergeEnvironment.ps1* -een gegevenslaag voor een test instellen voor splitsen/samen voegen (Zie de tabel hieronder voor een gedetailleerde beschrijving)
2. *ExecuteSampleSplitMerge.ps1* -test bewerkingen uitvoeren op de gegevenslaag van de test (Zie de tabel hieronder voor een gedetailleerde beschrijving)
3. *GetMappings.ps1* -voorbeeld script op het hoogste niveau waarmee de huidige status van de Shard-toewijzingen wordt afgedrukt.
4. *ShardManagement. psm1*  -hulp script dat de SHARDMANAGEMENT-API verpakt
5. *SqlDatabaseHelpers. psm1* -hulp script voor het maken en beheren van data bases in SQL database

   <table style="width:100%">
     <tr>
       <th>Power shell-bestand</th>
       <th>Stappen</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Hiermee maakt u een Shard-toewijzings beheer database</td>
     </tr>
     <tr>
       <td>2. Maakt 2 Shard-data bases.
     </tr>
     <tr>
       <td>3. Hiermee maakt u een Shard-kaart voor deze data bases (Hiermee worden bestaande Shard-kaarten voor deze data bases verwijderd). </td>
     </tr>
     <tr>
       <td>4. Hiermee maakt u een kleine voorbeeld tabel in beide Shards en vult u de tabel in een van de Shards.</td>
     </tr>
     <tr>
       <td>5. Declareert de waarde voor de Shard-tabel.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Power shell-bestand</th>
       <th>Stappen</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Hiermee verzendt u een splits aanvraag naar de webfrontend van de Split-Merge-service, die de helft van de gegevens van de eerste Shard naar de tweede Shard splitst.</td>
     </tr>
     <tr>
       <td>2. Hiermee wordt de web-frontend gecontroleerd op de status van de gesplitste aanvraag en wordt gewacht totdat de aanvraag is voltooid.</td>
     </tr>
     <tr>
       <td>3. Hiermee verzendt u een aanvraag voor samen voegen naar de web-frontend van de Split-Merge-service, waarmee de gegevens van de tweede Shard terug worden verplaatst naar de eerste Shard.</td>
     </tr>
     <tr>
       <td>4. Hiermee wordt de web-frontend voor de status van de samenvoeg aanvraag gecontroleerd en wordt gewacht totdat de aanvraag is voltooid.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Power shell gebruiken om uw implementatie te controleren

1. Open een nieuw Power shell-venster en navigeer naar de map waar u het Split-Merge-pakket hebt gedownload en navigeer vervolgens naar de map Power shell.

2. Maak een server (of kies een bestaande server) waar Shard-toewijzings beheer en Shards worden gemaakt.

   > [!NOTE]
   > Het *SetupSampleSplitMergeEnvironment.ps1* script maakt standaard al deze data bases op dezelfde server, zodat het script eenvoudig te gebruiken is. Dit is geen beperking van de Split-Merge-service zelf.

   Er is een SQL-verificatie aanmelding met lees-/schrijftoegang tot de Db's vereist voor de Split-Merge-service om gegevens te verplaatsen en de Shard-kaart bij te werken. Omdat de Split-Merge-service in de Cloud wordt uitgevoerd, ondersteunt deze momenteel geen geïntegreerde verificatie.

   Zorg ervoor dat de server is geconfigureerd voor toegang vanaf het IP-adres van de computer waarop deze scripts worden uitgevoerd. U kunt deze instelling vinden onder SQL Server/firewalls en virtuele netwerken/client-IP-adressen.

3. Voer het *SetupSampleSplitMergeEnvironment.ps1* script uit om de voorbeeld omgeving te maken.

   Als u dit script uitvoert, worden alle bestaande Shard-toewijzings beheer gegevens structuren in de Shard-toewijzings beheer database en de Shards gewist. Het kan handig zijn om het script opnieuw uit te voeren als u de Shard-kaart of Shards opnieuw wilt initialiseren.

   Voor beeld van opdracht regel:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Voer het Getmappings.ps1 script uit om de toewijzingen weer te geven die momenteel bestaan in de voorbeeld omgeving.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Voer het *ExecuteSampleSplitMerge.ps1* script uit om een splits bewerking uit te voeren (het verplaatsen van de helft van de gegevens op de eerste Shard naar de tweede Shard) en vervolgens een samenvoeg bewerking (waarbij de gegevens weer worden verplaatst naar de eerste Shard). Als u TLS hebt geconfigureerd en het http-eind punt is uitgeschakeld, controleert u of u in plaats daarvan het https://-eind punt gebruikt.

    Voor beeld van opdracht regel:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Als de onderstaande fout wordt weer gegeven, is er waarschijnlijk een probleem met het certificaat van uw webeindpunt. Probeer verbinding te maken met het eind punt van het web met uw favoriete webbrowser en controleer of er een certificaat fout is opgetreden.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    Als dat is gelukt, ziet de uitvoer er als volgt uit:

    ```output
    > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
    > Sending split request
    > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
    > Polling split-merge request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    > Sending merge request
    > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
    > Polling request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    >
    ```

6. Experimenteer met andere gegevens typen. Al deze scripts hebben een optionele para meter-ShardKeyType waarmee u het sleutel type kunt opgeven. De standaard waarde is Int32, maar u kunt ook Int64, GUID of binary opgeven.

## <a name="create-requests"></a>Aanvragen maken

De service kan worden gebruikt met behulp van de webgebruikersinterface of door de Power shell-module SplitMerge. psm1 te importeren en te gebruiken, waarmee uw aanvragen via de webfunctie worden verzonden.

De service kan gegevens in zowel Shard-tabellen als referentie tabellen verplaatsen. Een Shard-tabel heeft een sharding-sleutel kolom en heeft verschillende rijgegevens op elke Shard. Een verwijzings tabel is niet Shard, zodat deze dezelfde rijgegevens op elke Shard bevat. Verwijzings tabellen zijn handig voor gegevens die niet vaak veranderen en worden gebruikt om samen te voegen met Shard-tabellen in query's.

Als u een bewerking voor splitsen en samen voegen wilt uitvoeren, moet u de Shard-tabellen en verwijzings tabellen declareren die u wilt verplaatsen. Dit wordt bereikt met de **waarde** -API. Deze API bevindt zich in de naam ruimte **micro soft. Azure. SqlDatabase. ElasticScale. ShardManagement. schema** .

1. Maak voor elke Shard-tabel een **ShardedTableInfo** -object dat de bovenliggende schema naam van de tabel beschrijft (optioneel, standaard ingesteld op ' dbo '), de tabel naam en de naam van de kolom in die tabel die de sharding-sleutel bevat.
2. Maak voor elke verwijzings tabel een **ReferenceTableInfo** -object dat de naam van het bovenliggende schema van de tabel beschrijft (optioneel, standaard ingesteld op ' dbo ') en de tabel naam.
3. Voeg de bovenstaande TableInfo-objecten toe aan een nieuw **waarde** -object.
4. Een verwijzing naar een **ShardMapManager** -object ophalen en **GetSchemaInfoCollection** aanroepen.
5. Voeg de **waarde** toe aan de **SchemaInfoCollection**, waarbij u de naam van de Shard-toewijzing opgeeft.

Een voor beeld hiervan is te zien in het SetupSampleSplitMergeEnvironment.ps1 script.

De Split-Merge-service maakt de doel database (of het schema voor tabellen in de data base) niet voor u. Deze moeten vooraf worden gemaakt voordat een aanvraag naar de service wordt verzonden.

## <a name="troubleshooting"></a>Problemen oplossen

Het onderstaande bericht wordt weer gegeven bij het uitvoeren van de Power shell-voorbeeld scripts:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Deze fout houdt in dat uw TLS/SSL-certificaat niet correct is geconfigureerd. Volg de instructies in de sectie verbinding maken met een webbrowser.

Als u geen aanvragen kunt indienen, ziet u mogelijk het volgende:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Controleer in dit geval het configuratie bestand, met name de instelling voor **WorkerRoleSynchronizationStorageAccountConnectionString**. Deze fout geeft meestal aan dat de werk rollen de meta gegevens database niet kan initialiseren bij het eerste gebruik.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
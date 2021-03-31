---
title: Inzicht in periodieke back-upconfiguratie
description: Gebruik Service Fabric periodieke functie voor back-up en herstel om periodieke back-ups te configureren van uw betrouw bare stateful Services of Reliable Actors.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018882"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Informatie over periodieke back-upconfiguratie in azure Service Fabric

Het configureren van periodieke back-ups van uw betrouw bare stateful Services of Reliable Actors bestaat uit de volgende stappen:

1. **Back-upbeleid maken**: in deze stap worden een of meer back-beleidsregels gemaakt, afhankelijk van de vereisten.

2. **Back-up inschakelen**: in deze stap koppelt u het back-upbeleid dat u in **stap 1** hebt gemaakt aan de vereiste entiteiten, _toepassing_, _service_ of _partitie_.

## <a name="create-backup-policy"></a>Back-upbeleid maken

Een back-upbeleid bestaat uit de volgende configuraties:

* **Gegevens verlies automatisch herstellen**: Hiermee geeft u op of herstellen automatisch moet worden geactiveerd met de meest recente beschik bare back-up voor het geval de partitie een gegevens verlies gebeurtenis ondervindt.
> [!NOTE]
> U kunt het beste geen automatisch herstel instellen in productie clusters
>

* **Maximale incrementele back-ups**: Hiermee definieert u het maximum aantal incrementele back-ups dat moet worden gemaakt tussen twee volledige back-ups. Maximale incrementele back-ups geven de bovengrens aan. Er kan een volledige back-up worden gemaakt voordat het opgegeven aantal incrementele back-ups in een van de volgende voor waarden is voltooid

    1. De replica heeft nooit een volledige back-up gemaakt, omdat deze primair is geworden.

    2. Enkele logboek records sinds de laatste back-up zijn afgekapt.

    3. De MaxAccumulatedBackupLogSizeInMB-limiet door de replica is door gegeven.

* **Back-upschema**: de tijd of frequentie waarmee periodieke back-ups worden gemaakt. Eén kan back-ups plannen die periodiek worden gepland op het opgegeven interval of op een vaste tijd dagelijks/wekelijks.

    1. **Back-upschema op basis van frequentie**: dit schema type moet worden gebruikt als de gegevens back-up op vaste intervallen moet worden uitgevoerd. Het gewenste tijds interval tussen twee opeenvolgende back-ups wordt gedefinieerd met behulp van de ISO8601-indeling. Back-upschema op basis van frequentie ondersteunt interval omzetting tot de minuut.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Back-upschema op basis van tijd**: dit schema type moet worden gebruikt als de nood zaak is om gegevens back-up te maken op specifieke tijdstippen van de dag of week. Het frequentie type van de planning kan dagelijks of wekelijks zijn.
        1. **_Dagelijks_ back-upschema op basis van tijd**: dit schema type moet worden gebruikt als de nood zaak is om gegevens back-up te maken op specifieke tijdstippen van de dag. Als u dit wilt opgeven, stelt u deze in op `ScheduleFrequencyType` _dagelijks_; en stelt `RunTimes` u in op een lijst met de gewenste tijd op de dag in iso8601-notatie, de datum die wordt opgegeven samen met de tijd, wordt genegeerd. Dit is bijvoorbeeld `0001-01-01T18:00:00` _6:00 pm_ dagelijks, waarbij datum deel _0001-01-01_ wordt genegeerd. Hieronder ziet u een voor beeld van de configuratie om dagelijks een back-up te activeren om _9:00 uur_ en _6:00 uur_ per dag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Wekelijks_ back-upschema op basis van tijd**: dit schema type moet worden gebruikt als de nood zaak is om gegevens back-up te maken op specifieke tijdstippen van de dag. Als u dit wilt opgeven, stelt u in op `ScheduleFrequencyType` _wekelijks_; ingesteld `RunDays` op lijst met dagen in een week wanneer de back-up moet worden geactiveerd en moet worden ingesteld `RunTimes` op een lijst met gewenste tijdstippen gedurende de dag in iso8601-notatie, de datum die wordt opgegeven samen met de tijd, wordt genegeerd. Lijst van dagen van een week waarop de periodieke back-up moet worden geactiveerd. Hieronder ziet u een voor beeld van de configuratie om dagelijks een back-up te activeren op _9:00 uur_ en _6:00 uur_ tijdens maandag tot en met vrijdag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Back-upopslag**: Hiermee geeft u de locatie op voor het uploaden van back-ups. Opslag kan een Azure Blob Store of een bestands share zijn.
    1. **Azure Blob Store**: dit opslag type moet worden geselecteerd wanneer het nodig is om gegenereerde back-ups op te slaan in Azure. Zowel _zelfstandige_ als _op Azure gebaseerde_ clusters kunnen gebruikmaken van dit opslag type. De beschrijving voor dit opslag type vereist connection string en de naam van de container waarnaar de back-ups moeten worden geüpload. Als de container met de opgegeven naam niet beschikbaar is, wordt deze gemaakt tijdens het uploaden van een back-up.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Backup Restore-service werkt niet met v1 Azure Storage
        >

    2. **Bestands share**: dit opslag type moet worden geselecteerd voor _zelfstandige_ clusters wanneer het nodig is om gegevens back-up on-premises op te slaan. Voor de beschrijving voor dit opslag type is het pad van de bestands share vereist waar de back-ups moeten worden geüpload. Toegang tot de bestands share kan worden geconfigureerd met een van de volgende opties:
        1. _Geïntegreerde Windows-verificatie_, waarbij de toegang tot de bestands share wordt gegeven aan alle computers die deel uitmaken van het service Fabric cluster. In dit geval stelt u de volgende velden in voor het configureren van een back-upopslag op basis van een _Bestands share_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. De _Bestands share beveiligen met behulp van de gebruikers naam en het wacht woord_, waarbij de toegang tot de bestands share wordt gegeven aan specifieke gebruikers. Bestands share Storage-specificatie biedt ook de mogelijkheid om een secundaire gebruikers naam en een secundair wacht woord op te geven om back-ups te kunnen maken bij een mislukte verificatie met de primaire gebruikers naam en het primaire wacht woord. In dit geval stelt u de volgende velden in voor het configureren van een back-upopslag op basis van een _Bestands share_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Zorg ervoor dat de betrouw baarheid van de opslag voldoet aan de betrouwbaarheids vereisten van back-upgegevens.
>

* **Bewaar beleid**: Hiermee geeft u het beleid voor het bewaren van back-ups in de geconfigureerde opslag. Alleen Basic-Bewaar beleid wordt ondersteund.
    1. **Basis beleid voor bewaren**: met dit Bewaar beleid kunt u optimaal gebruikmaken van de opslag capaciteit door back-upbestanden te verwijderen die niet meer nodig zijn. `RetentionDuration` kan worden opgegeven voor het instellen van de tijds Panne waarvoor back-ups moeten worden bewaard in de opslag. `MinimumNumberOfBackups` is een optionele para meter die kan worden opgegeven om ervoor te zorgen dat het opgegeven aantal back-ups altijd worden bewaard, ongeacht het `RetentionDuration` . Hieronder ziet u een voor beeld van de configuratie voor het bewaren van back-ups gedurende _10_ dagen en het aantal back-ups dat niet meer dan _20_ mag worden gebruikt.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat u het back-upbeleid hebt gedefinieerd om te voldoen aan de vereisten voor gegevens back-ups, moet het back-upbeleid op de juiste manier zijn gekoppeld aan een _toepassing_, of _service_ of een _partitie_.

> [!NOTE]
> Zorg ervoor dat er geen toepassings upgrades worden uitgevoerd voordat u back-ups inschakelt
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Hiërarchische doorgifte van het back-upbeleid
In Service Fabric is de relatie tussen de toepassing, de service en de partities hiërarchisch zoals uitgelegd in het [toepassings model](./service-fabric-application-model.md). Het back-upbeleid kan worden gekoppeld aan een _toepassing_, _service_ of een _partitie_ in de hiërarchie. Het back-upbeleid wordt hiërarchisch door gegeven aan het volgende niveau. Ervan uitgaande dat er slechts één back-upbeleid is gemaakt en gekoppeld aan een _toepassing_, worden alle stateful partities die deel uitmaken van alle _betrouw bare stateful Services_ en _reliable actors_ van de _toepassing_ , met behulp van het back-upbeleid, gemaakt. Of als het back-upbeleid is gekoppeld aan een _betrouw bare stateful service_, wordt er een back-up van alle partities gemaakt met behulp van het back-upbeleid.

### <a name="overriding-backup-policy"></a>Back-upbeleid overschrijven
Er is mogelijk een scenario waarbij gegevens back-up met hetzelfde back-upschema is vereist voor alle services van de toepassing, met uitzonde ring van specifieke services waarbij gegevens back-ups moeten worden gemaakt met een hogere frequentie planning of als back-up naar een ander opslag account of bestands share. Om dergelijke scenario's te verhelpen, biedt back Restore service de mogelijkheid om het door gegeven beleid te overschrijven op service-en partitie bereik. Wanneer het back-upbeleid is gekoppeld aan _service_ of _partitie_, overschrijft het het door gegeven back-upbeleid, indien aanwezig.

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt het installatie programma gebruikt met twee toepassingen, _MyApp_A_ en _MyApp_B_. Toepassings _MyApp_A_ bevat twee betrouw bare stateful Services, _SvcA1_  &  _SvcA3_ en één reliable actor service, _ActorA2_. _SvcA1_ bevat drie partities terwijl _ActorA2_ en _SvcA3_ twee partities bevatten.  Toepassings _MyApp_B_ bevat drie betrouw bare stateful Services, _SvcB1_, _SvcB2_ en _SvcB3_. _SvcB1_ en _SvcB2_ bevatten twee partities, terwijl _SvcB3_ drie partities bevat.

Stel dat deze toepassingen gegevens back-ups als volgt zijn

1. MyApp_A
    1. Maak dagelijks een back-up van de gegevens voor alle partities van alle _betrouw bare stateful Services_ en _reliable actors_ die bij de toepassing horen. Upload back-upgegevens naar locatie _BackupStore1_.

    2. Voor een van de services, _SvcA3_, is elk uur een back-up van gegevens vereist.

    3. De gegevens grootte in de partitie _SvcA1_P2_ is meer dan verwacht en de back-upgegevens moeten worden opgeslagen op een andere opslag locatie _BackupStore2_.

2. MyApp_B
    1. Maak elke zondag om 8:00 uur een back-up van de gegevens voor alle partities van de _SvcB1_ -service. Upload back-upgegevens naar locatie _BackupStore1_.

    2. Maak een back-up van gegevens elke dag om 8:00 uur voor partitie _SvcB2_P1_. Upload back-upgegevens naar locatie _BackupStore1_.

Om deze vereisten voor gegevens back-ups op te lossen, worden er back-upbeleid BP_1 naar BP_5 gemaakt en wordt de back-up als volgt ingeschakeld.
1. MyApp_A
    1. Maak een back-upbeleid _BP_1_, met een frequentie-gebaseerd back-upschema waarbij de frequentie is ingesteld op 24 uur. back-upopslag is geconfigureerd voor het gebruik van opslag locatie _BackupStore1_. Schakel dit beleid in voor toepassings _MyApp_A_ met de API voor het [inschakelen van back-ups van toepassingen](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) . Met deze actie wordt de back-up van gegevens met behulp van back-upbeleid _BP_1_ voor alle partities van _betrouw bare stateful Services_ en _reliable actors_ die tot toepassings _MyApp_A_ behoren.

    2. Maak een back-upbeleid _BP_2_, met een frequentie-gebaseerd back-upschema waarbij de frequentie is ingesteld op 1 uur. back-upopslag is geconfigureerd voor het gebruik van opslag locatie _BackupStore1_. Schakel dit beleid in voor service _SvcA3_ met behulp van de API voor het [inschakelen van back-ups](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Met deze actie wordt het door gegeven beleid overschreven _BP_1_ door expliciet ingeschakelde back-upbeleid _BP_2_ voor alle _SvcA3_ van gegevens back-ups met behulp van back-upbeleid _BP_2_ voor deze partities.

    3. Maak een back-upbeleid _BP_3_, met een frequentie-gebaseerd back-upschema waarbij de frequentie is ingesteld op 24 uur. back-upopslag is geconfigureerd voor het gebruik van opslag locatie _BackupStore2_. Schakel dit beleid in voor partitie _SvcA1_P2_ met behulp van de API voor het [maken van partities](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Deze actie overschrijft de door gegeven beleids _BP_1_ door expliciet ingeschakelde back-upbeleid _BP_3_ voor partitie _SvcA1_P2_.

2. MyApp_B
    1. Maak een back-upbeleid, _BP_4_, met een op tijd gebaseerd back-upschema waarbij het type plannings frequentie is ingesteld op wekelijks, uitvoerings dagen is ingesteld op zondag en uitvoerings tijden is ingesteld op 8:00 uur. Back-upopslag is geconfigureerd om opslag locatie _BackupStore1_ te gebruiken. Schakel dit beleid in voor service _SvcB1_ met behulp van de API voor het [inschakelen van back-ups](/rest/api/servicefabric/sfclient-api-enableservicebackup) . Met deze actie wordt back-up van gegevens met behulp van back-upbeleid _BP_4_ voor alle partities van de service _SvcB1_.

    2. Maak een back-upbeleid _BP_5_, met een op tijd gebaseerd back-upschema waarbij het type plannings frequentie is ingesteld op dagelijks en uitvoerings tijden is ingesteld op 8:00 uur. Back-upopslag is geconfigureerd om opslag locatie _BackupStore1_ te gebruiken. Schakel dit beleid in voor partitie _SvcB2_P1_ met behulp van de API voor het [maken van partities](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Met deze actie wordt de back-up van gegevens met behulp van back-upbeleid _BP_5_ voor partitie _SvcB2_P1_.

In het volgende diagram ziet u expliciet ingeschakeld back-upbeleid en door gegeven back-upbeleid.

![Toepassings hiërarchie Service Fabric][0]

## <a name="disable-backup"></a>Back-up uitschakelen
Back-upbeleid kan worden uitgeschakeld wanneer er geen back-up van gegevens nodig is. Back-upbeleid dat op een _toepassing_ is ingeschakeld kan alleen worden uitgeschakeld op dezelfde _toepassing_ met behulp van de API voor het uitschakelen van de [back-up](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) van de toepassing. het back-upbeleid dat op een _service_ is ingeschakeld kan worden uitgeschakeld _op dezelfde_ _service_ met behulp van de API voor het uitschakelen van back [-UPS](/rest/api/servicefabric/sfclient-api-disableservicebackup) en het [back-](/rest/api/servicefabric/sfclient-api-disablepartitionbackup) upbeleid dat op een _partitie_ wordt ingeschakeld, kan

* Het uitschakelen van het back-upbeleid voor een _toepassing_ stopt alle periodieke gegevens back-ups als gevolg van het door geven van het back-upbeleid tot betrouw bare stateful service partities of betrouw bare actor-partities.

* Het uitschakelen van het back-upbeleid voor een _service_ stopt alle periodieke gegevens back-ups wanneer dit back-upbeleid wordt door gegeven aan de partities van de _service_.

* Als u het back-upbeleid voor een _partitie_ uitschakelt, wordt alle periodieke gegevens back-up gestopt vanwege het back-upbeleid op de partitie.

* Bij het uitschakelen van de back-up voor een entiteit (toepassing/service/partitie), `CleanBackup` kan worden ingesteld op _True_ om alle back-ups in geconfigureerde opslag ruimte te verwijderen.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Zorg ervoor dat er geen toepassings upgrades worden uitgevoerd voordat u back-up uitschakelt.
>

## <a name="suspend--resume-backup"></a>Back-up onderbreken & hervatten
Bepaalde situatie kan leiden tot tijdelijke onderbreking van periodieke back-ups van gegevens. Afhankelijk van de vereiste kan het onderbreken van de back-upapi in een dergelijke situatie worden gebruikt bij een _toepassing_, _service_ of _partitie_. De regel voor periodieke back-ups is transitief over de substructuur van de hiërarchie van de toepassing vanaf het punt waarop deze wordt toegepast. 

* Wanneer opschorting wordt toegepast op een _toepassing_ met behulp van de back-upapi van de [toepassing onderbreken](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) , worden alle services en partities onder deze toepassing onderbroken voor periodieke back-ups van gegevens.

* Wanneer opschorting wordt toegepast op een _service_ met behulp van een [suspend-API voor service back-ups](/rest/api/servicefabric/sfclient-api-suspendservicebackup) , worden alle partities onder deze service onderbroken voor periodieke back-ups van gegevens.

* Wanneer de onderbreking wordt toegepast op een _partitie_ met behulp van de [back-up van de partitie Suspend](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) , worden partities onder deze service onderbroken voor periodieke back-ups van gegevens.

Zodra de nood zaak voor de onderbreking is voltooid, kan de periodieke gegevens back-up worden hersteld met behulp van de respectieve back-upapi. Periodieke back-ups moeten worden hervat op dezelfde _toepassing_, _service_ of _partitie_ waar deze is onderbroken.

* Als de onderbreking is toegepast op een _toepassing_, moet deze worden hervat met behulp van de [back-](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) upapi voor toepassingen. 

* Als de onderbreking tijdens een _service_ werd toegepast, moet deze worden hervat met behulp van de [Resume-](/rest/api/servicefabric/sfclient-api-resumeservicebackup) API voor services.

* Als de onderbreking is toegepast op een _partitie_, moet deze worden hervat met behulp van de [back-](/rest/api/servicefabric/sfclient-api-resumepartitionbackup) upapi voor partities.

### <a name="difference-between-suspend-and-disable-backups"></a>Verschil tussen het blok keren en uitschakelen van back-ups
Het uitschakelen van de back-up moet worden gebruikt wanneer back-ups niet meer nodig zijn voor een bepaalde toepassing, service of partitie. Eén kan het uitschakelen van de back-upaanvraag samen met de para meter schone back-ups worden aangeroepen als waar, wat betekent dat alle bestaande back-ups ook worden verwijderd. Suspend moet echter worden gebruikt in scenario's waarbij een back-up tijdelijk moet worden uitgeschakeld, bijvoorbeeld wanneer de lokale schijf vol is of als het uploaden van de back-up mislukt als gevolg van een bekend netwerk probleem, enzovoort. 

Hoewel uitschakelen kan alleen worden aangeroepen op een niveau dat eerder was ingeschakeld voor back-up, maar schorsing kan echter worden toegepast op elk niveau dat momenteel is ingeschakeld voor back-up, hetzij rechtstreeks hetzij via overname/hiërarchie. Als back-up bijvoorbeeld is ingeschakeld op het niveau van de toepassing, kan één aanroepen alleen uitschakelen op toepassings niveau, maar kan worden aangeroepen op toepassing, elke service of partitie onder die toepassing. 

## <a name="auto-restore-on-data-loss"></a>Gegevens verlies automatisch herstellen
De service partitie kan gegevens verliezen vanwege onverwachte fouten. Bijvoorbeeld: de schijf voor twee van de drie replica's voor een partitie (met inbegrip van de primaire replica) wordt beschadigd of gewist.

Wanneer Service Fabric detecteert dat de partitie zich in gegevens verlies bevindt, roept deze de `OnDataLossAsync` Interface methode op de partitie aan en verwacht de partitie dat de vereiste actie moet worden ondernomen om verlies van gegevens te voor komen. Als in dit geval het effectief back-upbeleid op de partitie is `AutoRestoreOnDataLoss` ingesteld op ingeschakeld, `true` wordt de herstel bewerking automatisch geactiveerd met de meest recente beschik bare back-up voor deze partitie.

> [!NOTE]
> U kunt het beste geen automatisch herstel instellen in productie clusters
>

## <a name="get-backup-configuration"></a>Back-upconfiguratie ophalen
Er worden afzonderlijke Api's beschikbaar gesteld om informatie over de back-upconfiguratie op te halen voor een _toepassings_-, _service_-en _partitie_ bereik. [Back-Upconfiguratiegegevens voor toepassingen](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)ophalen, [configuratie gegevens van service back-up ophalen](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)en [configuratie gegevens voor partitie back-ups ophalen](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) deze api's respectievelijk zijn. Deze Api's retour neren voornamelijk het toepasselijke back-upbeleid, het bereik waarop het back-upbeleid wordt toegepast en Details van de back-upblokkering. Hieronder vindt u een korte beschrijving van de geretourneerde resultaten van deze Api's.

- Configuratie-informatie voor back-up van toepassing: bevat de details van het back-upbeleid dat wordt toegepast op de toepassing en alle overschreven beleids regels op Services en partities die tot de toepassing behoren. Het bevat ook de informatie over de ophanging van de toepassing en de IT-Services en partities.

- Configuratie-informatie voor service back-up: geeft de details van het effectief back-upbeleid op de service en het bereik waarop dit beleid is toegepast en alle overschreven beleids regels op de partities. Het bevat ook de informatie over de ophanging van de service en de partities.

- Back-upconfiguratie-informatie: geeft de details van het effectief back-upbeleid op de partitie en het bereik waarop dit beleid is toegepast. Het bevat ook de informatie over de onderbreking voor de partities.

## <a name="list-available-backups"></a>Beschik bare back-ups weer geven

Beschik bare back-ups kunnen worden weer gegeven met de API back-up ophalen. Het resultaat van de API-aanroep omvat back-upitems die betrekking hebben op alle back-ups die beschikbaar zijn op de back-upopslag, die is geconfigureerd in het desbetreffende back-upbeleid. Er zijn verschillende varianten van deze API beschikbaar voor het weer geven van de beschik bare back-ups die horen bij een toepassing, service of partitie. Deze Api's bieden ondersteuning voor de _meest recente_ beschik bare back-up van alle toepasselijke partities of het filteren van back-ups op basis van de _begin datum_ en _eind datum_.

Deze Api's ondersteunen ook de paginering van de resultaten, wanneer de para meter _MaxResults_ is ingesteld op een positief geheel getal dat niet gelijk is aan nul, dan retourneert de API maximum aantal items voor back-up van _MaxResults_ . Als er meer back-upitems beschikbaar zijn dan de waarde voor _MaxResults_ , wordt er een vervolg token geretourneerd. Een geldige vervolg token parameter kan worden gebruikt om de volgende set resultaten op te halen. Wanneer een geldige voortzettings token-waarde wordt door gegeven aan de volgende aanroep van de API, retourneert de API de volgende set resultaten. Er is geen vervolg token opgenomen in het antwoord wanneer alle beschik bare resultaten worden geretourneerd.

Hieronder vindt u de korte informatie over ondersteunde varianten.

- [Back-uplijst van toepassing ophalen](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): retourneert een lijst met back-ups die beschikbaar zijn voor elke partitie die hoort bij de opgegeven service Fabric-toepassing.

- [Lijst met back-ups van service ophalen](/rest/api/servicefabric/sfclient-api-getservicebackuplist): retourneert een lijst met back-ups die beschikbaar zijn voor elke partitie die hoort bij de gegeven service Fabric service.
 
- [Back-uplijst van partitie ophalen](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): retourneert een lijst met back-ups die beschikbaar zijn voor de opgegeven partitie.

## <a name="next-steps"></a>Volgende stappen
- [Naslag informatie over back-up terugzetten REST API](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png

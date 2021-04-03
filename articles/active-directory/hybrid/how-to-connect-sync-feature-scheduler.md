---
title: 'Azure AD Connect synchronisatie: scheduler | Microsoft Docs'
description: In dit onderwerp wordt de ingebouwde functie scheduler in Azure AD Connect Sync beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084733"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect sync: Scheduler (Azure AD Connect-synchronisatie: planning)
In dit onderwerp wordt de ingebouwde scheduler in Azure AD Connect Sync (synchronisatie-engine) beschreven.

Deze functie is geïntroduceerd in Build 1.1.105.0 (uitgebracht op februari 2016).

## <a name="overview"></a>Overzicht
Met Azure AD Connect synchronisatie worden wijzigingen in uw on-premises Directory gesynchroniseerd met behulp van een scheduler. Er zijn twee scheduler-processen: een voor wachtwoord synchronisatie en een andere voor synchronisatie-en onderhouds taken voor object/kenmerk. Dit onderwerp is van toepassing op de laatste.

In eerdere versies was de Scheduler voor objecten en kenmerken extern voor de synchronisatie-engine. Het heeft Windows taak planner of een afzonderlijke Windows-service gebruikt om het synchronisatie proces te activeren. De scheduler is met de 1,1-releases ingebouwd in de synchronisatie-engine en kan enige aanpassing toestaan. De nieuwe standaard synchronisatie frequentie is 30 minuten.

De scheduler is verantwoordelijk voor twee taken:

* **Synchronisatie cyclus**. Het proces voor het importeren, synchroniseren en exporteren van wijzigingen.
* **Onderhouds taken**. Sleutels en certificaten vernieuwen voor het opnieuw instellen van wacht woorden en Device Registration service (DRS). Oude vermeldingen in het operations-logboek opschonen.

De scheduler zelf is altijd actief, maar kan worden geconfigureerd om alleen een of geen van deze taken uit te voeren. Als u bijvoorbeeld uw eigen synchronisatie cyclus proces nodig hebt, kunt u deze taak in de scheduler uitschakelen, maar nog steeds de onderhouds taak uitvoeren.

>[!IMPORTANT]
>Standaard elke 30 minuten dat een synchronisatie cyclus wordt uitgevoerd. Als u de synchronisatie cyclus hebt gewijzigd, moet u ervoor zorgen dat een synchronisatie cyclus ten minste één keer per 7 dagen wordt uitgevoerd. 
>
>* Een Delta synchronisatie moet binnen 7 dagen na de laatste Delta synchronisatie plaatsvinden.
>* Een Delta synchronisatie (na een volledige synchronisatie) moet binnen 7 dagen na het tijdstip waarop de laatste volledige synchronisatie is voltooid, plaatsvinden.
>
>Als u dit niet doet, kan dit leiden tot synchronisatie problemen. hiervoor moet u een volledige synchronisatie uitvoeren om het probleem op te lossen. Dit geldt ook voor servers in de Faserings modus.

## <a name="scheduler-configuration"></a>Scheduler-configuratie
Als u de huidige configuratie-instellingen wilt weer geven, gaat u naar Power shell en voert u uit `Get-ADSyncScheduler` . U ziet iets als deze afbeelding:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Als u **de synchronisatie opdracht of cmdlet is niet beschikbaar** wanneer u deze cmdlet uitvoert, wordt de Power shell-module niet geladen. Dit probleem kan zich voordoen als u Azure AD Connect uitvoert op een domein controller of op een server met hogere Power shell-beperkings niveaus dan de standaard instellingen. Als u deze fout ziet, voert u uit `Import-Module ADSync` om de cmdlet beschikbaar te maken.

* **AllowedSyncCycleInterval**. Het kortste tijds interval tussen de synchronisatie cycli die zijn toegestaan door Azure AD. U kunt niet vaker synchroniseren dan deze instelling en toch worden ondersteund.
* **CurrentlyEffectiveSyncCycleInterval**. De planning is momenteel van kracht. Deze heeft dezelfde waarde als CustomizedSyncInterval (indien ingesteld) als deze niet vaker dan AllowedSyncInterval is. Als u een build gebruikt vóór 1.1.281 en u CustomizedSyncCycleInterval wijzigt, wordt deze wijziging van kracht na de volgende synchronisatie cyclus. Vanuit build 1.1.281 wordt de wijziging onmiddellijk van kracht.
* **CustomizedSyncCycleInterval**. Als u wilt dat de scheduler wordt uitgevoerd op een andere frequentie dan de standaard waarde van 30 minuten, configureert u deze instelling. In de bovenstaande afbeelding is de scheduler zo ingesteld dat deze elk uur wordt uitgevoerd. Als u deze instelling instelt op een waarde die lager is dan AllowedSyncInterval, wordt het laatste gebruikt.
* **NextSyncCyclePolicyType**. Delta of de eerste. Hiermee wordt gedefinieerd of de volgende uitvoering alleen wijzigingen in de Delta moet verwerken of als de volgende uitvoering een volledige import en synchronisatie moet uitvoeren. Daarnaast worden nieuwe of gewijzigde regels opnieuw verwerkt.
* **NextSyncCycleStartTimeInUTC**. De volgende keer dat de scheduler de volgende synchronisatie cyclus start.
* **PurgeRunHistoryInterval**. De bewerkings logboeken voor de tijd moeten worden bewaard. Deze logboeken kunnen worden gecontroleerd in de synchronisatie Service Manager. Standaard worden deze logboeken zeven dagen bewaard.
* **SyncCycleEnabled**. Hiermee wordt aangegeven of de scheduler de processen import, Sync en export uitvoert als onderdeel van de bewerking.
* **MaintenanceEnabled**. Hiermee wordt aangegeven of het onderhouds proces is ingeschakeld. Hiermee worden de certificaten/sleutels bijgewerkt en wordt het operations-logboek gewist.
* **StagingModeEnabled**. Hiermee wordt aangegeven of de [faserings modus](how-to-connect-sync-staging-server.md) is ingeschakeld. Als deze instelling is ingeschakeld, worden de exports onderdrukt van de uitvoering, maar worden nog steeds import en synchronisatie uitgevoerd.
* **SchedulerSuspended**. Tijdens een upgrade instellen door verbinding te maken om te voor komen dat de scheduler wordt uitgevoerd.

U kunt sommige van deze instellingen wijzigen met `Set-ADSyncScheduler` . De volgende para meters kunnen worden gewijzigd:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

In eerdere builds van Azure AD Connect werd **isStagingModeEnabled** weer gegeven in set-ADSyncScheduler. Deze eigenschap kan **niet** worden ingesteld. De eigenschap **SchedulerSuspended** mag alleen worden gewijzigd door Connect. Het wordt **niet ondersteund** om dit rechtstreeks in te stellen met Power shell.

De scheduler-configuratie wordt opgeslagen in azure AD. Als u een staging-server hebt, is de wijziging op de primaire server ook van invloed op de staging-server (behalve IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxis `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-dagen, HH-uur, mm-minuten, ss-seconden

Voorbeeld: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Hiermee wordt de scheduler zo gewijzigd dat elke 3 uur wordt uitgevoerd.

Voorbeeld: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Met wijzigingen wordt de planner gewijzigd zodat deze dagelijks wordt uitgevoerd.

### <a name="disable-the-scheduler"></a>De scheduler uitschakelen  
Als u wijzigingen in de configuratie wilt aanbrengen, moet u de scheduler uitschakelen. Wanneer u bijvoorbeeld [filters configureert](how-to-connect-sync-configure-filtering.md) of [wijzigingen aanbrengt aan de synchronisatie regels](how-to-connect-sync-change-the-configuration.md).

Voer uit om de scheduler uit te scha kelen `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![De scheduler uitschakelen](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Wanneer u de wijzigingen hebt aangebracht, vergeet dan niet om de Scheduler opnieuw in te scha kelen met `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>De planner starten
De scheduler wordt standaard elke 30 minuten uitgevoerd. In sommige gevallen wilt u mogelijk een synchronisatie cyclus uitvoeren tussen de geplande cycli of moet u een ander type uitvoeren.

### <a name="delta-sync-cycle"></a>Delta synchronisatie cyclus
Een Delta synchronisatie cyclus omvat de volgende stappen:


- Delta-import op alle connectors
- Delta synchronisatie op alle connectors
- Exporteren op alle connectors

### <a name="full-sync-cycle"></a>Volledige synchronisatie cyclus
Een volledige synchronisatie cyclus omvat de volgende stappen:

- Volledige import op alle connectors
- Volledige synchronisatie op alle connectors
- Exporteren op alle connectors

Het kan zijn dat u een urgente wijziging hebt die onmiddellijk moet worden gesynchroniseerd. Dit is de reden waarom u hand matig een cyclus moet uitvoeren. 

Als u een synchronisatie cyclus hand matig moet uitvoeren, voert u uit in Power shell `Start-ADSyncSyncCycle -PolicyType Delta` .

Als u een volledige synchronisatie cyclus wilt starten, voert u `Start-ADSyncSyncCycle -PolicyType Initial` uit vanaf een Power shell-prompt.   

Het uitvoeren van een volledige synchronisatie cyclus kan veel tijd in beslag nemen. Lees de volgende sectie voor meer informatie over het optimaliseren van dit proces.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Synchronisatie stappen die vereist zijn voor verschillende configuratie wijzigingen
Er zijn verschillende synchronisatie stappen vereist voor verschillende configuratie wijzigingen om ervoor te zorgen dat de wijzigingen op de juiste manier worden toegepast op alle objecten.

- Er zijn meer objecten of kenmerken toegevoegd die moeten worden geïmporteerd uit een bronmap (door de synchronisatie regels toe te voegen of te wijzigen)
    - Er is een volledige import vereist voor de connector voor die bron directory
- Wijzigingen in de synchronisatie regels aangebracht
    - Er is een volledige synchronisatie vereist voor de connector voor de gewijzigde synchronisatie regels
- Gewijzigde [filtering](how-to-connect-sync-configure-filtering.md) , zodat er een ander aantal objecten moet worden opgenomen
    - Een volledige import is vereist voor de connector voor elke AD-connector tenzij u filtering op basis van kenmerken gebruikt op basis van attributen die al in de synchronisatie-engine worden geïmporteerd

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Een synchronisatie cyclus aanpassen Voer de juiste combi natie van verschillen tussen Delta en volledige synchronisatie uit
Om te voor komen dat u een volledige synchronisatie cyclus uitvoert, kunt u specifieke connectors markeren om een volledige stap uit te voeren met de volgende cmdlets.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Voor beeld: als u wijzigingen hebt aangebracht in de synchronisatie regels voor connector ' AD forest A ' waarvoor geen nieuwe kenmerken hoeven te worden geïmporteerd, voert u de volgende cmdlets uit om een Delta synchronisatie cyclus uit te voeren die ook een volledige synchronisatie stap voor die connector had.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Voor beeld: als u wijzigingen hebt aangebracht in de synchronisatie regels voor connector ' AD forest A ' zodat er nu een nieuw kenmerk moet worden geïmporteerd, voert u de volgende cmdlets uit om een Delta synchronisatie cyclus uit te voeren, waarbij ook een volledige synchronisatie stap is uitgevoerd voor die connector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>De planner stoppen
Als de scheduler momenteel een synchronisatie cyclus uitvoert, moet u deze mogelijk stoppen. Als u bijvoorbeeld de installatie wizard start, wordt deze fout weer geven:

![Scherm opname laat zien dat configuratie fout bericht kan niet worden gewijzigd.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Wanneer een synchronisatie cyclus wordt uitgevoerd, kunt u geen configuratie wijzigingen aanbrengen. U kunt wachten tot de scheduler het proces heeft voltooid, maar ook stoppen, zodat u uw wijzigingen direct kunt door voeren. Het stoppen van de huidige cyclus is niet schadelijk en in behandeling zijnde wijzigingen worden verwerkt met de volgende uitvoering.

1. Begin met de scheduler om de huidige cyclus te stoppen met de Power shell-cmdlet `Stop-ADSyncSyncCycle` .
2. Als u een build vóór 1.1.281 gebruikt en vervolgens stopt met het stoppen van de scheduler, wordt de huidige connector van de huidige taak niet gestopt. Voer de volgende acties uit om ervoor te zorgen dat de connector stopt:

   ![Scherm afbeelding toont Synchronization Service Manager met geselecteerde connectors en een actieve connector gemarkeerd met de actie stoppen geselecteerd.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Start de **synchronisatie service** vanuit het menu Start. Ga naar **connectors**, Markeer de connector met de status **actief** en selecteer **stoppen** in de acties.

De scheduler is nog actief en start opnieuw op de volgende mogelijkheid.

## <a name="custom-scheduler"></a>Aangepaste planner
De cmdlets die in deze sectie worden beschreven, zijn alleen beschikbaar in build [1.1.130.0](reference-connect-version-history.md) en hoger.

Als de ingebouwde scheduler niet aan uw vereisten voldoet, kunt u de connectors plannen met behulp van Power shell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
U kunt op deze manier een profiel voor een connector starten:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

De namen die moeten worden gebruikt voor [connector namen](how-to-connect-sync-service-manager-ui-connectors.md) en [Run profile-namen](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) , vindt u in de [Synchronization Service Manager-gebruikers interface](how-to-connect-sync-service-manager-ui.md).

![Uitvoerings profiel aanroepen](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

De `Invoke-ADSyncRunProfile` cmdlet is synchroon, dat wil zeggen dat er geen besturings element wordt geretourneerd totdat de connector de bewerking heeft voltooid of een fout heeft gemaakt.

Wanneer u uw connectors plant, moet u deze plannen in de volgende volg orde:

1. (Volledig/Delta) Importeren uit on-premises directory's, zoals Active Directory
2. (Volledig/Delta) Importeren vanuit Azure AD
3. (Volledig/Delta) Synchronisatie van on-premises directory's, zoals Active Directory
4. (Volledig/Delta) Synchronisatie vanuit Azure AD
5. Exporteren naar Azure AD
6. Exporteren naar on-premises directory's, zoals Active Directory

Deze volg orde is de manier waarop de connectors worden uitgevoerd met de ingebouwde scheduler.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
U kunt ook de synchronisatie-engine bewaken om te zien of deze bezet is of niet-actief is. Met deze cmdlet wordt een leeg resultaat geretourneerd als de synchronisatie-engine niet actief is en geen connector wordt uitgevoerd. Als een connector wordt uitgevoerd, wordt de naam van de connector geretourneerd.

```
Get-ADSyncConnectorRunStatus
```

![Uitvoerings status van connector](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
In de bovenstaande afbeelding is de eerste regel van een staat waarin de synchronisatie-engine niet actief is. De tweede regel van wanneer de Azure AD-connector wordt uitgevoerd.

## <a name="scheduler-and-installation-wizard"></a>Wizard Planning en installatie
Als u de installatie wizard start, wordt de scheduler tijdelijk onderbroken. Dit gedrag is omdat wordt aangenomen dat u configuratie wijzigingen aanbrengt en deze instellingen kunnen niet worden toegepast als de synchronisatie-engine actief wordt uitgevoerd. Zorg er daarom niet voor dat de installatie wizard wordt geopend omdat de synchronisatie-engine stopt met het uitvoeren van synchronisatie acties.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

---
title: 'Azure AD Connect: automatische upgrade | Microsoft Docs'
description: In dit onderwerp wordt de ingebouwde functie voor automatische upgrades in Azure AD Connect Sync beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317613"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatische upgrade
Deze functie is geïntroduceerd in build [1.1.105.0 (uitgebracht op februari 2016)](reference-connect-version-history.md).  Deze functie is bijgewerkt in [Build 1.1.561](reference-connect-version-history.md) en ondersteunt nu aanvullende scenario's die voorheen niet werden ondersteund.

## <a name="overview"></a>Overzicht
Zorg ervoor dat de Azure AD Connect-installatie altijd up-to-date is, is nog nooit zo eenvoudig geweest met de functie voor **automatische upgrades** . Deze functie is standaard ingeschakeld voor snelle installaties en DirSync-upgrades. Wanneer een nieuwe versie wordt uitgebracht, wordt de installatie automatisch geüpgraded.
Automatische upgrade is standaard ingeschakeld voor het volgende:

* Snelle instellingen voor installatie en DirSync-upgrades.
* Met behulp van SQL Express LocalDB, wat snelle instellingen altijd gebruiken. DirSync met SQL Express gebruikt ook LocalDB.
* Het AD-account is het standaard MSOL_-account dat is gemaakt door snelle instellingen en DirSync.
* De tekst bevat minder dan 100.000 objecten.

De huidige status van automatische upgrade kan worden weer gegeven met de Power shell-cmdlet `Get-ADSyncAutoUpgrade` . Het heeft de volgende statussen:

| Staat | Opmerking |
| --- | --- |
| Ingeschakeld |Automatische upgrade is ingeschakeld. |
| Onderbroken |Alleen ingesteld door het systeem. Het systeem komt **momenteel niet** in aanmerking voor automatische upgrades. |
| Uitgeschakeld |Automatische upgrade is uitgeschakeld. |

U kunt scha kelen tussen **ingeschakeld** en **uitgeschakeld** met `Set-ADSyncAutoUpgrade` . Alleen het systeem moet de status **opgeschort** instellen.  Voordat de Set-ADSyncAutoUpgrade cmdlet wordt ge1.1.750.0d, wordt de functie voor automatisch upgraden geblokkeerd als de status van de automatische upgrade is ingesteld op opgeschort. Deze functionaliteit is nu gewijzigd, zodat de functie voor het bijwerken van de naam niet wordt geblokkeerd.

Automatische upgrade maakt gebruik van Azure AD Connect Health voor de upgrade-infra structuur. Zorg ervoor dat u de Url's in uw proxy server voor **Azure AD Connect Health** hebt geopend, zoals beschreven in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken voor een automatische upgrade.


Als de **Synchronization Service Manager** gebruikers interface op de server wordt uitgevoerd, wordt de upgrade onderbroken totdat de gebruikers interface is gesloten.

## <a name="troubleshooting"></a>Problemen oplossen
Als de installatie van uw verbinding niet werkt zoals verwacht, voert u de volgende stappen uit om erachter te komen wat er mis kan zijn.

Eerst moet u niet verwachten dat er wordt geprobeerd de automatische upgrade uit te voeren op de eerste dag dat een nieuwe versie wordt uitgebracht. Er is opzettelijk een wille keurige aselecte manier voordat een upgrade wordt uitgevoerd en er wordt geen waarschuwing verzonden als uw installatie niet onmiddellijk wordt bijgewerkt.

Als u denkt dat er iets niet klopt, voert u eerst uit `Get-ADSyncAutoUpgrade` om ervoor te zorgen dat automatische upgrade is ingeschakeld.

Als de status is onderbroken, kunt u de gebruiken `Get-ADSyncAutoUpgrade -Detail` om de reden weer te geven.  De blokkerings reden kan een teken reeks waarde bevatten, maar bevat meestal de teken reeks waarde van de UpgradeResult, dat wil zeggen, `UpgradeNotSupportedNonLocalDbInstall` of `UpgradeAbortedAdSyncExeInUse` .  Een samengestelde waarde kan ook worden geretourneerd, zoals `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Het is ook mogelijk om een resultaat te krijgen dat geen UpgradeResult is, dat wil zeggen ' AADHealthEndpointNotDefined ' of ' DirSyncInPlaceUpgradeNonLocalDb '.

Zorg er vervolgens voor dat u de vereiste Url's hebt geopend in uw proxy of firewall. Automatische updates maakt gebruik van Azure AD Connect Health zoals beschreven in het [overzicht](#overview). Als u een proxy gebruikt, zorg er dan voor dat de status is geconfigureerd voor het gebruik van een [proxy server](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Test ook de [status verbinding](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) met Azure AD.

Als de connectiviteit met Azure AD is geverifieerd, is het tijd om te kijken naar de gebeurtenis Logboeken. Start de logboeken en zoek in het gebeurtenis logboek van de **toepassing** . Voeg een Eventlog-filter toe voor de bron **Azure AD Connect-upgrade** en het gebeurtenis-id-bereik **300-399**.  
![Scherm opname van het venster ' Huidig logboek filteren ' met ' gebeurtenis bronnen ' en het selectie vakje ' include/exclude ' voor gebeurtenis-Id's gemarkeerd.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

U kunt nu de gebeurtenis logboeken zien die zijn gekoppeld aan de status voor automatische upgrade.  
![Eventlog-filter voor automatische upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

De resultaat code bevat een voor voegsel met een overzicht van de status.

| Voor voegsel resultaat code | Beschrijving |
| --- | --- |
| Geslaagd |Er is een upgrade uitgevoerd voor de installatie. |
| UpgradeAborted |De upgrade is gestopt door een tijdelijke voor waarde. Er wordt een nieuwe poging gedaan en de verwachting is dat deze later slaagt. |
| UpgradeNotSupported |Het systeem heeft een configuratie die verhindert dat het systeem automatisch wordt bijgewerkt. Er wordt opnieuw geprobeerd te zien of de status wordt gewijzigd, maar de verwachting is dat het systeem hand matig moet worden bijgewerkt. |

Hier volgt een lijst met de meest voorkomende berichten die u vindt. Er worden niet alle weer gegeven, maar het resultaat moet duidelijk zijn bij wat het probleem is.

| Resultaat bericht | Beschrijving |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Kan niet schrijven naar het REGI ster. |
| UpgradeAbortedInsufficientDatabasePermissions |De ingebouwde groep Administrators heeft geen machtigingen voor de data base. Voer hand matig een upgrade uit naar de nieuwste versie van Azure AD Connect om dit probleem op te lossen. |
| UpgradeAbortedInsufficientDiskSpace |Er is onvoldoende schijf ruimte beschikbaar voor de ondersteuning van een upgrade. |
| UpgradeAbortedSecurityGroupsNotPresent |Kan niet alle beveiligings groepen vinden en oplossen die worden gebruikt door de synchronisatie-engine. |
| UpgradeAbortedServiceCanNotBeStarted |De synchronisatie van de NT-service **Microsoft Azure AD** is niet gestart. |
| UpgradeAbortedServiceCanNotBeStopped |De synchronisatie van de NT-service **Microsoft Azure AD** is niet gestopt. |
| UpgradeAbortedServiceIsNotRunning |De NT-service **Microsoft Azure AD synchronisatie** is niet actief. |
| UpgradeAbortedSyncCycleDisabled |De optie SyncCycle in de [scheduler](how-to-connect-sync-feature-scheduler.md) is uitgeschakeld. |
| UpgradeAbortedSyncExeInUse |De [gebruikers interface van Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) is geopend op de server. |
| UpgradeAbortedSyncOrConfigurationInProgress |De installatie wizard wordt uitgevoerd of een synchronisatie is gepland buiten de scheduler. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |U hebt uw eigen aangepaste regels toegevoegd aan de configuratie. |
| UpgradeNotSupportedInvalidPersistedState |De installatie is geen snelle instellingen of een DirSync-upgrade. |
| UpgradeNotSupportedNonLocalDbInstall |U gebruikt geen SQL Server Express LocalDB-data base. |
|UpgradeNotSupportedLocalDbSizeExceeded|Lokale data base-grootte is groter dan of gelijk aan 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|De status gegevens uploads zijn uitgeschakeld vanuit de portal|

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

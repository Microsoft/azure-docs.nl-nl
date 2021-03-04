---
title: Rollen, machtigingen en beveiliging in Azure Monitor
description: Meer informatie over het gebruik van de ingebouwde rollen en machtigingen van Azure Monitor om de toegang tot bewakings resources te beperken.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.openlocfilehash: d0877059dca01bf01df7a4f2aeaa5114278ecc97
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040274"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Rollen, machtigingen en beveiliging in Azure Monitor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veel teams moeten de toegang tot bewakings gegevens en-instellingen strikt reguleren. Als u bijvoorbeeld team leden hebt die uitsluitend werken aan bewaking (ondersteunings technici, DevOps Engineers) of als u een beheerde service provider gebruikt, wilt u deze mogelijk alleen toegang geven tot de bewaking van gegevens en de mogelijkheid voor het maken, wijzigen of verwijderen van resources te beperken. In dit artikel wordt beschreven hoe u snel een ingebouwde bewakings functie van Azure toepast op een gebruiker in azure of uw eigen aangepaste rol bouwt voor een gebruiker die beperkte controle machtigingen nodig heeft. Vervolgens worden de beveiligings overwegingen voor uw Azure Monitor-gerelateerde resources besproken en wordt uitgelegd hoe u de toegang tot de gegevens die ze bevatten, kunt beperken.

## <a name="built-in-monitoring-roles"></a>Ingebouwde bewakingsrollen
De ingebouwde rollen van Azure Monitor zijn ontworpen om de toegang tot resources in een abonnement te beperken, terwijl ze nog steeds verantwoordelijk zijn voor de bewaking van de infra structuur voor het verkrijgen en configureren van de gegevens die ze nodig hebben. Azure Monitor biedt twee out-of-the-box-rollen: een bewakings lezer en een bewakings bijdrager.

### <a name="monitoring-reader"></a>Bewakings lezer
Personen die de rol bewakings lezer toegewezen kunnen alle bewakings gegevens in een abonnement weer geven, maar kunnen geen resource wijzigen of instellingen bewerken die betrekking hebben op bewakings bronnen. Deze rol is geschikt voor gebruikers in een organisatie, zoals ondersteuning of Operations engineers, die het volgende moeten kunnen:

* Bekijk bewakings dashboards in de portal en maak hun eigen persoonlijke bewakings dashboards.
* Waarschuwings regels weer geven die zijn gedefinieerd in [Azure-waarschuwingen](alerts/alerts-overview.md)
* Query's uitvoeren voor metrische gegevens met behulp van de [Azure Monitor rest API](/rest/api/monitor/metrics), [Power shell-cmdlets](powershell-samples.md)of [kruislingse platform-cli](cli-samples.md).
* Query's uitvoeren op het activiteiten logboek met behulp van de portal, Azure Monitor REST API, Power shell-cmdlets of kruislingse platform-CLI.
* De [Diagnostische instellingen](essentials/diagnostic-settings.md) voor een resource weer geven.
* Het [logboek profiel](essentials/activity-log.md#legacy-collection-methods) voor een abonnement weer geven.
* Instellingen voor automatisch schalen weer geven.
* Waarschuwings activiteit en instellingen weer geven.
* Toegang krijgen tot Application Insights gegevens en gegevens weer geven in AI Analytics.
* Log Analytics werkruimte gegevens zoeken, inclusief gebruiks gegevens voor de werk ruimte.
* Log Analytics-beheer groepen weer geven.
* Het zoek schema ophalen in Log Analytics werk ruimte.
* Bewakings pakketten weer geven in Log Analytics-werk ruimte.
* Opgeslagen Zoek opdrachten ophalen en uitvoeren in Log Analytics werk ruimte.
* De opslag configuratie voor de Log Analytics werkruimte ophalen.

> [!NOTE]
> Deze rol geeft geen lees toegang tot logboek gegevens die zijn gestreamd naar een Event Hub of die is opgeslagen in een opslag account. [Hieronder vindt](#security-considerations-for-monitoring-data) u meer informatie over het configureren van toegang tot deze resources.
> 
> 

### <a name="monitoring-contributor"></a>Inzender bewaken
Personen aan wie de rol bewakings bijdrage is toegewezen, kunnen alle bewakings gegevens in een abonnement bekijken en controle-instellingen maken of wijzigen, maar kunnen geen andere resources wijzigen. Deze rol is een hoofd verzameling van de rol van de bewakings lezer en is geschikt voor leden van het bewakings team van een organisatie of voor beheerde service providers die naast de bovenstaande machtigingen ook de volgende handelingen moeten kunnen doen:

* Publiceer bewakings dashboards als een gedeeld dash board.
* [Diagnostische instellingen](essentials/diagnostic-settings.md) instellen voor een resource.\*
* Het [logboek profiel](essentials/activity-log.md#legacy-collection-methods) instellen voor een abonnement.\*
* De activiteiten en instellingen voor waarschuwings regels instellen via [Azure-waarschuwingen](alerts/alerts-overview.md).
* Application Insights-webtests en-onderdelen maken.
* Lijst met gedeelde sleutels van Log Analytics werkruimte.
* Bewakings pakketten in Log Analytics werk ruimte in-of uitschakelen.
* Opgeslagen Zoek opdrachten in Log Analytics werk ruimte maken en verwijderen en uitvoeren.
* De opslag configuratie voor de Log Analytics werkruimte maken en verwijderen.

\*gebruikers moeten ook afzonderlijk Listkeys ophalen-machtigingen krijgen voor de doel resource (opslag account of Event Hub naam ruimte) om een logboek profiel of diagnostische instelling in te stellen.

> [!NOTE]
> Deze rol geeft geen lees toegang tot logboek gegevens die zijn gestreamd naar een Event Hub of die is opgeslagen in een opslag account. [Hieronder vindt](#security-considerations-for-monitoring-data) u meer informatie over het configureren van toegang tot deze resources.
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Bewakings machtigingen en aangepaste Azure-rollen
Als de bovenstaande ingebouwde rollen niet voldoen aan de exacte behoeften van uw team, kunt u [een aangepaste Azure-rol maken](../role-based-access-control/custom-roles.md) met gedetailleerde machtigingen. Hieronder vindt u de algemene Azure RBAC-bewerkingen voor Azure Monitor met hun beschrijvingen.

| Bewerking | Beschrijving |
| --- | --- |
| Micro soft. Insights/ActionGroups/[lezen, schrijven, verwijderen] |Actie groepen lezen/schrijven/verwijderen. |
| Micro soft. Insights/ActivityLogAlerts/[lezen, schrijven, verwijderen] |Waarschuwingen voor het activiteiten logboek lezen/schrijven/verwijderen. |
| Micro soft. Insights/AlertRules/[lezen, schrijven, verwijderen] |Waarschuwings regels lezen/schrijven/verwijderen (van klassieke waarschuwingen). |
| Micro soft. Insights/AlertRules/incidenten/lezen |Incidenten weer geven (geschiedenis van de waarschuwings regel die wordt geactiveerd) voor waarschuwings regels. Dit is alleen van toepassing op de portal. |
| Micro soft. Insights/AutoscaleSettings/[lezen, schrijven, verwijderen] |Instellingen voor automatisch schalen lezen/schrijven/verwijderen. |
| Micro soft. Insights/DiagnosticSettings/[lezen, schrijven, verwijderen] |Diagnostische instellingen lezen/schrijven/verwijderen. |
| Micro soft. Insights/EventCategories/lezen |Alle mogelijke categorieën in het activiteiten logboek opsommen. Wordt gebruikt door de Azure Portal. |
| Micro soft. Insights/eventtypes/digestevents/lezen |Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. |
| Micro soft. Insights/eventtypes/values/Read |Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek. |
| Micro soft. Insights/ExtendedDiagnosticSettings/[lezen, schrijven, verwijderen] | Diagnostische instellingen voor netwerk stroom logboeken lezen/schrijven/verwijderen. |
| Micro soft. Insights/LogDefinitions/lezen |Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. |
| Micro soft. Insights/LogProfiles/[lezen, schrijven, verwijderen] |Logboek profielen lezen/schrijven/verwijderen (streaming-activiteiten logboek naar Event Hub of opslag account). |
| Micro soft. Insights/MetricAlerts/[lezen, schrijven, verwijderen] |Bijna realtime waarschuwingen over metrische gegevens lezen/schrijven/verwijderen |
| Micro soft. Insights/MetricDefinitions/lezen |Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
| Micro soft. Insights/metrische gegevens/lezen |Meet gegevens voor een resource lezen. |
| Micro soft. Insights/registreren/actie |Registreer de Azure Monitor resource provider. |
| Micro soft. Insights/ScheduledQueryRules/[lezen, schrijven, verwijderen] |Waarschuwingen voor logboeken lezen/schrijven/verwijderen in Azure Monitor. |



> [!NOTE]
> Voor toegang tot waarschuwingen, diagnostische instellingen en metrische gegevens voor een resource moet de gebruiker lees toegang hebben tot het bron type en het bereik van die resource. Maken (' schrijven ') een diagnostische instelling of logboek profiel die naar een opslag account of streams naar Event hubs archiveert, vereist dat de gebruiker ook Listkeys ophalen-machtiging heeft voor de doel bron.
> 
> 

U kunt bijvoorbeeld de bovenstaande tabel gebruiken om een aangepaste Azure-rol te maken voor een ' activiteiten logboek lezer ' zoals deze:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Beveiligingsoverwegingen voor het bewaken van gegevens
Bewakings gegevens, met name logboek bestanden, kunnen gevoelige informatie bevatten, zoals IP-adressen of gebruikers namen. Het bewaken van gegevens van Azure is beschikbaar in drie basis formulieren:

1. Het activiteiten logboek, waarmee alle acties op het vlak van het besturings element van uw Azure-abonnement worden beschreven.
2. Bron logboeken, logboeken die worden verzonden door een resource.
3. Metrische gegevens, die worden verzonden door resources.

Al deze drie van deze gegevens typen kunnen worden opgeslagen in een opslag account of worden gestreamd naar Event hub, waarvan beide Azure-resources voor algemeen gebruik zijn. Omdat dit resources voor algemeen gebruik zijn, is het maken, verwijderen en openen ervan een geautoriseerde bewerking die is gereserveerd voor een beheerder. U wordt aangeraden de volgende procedures te gebruiken voor het bewaken van bronnen om misbruik te voor komen:

* Gebruik één enkel speciaal opslag account voor het bewaken van gegevens. Als u bewakings gegevens wilt scheiden in meerdere opslag accounts, moet u het gebruik van een opslag account nooit delen tussen bewaking en niet-bewakings gegevens, omdat dit per ongeluk personen die alleen toegang nodig hebben tot bewakings gegevens (bijvoorbeeld een SIEM van derden) toegang hebben tot gegevens die niet worden bewaakt.
* Gebruik één, toegewezen Service Bus of event hub-naam ruimte in alle diagnostische instellingen om dezelfde reden als hierboven.
* Beperk de toegang tot bewakings-gerelateerde opslag accounts of event hubs door ze in een afzonderlijke resource groep te bewaren en bereik op uw bewakings rollen te [gebruiken](../role-based-access-control/overview.md#scope) om de toegang tot de resource groep te beperken.
* Ken nooit de machtiging Listkeys ophalen toe voor opslag accounts of event hubs in het abonnements bereik wanneer een gebruiker alleen toegang nodig heeft tot bewakings gegevens. Geef in plaats daarvan deze machtigingen aan de gebruiker op een resource of resource groep (als u een toegewezen controle resource groep hebt).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Toegang beperken tot opslag accounts met betrekking tot bewaking
Wanneer een gebruiker of toepassing toegang nodig heeft tot bewakings gegevens in een opslag account, moet u [een account-SAS genereren](/rest/api/storageservices/create-account-sas) op het opslag account dat bewakings gegevens bevat met alleen-lezen toegang op service niveau tot Blob Storage. In Power shell kan dit er als volgt uitzien:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Vervolgens kunt u het token toekennen aan de entiteit die van dat opslag account moet worden gelezen en kunnen alle blobs in dat opslag account worden vermeld en gelezen.

Als u deze machtiging ook met Azure RBAC wilt beheren, kunt u die entiteit de machtiging micro soft. Storage/Storage accounts/listkeys ophalen/Action verlenen voor dat specifieke opslag account. Dit is nodig voor gebruikers die een diagnostische instelling of logboek profiel moeten kunnen instellen om te archiveren naar een opslag account. U kunt bijvoorbeeld de volgende aangepaste Azure-rol maken voor een gebruiker of toepassing die alleen uit één opslag account moet worden gelezen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Met de machtiging Listkeys ophalen kan de gebruiker de sleutels van het primaire en secundaire opslag account weer geven. Deze sleutels verlenen de gebruiker alle ondertekende machtigingen (lezen, schrijven, maken van blobs, verwijderen van blobs enz.) voor alle ondertekende Services (BLOB, wachtrij, tabel, bestand) in dat opslag account. U kunt het beste een hierboven beschreven account-SAS gebruiken.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Toegang tot de bewaking van Event hubs beperken
Een vergelijkbaar patroon kan worden gevolgd door Event hubs, maar eerst moet u een toegewezen listener-autorisatie regel maken. Ga als volgt te werk als u toegang wilt verlenen tot een toepassing die alleen moet Luis teren naar controle-gerelateerde Event hubs:

1. Maak een beleid voor gedeelde toegang op de Event Hub (s) die zijn gemaakt voor streaming-bewakings gegevens met alleen Luister claims. Dit kan in de portal worden uitgevoerd. U kunt bijvoorbeeld het volgende noemen: ' monitoringReadOnly '. Indien mogelijk moet u die sleutel rechtstreeks aan de consument geven en de volgende stap overs Laan.
2. Als de consument de sleutel ad hoc moet kunnen ophalen, geeft u de gebruiker de Listkeys ophalen actie voor die Event Hub. Dit is ook nodig voor gebruikers die een diagnostische instelling of logboek profiel moeten kunnen instellen om te streamen naar Event hubs. U kunt bijvoorbeeld een Azure RBAC-regel maken:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Bewaking binnen een beveiligd virtueel netwerk

Azure Monitor moet toegang hebben tot uw Azure-resources om de services te kunnen bieden die u inschakelt. Als u uw Azure-resources wilt bewaken terwijl u ze toch van toegang tot het open bare Internet beveiligt, kunt u de volgende instellingen inschakelen.

### <a name="secured-storage-accounts"></a>Beveiligde opslag accounts 

Bewakings gegevens worden vaak naar een opslag account geschreven. U kunt er ook voor zorgen dat de gegevens die naar een opslag account worden gekopieerd, niet toegankelijk zijn voor onbevoegde gebruikers. Voor extra beveiliging kunt u netwerk toegang vergren delen om alleen uw geautoriseerde resources en vertrouwde micro soft-Services toegang te geven tot een opslag account door een opslag account te beperken om ' geselecteerde netwerken ' te gebruiken.
![Azure Storage instellingen dialoog venster ](./media/roles-permissions-security/secured-storage-example.png) Azure monitor wordt beschouwd als een van deze ' vertrouwde micro soft-Services ' als u vertrouwde micro soft-Services toegang verleent tot uw beveiligde opslag, heeft Azure monitor toegang tot uw account voor beveiligde opslag, waardoor het schrijven van Azure monitor resource logboeken, het activiteiten logboek en de metrische gegevens naar uw opslag account onder deze beveiligde voor waarden. Op deze locatie kunnen ook Log Analytics logboeken van beveiligde opslag worden gelezen.   


Zie [netwerk beveiliging en Azure Storage](../storage/common/storage-network-security.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over Azure RBAC en machtigingen in Resource Manager](../role-based-access-control/overview.md)
* [Lees het overzicht van bewaking in azure](overview.md)


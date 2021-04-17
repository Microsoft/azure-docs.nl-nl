---
title: Overzicht van inrichtingslogboeken in de Azure Portal (preview) | Microsoft Docs
description: Maak kennis met het inrichten van logboekrapporten in Azure Active Directory de Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535995"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Overzicht van inrichtingslogboeken in de Azure Portal (preview)

De rapportagearchitectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- Activiteit: 
    - **Aanmeldingen: informatie** over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers.
    - [Auditlogboeken:](concept-audit-logs.md)informatie over systeemactiviteit over gebruikers- en groepsbeheer, beheerde toepassingen en directory-activiteiten.
    - **Inrichtingslogboeken:** systeemactiviteit over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtingsservice. 

- Veiligheid: 
    - **Riskante aanmeldingen:** een riskante aanmelding [is](../identity-protection/overview-identity-protection.md) een indicator voor een aanmeldingspoging die mogelijk is uitgevoerd door iemand die niet de legitieme eigenaar van een gebruikersaccount is.
    - **Gebruikers voor risico:** een [riskante gebruiker](../identity-protection/overview-identity-protection.md) is een indicator voor een gebruikersaccount dat mogelijk is aangetast.

In dit onderwerp vindt u een overzicht van de inrichtingslogboeken. De logboeken bieden antwoorden op vragen zoals: 

* Welke groepen zijn gemaakt in ServiceNow?
* Welke gebruikers zijn verwijderd uit Adobe?
* Welke gebruikers van Workday zijn gemaakt in Active Directory? 

## <a name="prerequisites"></a>Vereisten

Deze gebruikers hebben toegang tot de gegevens in inrichtingslogboeken:

* Toepassingseigenaren (logboeken voor hun eigen toepassingen)
* Gebruikers in de rollen Beveiligingsbeheerder, Beveiligingslezer, Rapportlezer, Beveiligingsoperator, Toepassingsbeheerder en Cloudtoepassingsbeheerder
* Gebruikers met een aangepaste rol met de [machtiging provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Hoofdbeheerders


Als u het rapport met inrichtingsactiviteiten wilt weergeven, moet aan uw tenant Azure AD Premium licentie zijn gekoppeld. Zie Aan de slag met azure-Azure Active Directory Premium om uw Azure [AD-editie Azure Active Directory Premium.](../fundamentals/active-directory-get-started-premium.md) 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Manieren om te communiceren met de inrichtingslogboeken 
Klanten kunnen de inrichtingslogboeken op vier manieren gebruiken:

- Toegang tot de logboeken vanuit Azure Portal, zoals beschreven in de volgende sectie.
- De inrichtingslogboeken streamen naar [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). Met deze methode kunt u uitgebreide gegevensretentie en aangepaste dashboards, waarschuwingen en query's maken.
- Query's uitvoeren [Microsoft Graph API voor](/graph/api/resources/provisioningobjectsummary) de inrichtingslogboeken.
- De inrichtingslogboeken downloaden als een CSV- of JSON-bestand.

## <a name="access-the-logs-from-the-azure-portal"></a>Toegang tot de logboeken vanuit de Azure Portal
U kunt de inrichtingslogboeken openen door  Inrichtingslogboeken te selecteren in de sectie Bewaking van Azure Active Directory **deelvenster** in  [Azure Portal](https://portal.azure.com). Het kan tot twee uur duren voordat sommige inrichtingsrecords worden weergegeven in de portal.

![Schermopname met selecties voor toegang tot inrichtingslogboeken.](./media/concept-provisioning-logs/access-provisioning-logs.png "Inrichtingslogboeken")


Een inrichtingslogboek heeft een standaardlijstweergave die het volgende laat zien:

- De identiteit
- De actie
- Het bronsysteem
- Het doelsysteem
- De status
- De datum


![Schermopname van standaardkolommen in een inrichtingslogboek.](./media/concept-provisioning-logs/default-columns.png "Standaardkolommen")

U kunt de lijstweergave aanpassen door Kolommen **te selecteren** op de werkbalk.

![Schermopname van de knop voor het aanpassen van kolommen.](./media/concept-provisioning-logs/column-chooser.png "Kolomkiezer")

In dit gebied kunt u aanvullende velden weergeven of velden verwijderen die al worden weergegeven.

![Schermopname van beschikbare kolommen met een aantal geselecteerde kolommen.](./media/concept-provisioning-logs/available-columns.png "Beschikbare kolommen")

Selecteer een item in de lijstweergave voor meer gedetailleerde informatie.

![Schermopname met gedetailleerde informatie.](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>Inrichtingsactiviteiten filteren

U kunt uw inrichtingsgegevens filteren. Sommige filterwaarden worden dynamisch ingevuld op basis van uw tenant. Als u bijvoorbeeld geen 'create'-gebeurtenissen in uw tenant hebt, is er geen **optie Filter** maken.

In de standaardweergave kunt u de volgende filters selecteren:

- **Identiteit**
- **Datum**
- **Status**
- **Actie**


![Schermopname met filterwaarden.](./media/concept-provisioning-logs/default-filter.png "Filter")

Met **het** filter Identiteit kunt u de naam of identiteit opgeven die u belangrijk vindt. Deze identiteit kan een gebruiker, groep, rol of ander object zijn. 

U kunt zoeken op de naam of id van het object. De id varieert per scenario. Wanneer u bijvoorbeeld een object inrichten van Azure AD naar Salesforce, is de bron-id de object-id van de gebruiker in Azure AD. De doel-id is de id van de gebruiker in Salesforce. Bij het inrichten van Workday naar Active Directory, is de bron-id de werknemer-id van de Workday-werknemer. 

> [!NOTE]
> De naam van de gebruiker is mogelijk niet altijd aanwezig in **de kolom** Identiteit. Er is altijd één id. 


Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens. Mogelijke waarden zijn:

- 1 maand
- 7 dagen
- 30 dagen
- 24 uur
- Aangepast tijdsinterval

Wanneer u een aangepast tijdsbestek selecteert, kunt u een begindatum en een einddatum configureren.

Met **het** filter Status kunt u het volgende selecteren:

- **Alles**
- **Geslaagd**
- **Fout**
- **Overgeslagen**

Met **het** filter Actie kunt u deze acties filteren:

- **Maken** 
- **Bijwerken**
- **Verwijderen**
- **Uitschakelen**
- **Overige**

Naast de filters van de standaardweergave kunt u de volgende filters instellen.

![Schermopname met velden die u als filters kunt toevoegen.](./media/concept-provisioning-logs/add-filter.png "Een veld kiezen")

- **Taak-id:** er is een unieke taak-id gekoppeld aan elke toepassing voor wie u inrichting hebt ingeschakeld.   

- **Cyclus-id:** de cyclus-id identificeert de inrichtingscyclus op unieke manier. U kunt deze id delen met productondersteuning om de cyclus op te zoeken waarin deze gebeurtenis heeft plaatsgevonden.

- **Wijzigings-id:** de wijzigings-id is een unieke id voor de inrichtingsgebeurtenis. U kunt deze id delen met productondersteuning om de inrichtingsgebeurtenis op te zoeken.   

- **Bronsysteem:** u kunt opgeven van waar de identiteit wordt ingericht. Wanneer u bijvoorbeeld een object inrichten van Azure AD naar ServiceNow, is het bronsysteem Azure AD. 

- **Doelsysteem:** u kunt opgeven waar de identiteit wordt ingericht. Wanneer u bijvoorbeeld een object inrichten van Azure AD naar ServiceNow, is het doelsysteem ServiceNow. 

- **Toepassing:** u kunt alleen records van toepassingen weergeven met een weergavenaam die een specifieke tekenreeks bevat.

## <a name="provisioning-details"></a>Inrichtingsdetails 

Wanneer u een item selecteert in de inrichtingslijstweergave, krijgt u meer informatie over dit item. De details zijn gegroepeerd op de volgende tabbladen.

![Schermopname van vier tabbladen met inrichtingsdetails.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabs")

- **Stappen:** hier worden de stappen beschreven die nodig zijn om een object in terichten. Het inrichten van een object kan bestaan uit vier stappen:
  
  1. Importeer het -object.
  1. Bepaal of het object binnen het bereik valt.
  1. Het object matchen tussen de bron en het doel.
  1. Het object inrichten (maken, bijwerken, verwijderen of uitschakelen).

  ![Schermopname van de inrichtingsstappen op het tabblad Stappen.](./media/concept-provisioning-logs/steps.png "Filter")

- **Problemen met &:** bevat de foutcode en de reden. De foutgegevens zijn alleen beschikbaar als er een fout is opgetreden.

- **Gewijzigde eigenschappen:** toont de oude waarde en de nieuwe waarde. Als er geen oude waarde is, is die kolom leeg.

- **Samenvatting:** biedt een overzicht van wat er is gebeurd en id's voor het object in de bron- en doelsystemen.

## <a name="download-logs-as-csv-or-json"></a>Logboeken downloaden als CSV of JSON

U kunt de inrichtingslogboeken voor later gebruik downloaden door naar de logboeken in de Azure Portal en **Downloaden te selecteren.** Het bestand wordt gefilterd op basis van de filtercriteria die u hebt geselecteerd. Maak de filters zo specifiek mogelijk om de grootte en tijd van de download te verminderen. 

De CSV-download bevat drie bestanden:

* **ProvisioningLogs:** hiermee worden alle logboeken gedownload, met uitzondering van de inrichtingsstappen en gewijzigde eigenschappen.
* **ProvisioningLogs_ProvisioningSteps:** bevat de inrichtingsstappen en de wijzigings-id. U kunt de wijzigings-id gebruiken om de gebeurtenis samen te brengen met de andere twee bestanden.
* **ProvisioningLogs_ModifiedProperties:** bevat de kenmerken die zijn gewijzigd en de wijzigings-id. U kunt de wijzigings-id gebruiken om de gebeurtenis samen te brengen met de andere twee bestanden.

#### <a name="open-the-json-file"></a>Het JSON-bestand openen
Gebruik een teksteditor zoals Microsoft Visual Studio Code om [het JSON-bestand te openen.](https://aka.ms/vscode) Visual Studio code maakt het bestand gemakkelijker te lezen door syntaxis markeren. U kunt het JSON-bestand ook openen met behulp van browsers in een niet-te bewerken indeling, [zoals Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Het JSON-bestand opslificeren
Het JSON-bestand wordt gedownload in een geminerificeerde indeling om de grootte van de download te verminderen. Deze indeling kan ervoor zorgen dat de nettolading moeilijk te lezen is. Bekijk twee opties om het bestand beter te maken:

- Gebruik [Visual Studio Code om de JSON op te maken.](https://code.visualstudio.com/docs/languages/json#_formatting)

- Gebruik PowerShell om de JSON op te maken. Met dit script wordt de JSON uitgevoerd in een indeling die tabbladen en spaties bevat: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Het JSON-bestand parseren

Hier zijn enkele voorbeeldopdrachten voor het werken met het JSON-bestand met behulp van PowerShell. U kunt elke programmeertaal gebruiken waar u vertrouwd mee bent.  

Lees eerst [het JSON-bestand door](/powershell/module/microsoft.powershell.utility/convertfrom-json) deze opdracht uit te voeren:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

U kunt de gegevens nu parseren op basis van uw scenario. Hieronder vindt u enkele voorbeelden: 

- Alle taak-ID's in het JSON-bestand als uitvoer:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Uitvoer van alle wijzigings-ID's voor gebeurtenissen waarbij de actie 'maken' was:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Wat u moet weten

Hier zijn enkele tips en overwegingen voor het inrichten van rapporten:

- In Azure Portal worden gerapporteerde inrichtingsgegevens voor 30 dagen op de opslag op het moment dat u een Premium-editie hebt en 7 dagen als u een gratis editie hebt. U kunt de inrichtingslogboeken voor meer dan 30 dagen publiceren naar [Log Analytics.](../app-provisioning/application-provisioning-log-analytics.md) 

- U kunt het kenmerk change ID gebruiken als unieke id. Dit is bijvoorbeeld handig wanneer u interactie hebt met productondersteuning.

- Mogelijk ziet u overgeslagen gebeurtenissen voor gebruikers die niet binnen het bereik vallen. Dit is te verwachten, met name wanneer het synchronisatiebereik is ingesteld op alle gebruikers en groepen. De service evalueert alle objecten in de tenant, zelfs objecten die buiten het bereik vallen. 

- De inrichtingslogboeken zijn momenteel niet beschikbaar in de overheidscloud. Als u geen toegang hebt tot de inrichtingslogboeken, gebruikt u de auditlogboeken als tijdelijke oplossing. 

- De inrichtingslogboeken geven geen rolimports weer (van toepassing op AWS, Salesforce en Zendesk). U vindt de logboeken voor rolimports in de auditlogboeken. 

## <a name="error-codes"></a>Foutcodes

Gebruik de volgende tabel om beter inzicht te krijgen in het oplossen van fouten die u in de inrichtingslogboeken vindt. Voor foutcodes die ontbreken, geeft u feedback via de koppeling onder aan deze pagina. 

|Foutcode|Beschrijving|
|---|---|
|Conflict, EntryConflict|Corrigeert de conflicterende kenmerkwaarden in Azure AD of de toepassing. Of controleer uw overeenkomende kenmerkconfiguratie als het conflicterende gebruikersaccount moest worden gematcht en overgenomen. Lees de [documentatie](../app-provisioning/customize-application-attributes.md) voor meer informatie over het configureren van overeenkomende kenmerken.|
|TooManyRequests|De doel-app heeft deze poging om de gebruiker bij te werken afgewezen omdat deze overbelast is en te veel aanvragen ontvangt. U hoeft niets te doen. Deze poging wordt automatisch ingetrokken. Microsoft is ook op de hoogte gesteld van dit probleem.|
|InternalServerError |De doel-app heeft een onverwachte fout geretourneerd. Een serviceprobleem met de doeltoepassing kan verhinderen dat dit werkt. Deze poging wordt automatisch binnen 40 minuten ingetrokken.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD is geverifieerd met de doeltoepassing, maar is niet gemachtigd om de update uit te voeren. Lees alle instructies die de doeltoepassing heeft gegeven, samen met de betreffende zelfstudie over [de toepassing.](../saas-apps/tutorial-list.md)|
|UnprocessableEntity|De doeltoepassing heeft een onverwacht antwoord geretourneerd. De configuratie van de doeltoepassing is mogelijk niet juist of een serviceprobleem met de doeltoepassing verhindert mogelijk dat dit werkt.|
|WebExceptionProtocolError |Er is een HTTP-protocolfout opgetreden bij het maken van verbinding met de doeltoepassing. U hoeft niets te doen. Deze poging wordt automatisch binnen 40 minuten ingetrokken.|
|InvalidAnchor|Een gebruiker die eerder is gemaakt of een overeenkomst heeft met de inrichtingsservice, bestaat niet meer. Zorg ervoor dat de gebruiker bestaat. Als u een nieuwe match van alle gebruikers wilt forcen, gebruikt u de Microsoft Graph API om de [taak opnieuw op te starten.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) <br><br>Als u het inrichten opnieuw start, wordt er een eerste cyclus gestart. Dit kan even duren. Als u de inrichting opnieuw start, wordt ook de cache verwijderd die de inrichtingsservice gebruikt om te werken. Dit betekent dat alle gebruikers en groepen in de tenant opnieuw moeten worden geëvalueerd en dat bepaalde inrichtingsgebeurtenissen kunnen worden uitgevallen.|
|Niet-implementeerd | De doel-app heeft een onverwacht antwoord geretourneerd. De configuratie van de app is mogelijk niet juist of een serviceprobleem met de doel-app verhindert mogelijk dat dit werkt. Bekijk de instructies die de doeltoepassing heeft gegeven, samen met de betreffende zelfstudie over [de toepassing.](../saas-apps/tutorial-list.md) |
|MandatoryFieldsMissing, MissingValues |De gebruiker kan niet worden gemaakt omdat de vereiste waarden ontbreken. Corrigeert de ontbrekende kenmerkwaarden in de bronrecord of controleer uw overeenkomende kenmerkconfiguratie om ervoor te zorgen dat de vereiste velden niet worden weggelaten. [Meer informatie over](../app-provisioning/customize-application-attributes.md) het configureren van overeenkomende kenmerken.|
|SchemaAttributeNotFound |De bewerking kan niet worden uitgevoerd omdat er een kenmerk is opgegeven dat niet bestaat in de doeltoepassing. Zie de [documentatie over](../app-provisioning/customize-application-attributes.md) kenmerkaanpassing en zorg ervoor dat uw configuratie juist is.|
|InternalError |Er is een interne servicefout opgetreden in de Azure AD-inrichtingsservice. U hoeft niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|InvalidDomain |De bewerking kan niet worden uitgevoerd omdat een kenmerkwaarde een ongeldige domeinnaam bevat. Werk de domeinnaam van de gebruiker bij of voeg deze toe aan de lijst met toegestane domeinen in de doeltoepassing. |
|Time-out |De bewerking kan niet worden voltooid omdat het te lang duurde voordat de doeltoepassing reageerde. U hoeft niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|LicenseLimitExceeded|De gebruiker kan niet worden gemaakt in de doeltoepassing omdat er geen licenties beschikbaar zijn voor deze gebruiker. Meer licenties aanschaffen voor de doeltoepassing. Of controleer uw gebruikerstoewijzingen en configuratie van kenmerktoewijzingen om ervoor te zorgen dat de juiste gebruikers met de juiste kenmerken worden toegewezen.|
|DuplicateTargetEntries  |De bewerking kan niet worden voltooid omdat meer dan één gebruiker in de doeltoepassing is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker uit de doeltoepassing of [configureer uw kenmerktoewijzingen opnieuw.](../app-provisioning/customize-application-attributes.md)|
|DuplicateSourceEntries | De bewerking kan niet worden voltooid omdat er meer dan één gebruiker is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker of [configureer uw kenmerktoewijzingen opnieuw.](../app-provisioning/customize-application-attributes.md)|
|ImportSkipped | Wanneer elke gebruiker wordt geëvalueerd, probeert het systeem de gebruiker te importeren uit het bronsysteem. Deze fout treedt meestal op wanneer de gebruiker die wordt geïmporteerd, de overeenkomende eigenschap mist die is gedefinieerd in uw kenmerktoewijzingen. Zonder een waarde die aanwezig is op het gebruikersobject voor het overeenkomende kenmerk, kan het systeem geen bereik, overeenkomende of exporteren van wijzigingen evalueren. Houd er rekening mee dat de aanwezigheid van deze fout niet aangeeft dat de gebruiker binnen het bereik valt, omdat u het bereik van de gebruiker nog niet hebt geëvalueerd.|
|EntrySynchronizationSkipped | De inrichtingsservice heeft een query uitgevoerd op het bronsysteem en de gebruiker geïdentificeerd. Er is geen verdere actie ondernomen op de gebruiker en deze is overgeslagen. De gebruiker kan buiten het bereik zijn geweest of de gebruiker bestond al in het doelsysteem zonder verdere wijzigingen vereist te hebben.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Een GET-aanvraag voor het ophalen van een gebruiker of groep heeft meerdere gebruikers of groepen ontvangen in het antwoord. Het systeem verwacht slechts één gebruiker of groep in het antwoord te ontvangen. [Als](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)u bijvoorbeeld een GET-aanvraag doet om een groep op te halen en een filter op te geven om leden uit te sluiten, en uw SCIM-eindpunt (System for Cross-Domain Identity Management) de leden retourneert, krijgt u deze foutmelding.|

## <a name="next-steps"></a>Volgende stappen

* [De status van het inrichten van gebruikers controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Probleem bij het configureren van gebruikers inrichten voor een toepassing in de Azure AD-galerie](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API voor inrichtingslogboeken](/graph/api/resources/provisioningobjectsummary)
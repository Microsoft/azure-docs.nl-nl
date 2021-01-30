---
title: Overzicht van het inrichtings logboek in de Azure Portal (preview) | Microsoft Docs
description: Krijg een inleiding op het inrichten van logboek rapporten in Azure Active Directory via de Azure Portal.
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
ms.openlocfilehash: ad69df37d2635156873dc59d6fbf700a67ade548
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091929"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Overzicht van het inrichtings logboek in de Azure Portal (preview-versie)

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- Activiteit: 
    - **Aanmeldingen**: informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - [Audit logboeken](concept-audit-logs.md): informatie over systeem activiteit over gebruikers-en groeps beheer, beheerde toepassingen en Directory-activiteiten.
    - **Inrichtings logboeken**: systeem activiteit over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtings service. 

- Beveiligingsprincipal 
    - **Risk ante aanmeldingen**: een [Risk ante](../identity-protection/overview-identity-protection.md) aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
    - **Gebruikers die zijn gemarkeerd voor risico**: een [Risk ante gebruiker](../identity-protection/overview-identity-protection.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

In dit onderwerp vindt u een overzicht van de inrichtings Logboeken. De logboeken bevatten antwoorden op vragen zoals: 

* Welke groepen zijn met succes gemaakt in ServiceNow?
* Wat zijn de gebruikers van Adobe verwijderd?
* Wat zijn de gebruikers van workday gemaakt in Active Directory? 

## <a name="prerequisites"></a>Vereisten

Deze gebruikers hebben toegang tot de gegevens in de inrichtings logboeken:

* Eigen aren van toepassingen (logboeken voor hun eigen toepassingen)
* Gebruikers in de rol beveiligings beheerder, beveiligings lezer, rapport lezer, beveiligings operator, toepassings beheerder en Cloud toepassings beheerder
* Gebruikers in een aangepaste rol met de [machtiging provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Hoofdbeheerders


Als u het rapport inrichtings activiteit wilt weer geven, moet aan uw Tenant een Azure AD Premium-licentie zijn gekoppeld. Zie aan de [slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md)om uw Azure AD-editie bij te werken. 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Manieren van interactie met de inrichtings logboeken 
Klanten kunnen op vier manieren communiceren met de inrichtings logboeken:

- Toegang tot de logboeken vanuit de Azure Portal, zoals beschreven in de volgende sectie.
- De inrichtings logboeken streamen naar [Azure monitor](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics). Met deze methode kunt u uitgebreide gegevens retentie en aangepaste Dash boards, waarschuwingen en query's bouwen.
- Query's uitvoeren op de [Microsoft Graph-API](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) voor de inrichtings Logboeken.
- De inrichtings logboeken worden gedownload als een CSV-of JSON-bestand.

## <a name="access-the-logs-from-the-azure-portal"></a>Toegang tot de logboeken vanuit de Azure Portal
U kunt toegang krijgen tot de inrichtings logboeken door **inrichtings logboeken** te selecteren in het gedeelte **bewaking** van het deel venster **Azure Active Directory** van de [Azure Portal](https://portal.azure.com). Het kan Maxi maal twee uur duren voordat bepaalde inrichtings records worden weer gegeven in de portal.

![Scherm afbeelding met selecties voor toegang tot inrichtings Logboeken.](./media/concept-provisioning-logs/access-provisioning-logs.png "Inrichtingslogboeken")


Een inrichtings logboek heeft een standaard lijst weergave waarin het volgende wordt weer gegeven:

- De identiteit
- De actie
- Het bron systeem
- Het doel systeem
- De status
- De datum


![Scherm opname van de standaard kolommen in een inrichtings logboek.](./media/concept-provisioning-logs/default-columns.png "Standaard kolommen")

U kunt de lijst weergave aanpassen door **kolommen** te selecteren op de werk balk.

![Scherm afbeelding met de knop voor het aanpassen van kolommen.](./media/concept-provisioning-logs/column-chooser.png "Kolom kiezer")

In dit gebied kunt u extra velden weer geven of velden verwijderen die al worden weer gegeven.

![Scherm opname van de beschik bare kolommen met een selectie.](./media/concept-provisioning-logs/available-columns.png "Beschik bare kolommen")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Scherm afbeelding met gedetailleerde informatie.](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>Inrichtings activiteiten filteren

U kunt uw inrichtings gegevens filteren. Sommige filter waarden worden dynamisch ingevuld op basis van uw Tenant. Als u bijvoorbeeld geen gebeurtenissen voor ' maken ' in uw Tenant hebt, is er geen optie voor het **maken** van een filter.

In de standaard weergave kunt u de volgende filters selecteren:

- **Identiteit**
- **Datum**
- **Status**
- **Actie**


![Scherm opname waarin de filter waarden worden weer gegeven.](./media/concept-provisioning-logs/default-filter.png "Filter")

Met het **identiteits** filter kunt u de naam of de identiteit opgeven die u bevalt. Deze identiteit kan een gebruiker, een groep, een rol of een ander object zijn. 

U kunt zoeken op de naam of ID van het object. De ID is afhankelijk van het scenario. Wanneer u bijvoorbeeld een object inricht vanuit Azure AD naar Sales Force, is de bron-ID de object-ID van de gebruiker in azure AD. De doel-ID is de ID van de gebruiker in Sales Force. Wanneer u een werk nemer inricht van workday naar Active Directory, is de bron-ID de werknemers-ID van de werkdag. 

> [!NOTE]
> De naam van de gebruiker is mogelijk niet altijd aanwezig in de **identiteits** kolom. Er wordt altijd één ID weer. 


Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens. Mogelijke waarden zijn:

- 1 maand
- 7 dagen
- 30 dagen
- 24 uur
- Aangepast tijdsinterval

Wanneer u een aangepast tijds bestek selecteert, kunt u een begin-en eind datum configureren.

Met het **status** filter kunt u het volgende selecteren:

- **Alles**
- **Geslaagd**
- **Veroorzaakt**
- **Overgeslagen**

Met het **actie** filter kunt u de volgende acties filteren:

- **Maken** 
- **Bijwerken**
- **Verwijderen**
- **Uitschakelen**
- **Overige**

Naast de filters van de standaard weergave, kunt u de volgende filters instellen.

![Scherm opname van velden die u als filters kunt toevoegen.](./media/concept-provisioning-logs/add-filter.png "Kies een veld")

- **Taak-id**: een unieke taak-id is gekoppeld aan elke toepassing waarvoor u het inrichten hebt ingeschakeld.   

- **Cyclus-id**: de cyclus-id is een unieke identificatie van de inrichtings cyclus. U kunt deze ID met product ondersteuning delen om de cyclus te zoeken waarin deze gebeurtenis plaatsvond.

- **Wijzigings-id**: de wijzigings-id is een unieke id voor de inrichtings gebeurtenis. U kunt deze ID met product ondersteuning delen om de inrichtings gebeurtenis op te zoeken.   

- **Bron systeem**: u kunt opgeven waar de identiteit wordt opgehaald. Wanneer u bijvoorbeeld een object van Azure AD inricht in ServiceNow, is het bron systeem Azure AD. 

- **Doel systeem**: u kunt opgeven waar de identiteit wordt ingericht. Wanneer u bijvoorbeeld een object van Azure AD inricht op ServiceNow, is het doel systeem ServiceNow. 

- **Toepassing**: u kunt alleen records van toepassingen weer geven met een weergave naam die een specifieke teken reeks bevat.

## <a name="provisioning-details"></a>Inrichtings gegevens 

Wanneer u een item in de inrichtings lijst weergave selecteert, krijgt u meer informatie over dit item. De details worden in de volgende tabbladen gegroepeerd.

![Scherm afbeelding met vier tabbladen die inrichtings gegevens bevatten.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabs")

- **Stappen**: overzicht van de stappen voor het inrichten van een object. Het inrichten van een object kan bestaan uit vier stappen:
  
  1. Importeer het object.
  1. Bepalen of het object binnen het bereik valt.
  1. Zoek het object tussen de bron en het doel.
  1. Het object inrichten (maken, bijwerken, verwijderen of uitschakelen).

  ![Scherm afbeelding toont de inrichtings stappen op het tabblad stappen.](./media/concept-provisioning-logs/steps.png "Filter")

- **Problemen met & aanbevelingen oplossen**: bevat de fout code en de reden. De informatie over de fout is alleen beschikbaar als er een fout optreedt.

- **Gewijzigde eigenschappen**: toont de oude waarde en de nieuwe waarde. Als er geen oude waarde is, is die kolom leeg.

- **Samen vatting**: Hier vindt u een overzicht van wat er is gebeurd en de id's voor het object in de bron-en doel systemen.

## <a name="download-logs-as-csv-or-json"></a>Logboeken downloaden als CSV of JSON

U kunt de inrichtings logboeken voor later gebruik downloaden door naar de logboeken in de Azure Portal te gaan en **downloaden** te selecteren. Het bestand wordt gefilterd op basis van de filter criteria die u hebt geselecteerd. Maak de filters zo specifiek mogelijk om de grootte en het tijdstip van de down load te verminderen. 

De CSV-down load omvat drie bestanden:

* **ProvisioningLogs**: Hiermee worden alle logboeken gedownload, met uitzonde ring van de inrichtings stappen en gewijzigde eigenschappen.
* **ProvisioningLogs_ProvisioningSteps**: bevat de inrichtings stappen en de wijzigings-id. U kunt de wijzigings-ID gebruiken om de gebeurtenis samen te voegen met de andere twee bestanden.
* **ProvisioningLogs_ModifiedProperties**: bevat de kenmerken die zijn gewijzigd en de wijzigings-id. U kunt de wijzigings-ID gebruiken om de gebeurtenis samen te voegen met de andere twee bestanden.

#### <a name="open-the-json-file"></a>Het JSON-bestand openen
Als u het JSON-bestand wilt openen, gebruikt u een tekst editor zoals [micro soft Visual Studio code](https://aka.ms/vscode). Met Visual Studio code is het bestand gemakkelijker te lezen door het markeren van syntaxis. U kunt het JSON-bestand ook openen door browsers te gebruiken in een indeling die niet kan worden bewerkt, zoals [micro soft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Het JSON-bestand Prettify
Het JSON-bestand wordt gedownload in de minified-indeling om de download grootte te verkleinen. Met deze indeling kan de payload moeilijk te lezen zijn. Bekijk twee opties om het bestand te Prettify:

- Gebruik [Visual Studio code om de JSON op te maken](https://code.visualstudio.com/docs/languages/json#_formatting).

- Gebruik Power shell om de JSON op te maken. Met dit script wordt de JSON uitgevoerd in een indeling die tabbladen en spaties bevat: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Het JSON-bestand parseren

Hier volgen enkele voorbeeld opdrachten voor het gebruik van het JSON-bestand met behulp van Power shell. U kunt elke programmeer taal gebruiken waarmee u vertrouwd bent.  

Lees eerst [het JSON-bestand](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) door deze opdracht uit te voeren:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

U kunt de gegevens nu parseren volgens uw scenario. Hieronder vindt u enkele voorbeelden: 

- Alle taak-Id's in het JSON-bestand worden uitgevoerd:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Alle wijzigings-Id's uitvoeren voor gebeurtenissen waarbij de actie ' maken ' is:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Wat u moet weten

Hier volgen enkele tips en overwegingen voor het inrichtings rapport:

- In de Azure Portal worden de gemelde inrichtings gegevens 30 dagen opgeslagen als u een Premium-editie en 7 dagen hebt als u een gratis editie hebt. U kunt de inrichtings logboeken naar [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) publiceren voor retentie na 30 dagen. 

- U kunt het kenmerk ID wijzigen als unieke id gebruiken. Dit is handig wanneer u bijvoorbeeld met product ondersteuning werkt.

- U ziet mogelijk overgeslagen gebeurtenissen voor gebruikers die niet binnen het bereik vallen. Dit wordt verwacht, vooral wanneer het synchronisatie bereik is ingesteld op alle gebruikers en groepen. De service evalueert alle objecten in de Tenant, zelfs degene die buiten het bereik vallen. 

- De inrichtings logboeken zijn momenteel niet beschikbaar in de Government Cloud. Als u geen toegang hebt tot de inrichtings logboeken, gebruikt u de audit Logboeken als tijdelijke oplossing. 

- In de inrichtings logboeken worden geen roldefinities weer gegeven (van toepassing op AWS, Sales Force en Zendesk). U kunt de logboeken voor de functie-import bewerkingen vinden in de audit Logboeken. 

## <a name="error-codes"></a>Foutcodes

Gebruik de volgende tabel voor meer informatie over het oplossen van fouten die u in de inrichtings Logboeken vindt. Geef feedback op met behulp van de koppeling onder aan deze pagina voor eventuele ontbrekende fout codes. 

|Foutcode|Beschrijving|
|---|---|
|Conflict, EntryConflict|Corrigeer de conflicterende kenmerk waarden in azure AD of de toepassing. U kunt ook de overeenkomende kenmerk configuratie controleren als het conflicterende gebruikers account zou moeten overeenkomen en moeten worden overgenomen. Raadpleeg de [documentatie](../app-provisioning/customize-application-attributes.md) voor meer informatie over het configureren van overeenkomende kenmerken.|
|TooManyRequests|De doel-app heeft deze poging om de gebruiker bij te werken geweigerd, omdat deze is overbelast en te veel aanvragen ontvangt. Er is niets te doen. Deze poging wordt automatisch buiten gebruik gesteld. Micro soft is ook op de hoogte gesteld van dit probleem.|
|InternalServerError |De doel-app heeft een onverwachte fout geretourneerd. Een service probleem met de doel toepassing kan verhinderen dat dit werkt. Deze poging wordt binnen 40 minuten automatisch ingetrokken.|
|InsufficientRights, MethodNotAllowed, NotPermitted, niet geautoriseerd| Azure AD is geverifieerd met de doel toepassing maar is niet gemachtigd om de update uit te voeren. Bekijk de instructies die de doel toepassing heeft gegeven, samen met de bijbehorende [zelf studie](../saas-apps/tutorial-list.md)van de toepassing.|
|UnprocessableEntity|De doel toepassing heeft een onverwacht antwoord geretourneerd. De configuratie van de doel toepassing is mogelijk niet juist of een service probleem met de doel toepassing kan verhinderen dat dit werkt.|
|WebExceptionProtocolError |Er is een HTTP-protocol fout opgetreden bij het maken van verbinding met de doel toepassing. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch ingetrokken.|
|InvalidAnchor|Een gebruiker die eerder is gemaakt of die overeenkomt met de inrichtings service, bestaat niet meer. Controleer of de gebruiker bestaat. Als u een nieuwe overeenkomst wilt afdwingen van alle gebruikers, gebruikt u de Microsoft Graph-API om [de taak opnieuw te starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>Bij het opnieuw starten van de inrichting wordt een eerste cyclus geactiveerd. Dit kan enige tijd duren. Bij het opnieuw starten van de inrichting wordt ook de cache verwijderd die door de inrichtings service wordt gebruikt. Dit betekent dat alle gebruikers en groepen in de Tenant opnieuw moeten worden geëvalueerd en dat bepaalde inrichtings gebeurtenissen kunnen worden verwijderd.|
|Niet geïmplementeerd | De doel-app heeft een onverwacht antwoord geretourneerd. De configuratie van de app is mogelijk niet juist of een service probleem met de doel-app kan verhinderen dat dit werkt. Bekijk de instructies die de doel toepassing heeft gegeven, samen met de bijbehorende [zelf studie](../saas-apps/tutorial-list.md)van de toepassing. |
|MandatoryFieldsMissing, MissingValues |De gebruiker kan niet worden gemaakt omdat vereiste waarden ontbreken. Corrigeer de ontbrekende kenmerk waarden in de bron record of Controleer de overeenkomende kenmerk configuratie om ervoor te zorgen dat de vereiste velden niet worden wegge laten. Meer [informatie](../app-provisioning/customize-application-attributes.md) over het configureren van overeenkomende kenmerken.|
|SchemaAttributeNotFound |De bewerking kan niet worden uitgevoerd, omdat er een kenmerk is opgegeven dat niet bestaat in de doel toepassing. Raadpleeg de [documentatie](../app-provisioning/customize-application-attributes.md) over het aanpassen van kenmerken en controleer of de configuratie juist is.|
|InternalError |Er is een interne service fout opgetreden in de Azure AD-inrichtings service. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|InvalidDomain |De bewerking kan niet worden uitgevoerd omdat een kenmerk waarde een ongeldige domein naam bevat. Werk de domein naam op de gebruiker bij of voeg deze toe aan de lijst met toegestane items in de doel toepassing. |
|Time-out |De bewerking kan niet worden voltooid omdat de doel toepassing te lang duurde om te reageren. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|LicenseLimitExceeded|De gebruiker kan niet worden gemaakt in de doel toepassing omdat er geen beschik bare licenties voor deze gebruiker zijn. Meer licenties voor de doel toepassing aanschaffen. U kunt ook uw gebruikers toewijzingen en configuratie van kenmerk toewijzing controleren om ervoor te zorgen dat de juiste gebruikers zijn toegewezen met de juiste kenmerken.|
|DuplicateTargetEntries  |De bewerking kan niet worden voltooid omdat er meer dan één gebruiker in de doel toepassing is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker uit de doel toepassing of [Configureer de kenmerk toewijzingen opnieuw](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | De bewerking kan niet worden voltooid omdat er meer dan één gebruiker met de geconfigureerde overeenkomende kenmerken is gevonden. Verwijder de dubbele gebruiker of [Configureer de kenmerk toewijzingen opnieuw](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Wanneer elke gebruiker wordt geëvalueerd, probeert het systeem de gebruiker te importeren uit het bron systeem. Deze fout treedt doorgaans op wanneer de overeenkomende eigenschap die is gedefinieerd in uw kenmerk toewijzingen ontbreekt voor de gebruiker die wordt geïmporteerd. Zonder dat er een waarde aanwezig is in het gebruikers object voor het overeenkomende kenmerk, kan het systeem geen bereik, overeenkomende of geëxporteerde wijzigingen evalueren. Houd er rekening mee dat de aanwezigheid van deze fout niet aangeeft dat de gebruiker zich in het bereik bevindt, omdat u nog geen bereik hebt geëvalueerd voor de gebruiker.|
|EntrySynchronizationSkipped | De inrichtings service heeft een query uitgevoerd op het bron systeem en heeft de gebruiker geïdentificeerd. Er is geen verdere actie ondernomen voor de gebruiker en deze is overgeslagen. De gebruiker is mogelijk buiten het bereik of de gebruiker bevond al in het doel systeem zonder dat verdere wijzigingen zijn vereist.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Een GET-aanvraag voor het ophalen van een gebruiker of groep die in het antwoord meerdere gebruikers of groepen heeft ontvangen. Het systeem verwacht slechts één gebruiker of groep in het antwoord te ontvangen. Als u [bijvoorbeeld](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)een GET-aanvraag doet om een groep op te halen en een filter op te geven om leden uit te sluiten, en uw systeem voor scim-eind punt (Cross-Domain Identity Management) de leden retourneert, krijgt u deze fout.|

## <a name="next-steps"></a>Volgende stappen

* [De status van het inrichten van gebruikers controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API voor het inrichtings logboek](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)

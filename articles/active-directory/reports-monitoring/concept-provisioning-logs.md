---
title: Inrichtings Logboeken in de Azure Active Directory-Portal (preview) | Microsoft Docs
description: Inleiding tot het inrichten van logboek rapporten in de Azure Active Directory Portal
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
ms.date: 12/28/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56818862b6bc4eb38b819185aceb121e6e78488e
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803524"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Rapporten inrichten in de Azure Active Directory Portal (preview)

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingen** : informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - **Audit logboeken**  -  [Audit logboeken](concept-audit-logs.md) bevatten informatie over de systeem activiteit van gebruikers en groeps beheer, beheerde toepassingen en Directory-activiteiten.
    - **Inrichtings logboeken** : systeem activiteiten bieden over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtings service. 

- **Beveiliging** 
    - **Risk ante aanmeldingen** : een [Risk ante aanmelding](../identity-protection/overview-identity-protection.md) is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
    - **Gebruikers die zijn gemarkeerd voor risico** : een [Risk ante gebruiker](../identity-protection/overview-identity-protection.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

In dit onderwerp vindt u een overzicht van het inrichtings rapport.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie hebben er toegang tot de gegevens?
* Eigen aren van toepassingen kunnen logboeken weer geven voor toepassingen waarvan ze eigenaar zijn
* Gebruikers in de rollen beveiligings beheerder, beveiligings lezer, rapport lezer, toepassings beheerder en Cloud toepassings beheerder
* Gebruikers in een aangepaste rol met de [machtiging provisioningLogs](https://docs.microsoft.com/azure/active-directory/roles/custom-enterprise-app-permissions#full-list-of-permissions)
* Globale beheerders


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Welke Azure AD-licentie hebt u nodig om de inrichtings activiteiten te openen?

Aan uw Tenant moet een Azure AD Premium-licentie zijn gekoppeld om het rapport alle inrichtings activiteiten te bekijken. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. 

## <a name="provisioning-logs"></a>Inrichtingslogboeken

De inrichtings logboeken bieden antwoorden op de volgende vragen:

* Welke groepen zijn met succes gemaakt in ServiceNow?
* Wat zijn de gebruikers van Adobe verwijderd?
* Wat zijn de gebruikers die niet met succes zijn gemaakt in DropBox?

U kunt toegang krijgen tot de inrichtings logboeken door **inrichtings logboeken** te selecteren in de sectie **bewaking** van de Blade **Azure Active Directory** in de [Azure Portal](https://portal.azure.com). Het kan Maxi maal twee uur duren voordat bepaalde inrichtings records worden weer gegeven in de portal.

![Inrichtingslogboeken](./media/concept-provisioning-logs/access-provisioning-logs.png "Inrichtingslogboeken")


Een inrichtings logboek heeft een standaard lijst weergave waarin het volgende wordt weer gegeven:

- De identiteit
- De actie
- Het bron systeem
- Het doel systeem
- De status
- De datum


![Standaard kolommen](./media/concept-provisioning-logs/default-columns.png "Standaard kolommen")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Kolom kiezer](./media/concept-provisioning-logs/column-chooser.png "Kolom kiezer")

Hiermee kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Beschik bare kolommen](./media/concept-provisioning-logs/available-columns.png "Beschik bare kolommen")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Gedetailleerde informatie](./media/concept-provisioning-logs/steps.png "Filteren")


## <a name="filter-provisioning-activities"></a>Inrichtings activiteiten filteren

U kunt uw inrichtings gegevens filteren. Sommige filter waarden worden dynamisch ingevuld op basis van uw Tenant. Als u bijvoorbeeld geen gebeurtenissen voor maken in uw Tenant hebt, is er geen filter optie om te maken.
In de standaard weergave kunt u de volgende filters selecteren:

- Identiteit
- Date
- Status
- Actie


![Filters toevoegen](./media/concept-provisioning-logs/default-filter.png "Filteren")

Met het **identiteits** filter kunt u de naam of de identiteit opgeven die u bevalt. Deze identiteit kan een gebruiker, een groep, een rol of een ander object zijn. U kunt zoeken op de naam of ID van het object. De ID is afhankelijk van het scenario. Wanneer u bijvoorbeeld een object inricht vanuit Azure AD naar Sales Force, is de bron-ID de object-ID van de gebruiker in azure AD terwijl de TargetID de ID van de gebruiker in Sales Force is. Bij het inrichten van workday naar Active Directory, is de bron-ID de werk nemer-ID van de werkdag. Houd er rekening mee dat de naam van de gebruiker mogelijk niet altijd aanwezig is in de identiteits kolom. Er wordt altijd één ID weer. 


Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens.  
Mogelijke waarden zijn:

- 1 maand
- 7 dagen
- 30 dagen
- 24 uur
- Aangepast tijdsinterval

Wanneer u een aangepast tijds bestek selecteert, kunt u een begin-en eind datum configureren.


Met het **status** filter kunt u het volgende selecteren:

- Alles
- Success
- Fout
- Overgeslagen



Met het **actie** filter kunt u het volgende filteren:

- Maken 
- Bijwerken
- Verwijderen
- Uitschakelen
- Overige

Daarnaast kunt u aan de filters van de standaard weergave ook de volgende filters instellen:

- Taak-id
- Cyclus-ID
- ID wijzigen
- Bron-ID
- Doel-ID
- Toepassing


![Kies een veld](./media/concept-provisioning-logs/add-filter.png "Kies een veld")


- **Taak-id** : een unieke taak-id is gekoppeld aan elke toepassing waarvoor u het inrichten hebt ingeschakeld.   

- **Cyclus-id** : Hiermee wordt de inrichtings cyclus uniek geïdentificeerd. U kunt deze ID delen ter ondersteuning van het opzoeken van de cyclus waarin deze gebeurtenis plaatsvond.

- **Wijzig de id** -unieke id voor de inrichtings gebeurtenis. U kunt deze ID delen ter ondersteuning van het opzoeken van de inrichtings gebeurtenis.   


- **Bron systeem** : Hiermee kunt u opgeven waar de identiteit van wordt opgehaald. Bij het inrichten van een object van Azure AD naar ServiceNow, is het bron systeem bijvoorbeeld Azure AD. 

- **Doel systeem** : Hiermee kunt u opgeven waar de identiteit wordt ingericht. Bij het inrichten van een object van Azure AD naar ServiceNow, is het doel systeem bijvoorbeeld ServiceNow. 

- **Toepassing** : Hiermee kunt u alleen records van toepassingen weer geven met een weergave naam die een specifieke teken reeks bevat.

 

## <a name="provisioning-details"></a>Inrichtings gegevens 

Wanneer u een item in de inrichtings lijst weergave selecteert, krijgt u meer informatie over dit item.
De details worden gegroepeerd op basis van de volgende categorieën:

- Stappen

- Problemen oplossen en aanbevelingen

- Gewijzigde eigenschappen

- Samenvatting


![Inrichtings gegevens](./media/concept-provisioning-logs/provisioning-tabs.png "Tabs")



### <a name="steps"></a>Stappen

Het tabblad **stappen** bevat een overzicht van de stappen voor het inrichten van een object. Het inrichten van een object kan bestaan uit vier stappen: 

- Object importeren
- Bepalen of het object binnen bereik is
- Object tussen bron en doel matchen
- Inrichtings object (actie ondernemen: dit kan een maken, bijwerken, verwijderen of uitschakelen zijn)



![Scherm afbeelding toont het tabblad stappen, waarin de inrichtings stappen worden weer gegeven.](./media/concept-provisioning-logs/steps.png "Filteren")


### <a name="troubleshoot-and-recommendations"></a>Problemen oplossen en aanbevelingen


Op het tabblad **probleem oplossing en aanbevelingen** vindt u de fout code en de reden. De fout gegevens zijn alleen beschikbaar in het geval van een fout. 


### <a name="modified-properties"></a>Gewijzigde eigenschappen

De **gewijzigde eigenschappen** bevat de oude waarde en nieuwe waarde. In gevallen waarin er geen oude waarde is, is de kolom oude waarde leeg. 


### <a name="summary"></a>Samenvatting

Op het tabblad **samen vatting** vindt u een overzicht van wat er is gebeurd en de id's voor het object in het bron-en doel systeem. 

## <a name="what-you-should-know"></a>Wat u moet weten

- In de Azure Portal worden de gemelde inrichtings gegevens 30 dagen opgeslagen als u een Premium-editie en 7 dagen hebt als u een gratis editie hebt. De inrichtings logboeken kunnen worden gepubliceerd naar [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) voor retentie na 30 dagen. 

- U kunt het kenmerk ID wijzigen als unieke id gebruiken. Dit is bijvoorbeeld handig bij interactie met product ondersteuning.

- Er is momenteel geen optie om inrichtings gegevens te downloaden als een CSV-bestand, maar u kunt de gegevens exporteren met behulp van [Microsoft Graph](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta).

- Er worden mogelijk overgeslagen gebeurtenissen weer geven voor gebruikers die zich niet in het bereik bevinden. Dit wordt verwacht, vooral wanneer het synchronisatie bereik is ingesteld op alle gebruikers en groepen. Onze service evalueert alle objecten in de Tenant, zelfs degene die buiten het bereik vallen. 

- De inrichtings logboeken zijn momenteel niet beschikbaar in de Government Cloud. Als u geen toegang hebt tot de inrichtings logboeken, moet u de audit Logboeken gebruiken als tijdelijke oplossing. 

- In de inrichtings logboeken worden geen functie-import bewerkingen weer gegeven (is van toepassing op AWS, Sales Force en ZenDesk). De logboeken voor de functie-import bewerkingen vindt u in de audit Logboeken. 

## <a name="error-codes"></a>Foutcodes

Gebruik de onderstaande tabel voor meer informatie over het oplossen van fouten die u in de inrichtings Logboeken kunt vinden. Geef feedback met behulp van de koppeling onder aan deze pagina voor eventuele ontbrekende fout codes. 

|Foutcode|Beschrijving|
|---|---|
|Conflict, EntryConflict|Corrigeer de conflicterende kenmerk waarden in azure AD of de toepassing of Controleer de overeenkomende kenmerk configuratie als het conflicterende gebruikers account zou moeten overeenkomen en moeten worden overgenomen. Raadpleeg de volgende [documentatie](../app-provisioning/customize-application-attributes.md) voor meer informatie over het configureren van overeenkomende kenmerken.|
|TooManyRequests|De doel-app heeft deze poging geweigerd de gebruiker bij te werken omdat deze is overbelast en te veel aanvragen ontvangt. Er is niets te doen. Deze poging wordt automatisch buiten gebruik gesteld. Micro soft is ook op de hoogte gesteld van dit probleem.|
|InternalServerError |De doel-app heeft een onverwachte fout geretourneerd. Er is mogelijk een service probleem met de doel toepassing die verhindert dat dit werkt. Deze poging wordt binnen 40 minuten automatisch ingetrokken.|
|InsufficientRights, MethodNotAllowed, NotPermitted, niet geautoriseerd| Azure AD kan worden geverifieerd met de doel toepassing, maar is niet gemachtigd om de update uit te voeren. Lees alle instructies van de doel toepassing en de bijbehorende [zelf studie](../saas-apps/tutorial-list.md)over toepassingen.|
|UnprocessableEntity|De doel toepassing heeft een onverwacht antwoord geretourneerd. De configuratie van de doel toepassing is mogelijk niet juist of er is mogelijk een service probleem met de doel toepassing die verhindert dat dit werkt.|
|WebExceptionProtocolError |Er is een HTTP-protocol fout opgetreden tijdens het verbinden met de doel toepassing. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch ingetrokken.|
|InvalidAnchor|Een gebruiker die eerder is gemaakt of die overeenkomt met de inrichtings service, bestaat niet meer. Controleer of de gebruiker bestaat. Als u een nieuwe overeenkomst wilt afdwingen van alle gebruikers, gebruikt u de MS Graph API om de [taak opnieuw te starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Houd er rekening mee dat bij het opnieuw starten van de inrichting een eerste cyclus wordt geactiveerd. Dit kan enige tijd duren. Ook wordt de cache verwijderd die wordt gebruikt door de inrichtings service, wat betekent dat alle gebruikers en groepen in de Tenant opnieuw moeten worden geëvalueerd en dat bepaalde inrichtings gebeurtenissen kunnen worden verwijderd.|
|Niet geïmplementeerd | De doel-app heeft een onverwacht antwoord geretourneerd. De configuratie van de app is mogelijk niet juist of er is mogelijk een service probleem met de doel-app die verhindert dat dit werkt. Lees alle instructies van de doel toepassing en de bijbehorende [zelf studie](../saas-apps/tutorial-list.md)over toepassingen. |
|MandatoryFieldsMissing, MissingValues |De gebruiker kan niet worden gemaakt omdat vereiste waarden ontbreken. Corrigeer de ontbrekende kenmerk waarden in de bron record of Controleer de overeenkomende kenmerk configuratie om ervoor te zorgen dat de vereiste velden niet worden wegge laten. Meer [informatie](../app-provisioning/customize-application-attributes.md) over het configureren van overeenkomende kenmerken.|
|SchemaAttributeNotFound |Kan de bewerking niet uitvoeren omdat er een kenmerk is opgegeven dat niet bestaat in de doel toepassing. Raadpleeg de [documentatie](../app-provisioning/customize-application-attributes.md) over het aanpassen van kenmerken en zorg ervoor dat uw configuratie juist is.|
|InternalError |Er is een interne service fout opgetreden in de Azure AD-inrichtings service. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|InvalidDomain |De bewerking kan niet worden uitgevoerd vanwege een kenmerk waarde met een ongeldige domein naam. Werk de domein naam op de gebruiker bij of voeg deze toe aan de lijst met toegestane items in de doel toepassing. |
|Time-out |De bewerking kan niet worden voltooid omdat de doel toepassing te lang duurde om te reageren. Er is niets te doen. Deze poging wordt binnen 40 minuten automatisch opnieuw geprobeerd.|
|LicenseLimitExceeded|De gebruiker kan niet worden gemaakt in de doel toepassing omdat er geen beschik bare licenties voor deze gebruiker zijn. U kunt aanvullende licenties voor de doel toepassing aanschaffen of uw gebruikers toewijzingen en configuratie van kenmerk toewijzing controleren om ervoor te zorgen dat de juiste gebruikers zijn toegewezen met de juiste kenmerken.|
|DuplicateTargetEntries  |De bewerking kan niet worden voltooid omdat er meer dan één gebruiker in de doel toepassing is gevonden met de geconfigureerde overeenkomende kenmerken. Verwijder de dubbele gebruiker uit de doel toepassing of configureer de kenmerk toewijzingen opnieuw, zoals [hier](../app-provisioning/customize-application-attributes.md)wordt beschreven.|
|DuplicateSourceEntries | De bewerking kan niet worden voltooid omdat er meer dan één gebruiker met de geconfigureerde overeenkomende kenmerken is gevonden. Verwijder de dubbele gebruiker of configureer de kenmerk toewijzingen opnieuw, zoals [hier](../app-provisioning/customize-application-attributes.md)wordt beschreven.|
|ImportSkipped | Wanneer elke gebruiker wordt geëvalueerd, wordt geprobeerd de gebruiker te importeren uit het bron systeem. Deze fout treedt doorgaans op wanneer de gebruiker die wordt geïmporteerd, de overeenkomende eigenschap die is gedefinieerd in uw kenmerk toewijzingen ontbreekt. Zonder dat er een waarde aanwezig is in het gebruikers object voor het overeenkomende kenmerk, kunnen we geen bereik-, zoek-of export wijzigingen evalueren. Let op: de aanwezigheid van deze fout geeft niet aan dat de gebruiker zich in het bereik bevindt omdat er nog geen bereik voor de gebruiker is geëvalueerd.|
|EntrySynchronizationSkipped | De inrichtings service heeft een query uitgevoerd op het bron systeem en heeft de gebruiker geïdentificeerd. Er is geen verdere actie ondernomen voor de gebruiker en deze is overgeslagen. De overs Laan kan worden veroorzaakt door de gebruiker buiten het bereik of de gebruiker die al in het doel systeem aanwezig is, zonder verdere wijzigingen vereist.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Bij het uitvoeren van een GET-aanvraag om een gebruiker of groep op te halen, hebben we meerdere gebruikers of groepen in het antwoord ontvangen. Verwacht wordt dat er slechts één gebruiker of groep in het antwoord wordt ontvangen. Als we [bijvoorbeeld](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)een GET-aanvraag doen om een groep op te halen en een filter op te geven om leden uit te sluiten en uw scim-eind punt de leden retourneert, wordt deze fout gegenereerd.|

## <a name="next-steps"></a>Volgende stappen

* [De status van het inrichten van gebruikers controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem.md)
* [Graph-API voor het inrichtings logboek](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)

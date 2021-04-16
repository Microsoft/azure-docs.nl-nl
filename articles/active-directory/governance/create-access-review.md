---
title: Een toegangsbeoordeling maken van groepen & toepassingen - Azure AD
description: Meer informatie over het maken van een toegangsbeoordeling van groepsleden of toegang tot toepassingen in Azure Active Directory toegangsbeoordelingen.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532269"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangsbeoordeling maken van groepen en toepassingen in Azure AD-toegangsbeoordelingen

De toegang tot groepen en toepassingen voor werknemers en gasten verandert in de tijd. Om het risico van verouderde toegangstoewijzingen te verminderen, kunnen beheerders Azure Active Directory (Azure AD) gebruiken om toegangsbeoordelingen te maken voor groepsleden of toegang tot toepassingen. Als u de toegang regelmatig wilt controleren, kunt u ook terugkerende toegangsbeoordelingen maken. Zie Gebruikerstoegang beheren en Gasttoegang beheren voor [meer](manage-user-access-with-access-reviews.md) informatie [over deze scenario's.](manage-guest-access-with-access-reviews.md)

U kunt een korte video bekijken waarin wordt gesproken over het inschakelen van toegangsbeoordelingen:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

In dit artikel wordt beschreven hoe u een of meer toegangsbeoordelingen maakt voor groepsleden of toegang tot toepassingen.

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder of gebruikersbeheerder

Zie [Licentievereisten](access-reviews-overview.md#license-requirements) voor meer informatie.

## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangsbeoordelingen maken

1. Meld u aan bij Azure Portal en open de [pagina Identiteitsbeheer.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

2. Klik in het menu links op **Toegangsbeoordelingen.**

3. Klik **op Nieuwe toegangsbeoordeling** om een nieuwe toegangsbeoordeling te maken.

    ![Deelvenster Toegangsbeoordelingen in Identiteitsbeheer](./media/create-access-review/access-reviews.png)

4. Selecteer **in Stap 1: selecteer wat u wilt controleren de** resource die u wilt bekijken.

    ![Een toegangsbeoordeling maken - Naam en beschrijving controleren](./media/create-access-review/select-what-review.png)

5. Als u **teams en groepen hebt geselecteerd** in stap 1, hebt u twee opties in stap 2
   - **Alle Microsoft 365 groepen met gastgebruikers.** Selecteer deze optie als u terugkerende beoordelingen wilt maken voor al uw gastgebruikers in al uw Microsoft Teams- en M365-groepen in uw organisatie. U kunt ervoor kiezen om bepaalde groepen uit te sluiten door te klikken op Select group(s) to exclude.
   - **Selecteer teams en groepen.** Selecteer deze optie als u een eindige set teams en/of groepen wilt opgeven die u wilt controleren. Nadat u op deze optie hebt geklikt, ziet u een lijst met groepen aan de rechterkant waar u uit kunt kiezen.

     ![Teams en groepen](./media/create-access-review/teams-groups.png)

     ![Teams en groepen die zijn gekozen in de gebruikersinterface](./media/create-access-review/teams-groups-detailed.png)

6. Als u Toepassingen **in stap** 1 hebt geselecteerd, kunt u een of meer toepassingen selecteren in stap 2.

    >[!NOTE]
    > Als u meerdere groepen en/of toepassingen selecteert, worden er meerdere toegangsbeoordelingen gemaakt. Als u bijvoorbeeld 5 groepen selecteert om te controleren, resulteert dit in 5 afzonderlijke toegangsbeoordelingen

   ![De interface die wordt weergegeven als u toepassingen hebt gekozen in plaats van groepen](./media/create-access-review/select-application-detailed.png)

7. Vervolgens kunt u in stap 3 een bereik voor de beoordeling selecteren. Uw opties zijn
   - **Alleen gastgebruikers.** Als u deze optie selecteert, wordt de toegangsbeoordeling beperkt tot alleen de Azure AD B2B-gastgebruikers in uw directory.
   - **Iedereen.** Als u deze optie selecteert, wordt de toegangsbeoordeling beperkt tot alle gebruikersobjecten die aan de resource zijn gekoppeld.

    >[!NOTE]
    > Als u Alle Microsoft 365 met gastgebruikers hebt geselecteerd in stap 2, kunt u alleen gastgebruikers controleren in stap 3

8. Klik op Volgende: Beoordelingen
9. Selecteer in **de sectie Revisoren** selecteren een of meer personen om de toegangsbeoordelingen uit te voeren. U kunt kiezen uit:
    - **Groepseigenaar(s)** (alleen beschikbaar bij het uitvoeren van een beoordeling voor een team of groep)
    - **Geselecteerde gebruikers of groepen**
    - **Gebruikers beoordelen hun eigen toegang**
    - **Managers van gebruikers.**
    Als u managers van gebruikers **of** **groepseigenaren**  kiest, hebt u ook de mogelijkheid om een revisor voor terugval op te geven. Revisoren van terugval worden gevraagd om een beoordeling uit te doen wanneer de gebruiker geen manager heeft opgegeven in de directory of als de groep geen eigenaar heeft.

    ![nieuwe toegangsbeoordeling](./media/create-access-review/new-access-review.png)

10. In de **sectie Terugkeerpatroon van beoordeling** opgeven kunt u een frequentie opgeven, zoals **Wekelijks, Maandelijks, Elk kwartaal, Halfjaarlijks, Jaarlijks.** Vervolgens geeft u een **Duur op,** waarmee wordt bepaald hoe lang een beoordeling moet worden geopend voor invoer van revisoren. De maximale duur die u kunt instellen voor een maandelijkse beoordeling is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voorkomen. Mogelijk wilt u de duur verkorten om ervoor te zorgen dat de invoer van uw revisoren eerder wordt toegepast. Vervolgens kunt u een **begindatum en** **einddatum selecteren.**

    ![Kiezen hoe vaak de beoordeling moet plaatsvinden](./media/create-access-review/frequency.png)

11. Klik op **de knop Volgende:** Instellingen onder aan de pagina
12. In de **instellingen bij voltooiing kunt** u opgeven wat er gebeurt nadat de beoordeling is voltooid

    ![Een toegangsbeoordeling maken - na voltooiingsinstellingen](./media/create-access-review/upon-completion-settings-new.png)

Als u de toegang voor geweigerde gebruikers automatisch wilt verwijderen, stelt u Resultaten automatisch toepassen op resource in op Inschakelen. Als u de resultaten handmatig wilt toepassen wanneer de beoordeling is voltooid, stelt u de schakelaar in op Uitschakelen.
Gebruik de lijst Als revisoren niet reageren om op te geven wat er gebeurt voor gebruikers die niet binnen de beoordelingsperiode door de revisor worden beoordeeld. Deze instelling heeft geen invloed op gebruikers die handmatig zijn beoordeeld door de beoordelaars. Als de uiteindelijke revisor besluit Weigeren te zijn, wordt de toegang van de gebruiker verwijderd.

- **Geen wijziging:** de toegang van de gebruiker ongewijzigd laten
- **Toegang verwijderen** : de toegang van de gebruiker verwijderen
- **Toegang goedkeuren** - Gebruikerstoegang goedkeuren
- **Aanbevelingen doen:** neem de aanbeveling van het systeem op voor het weigeren of goedkeuren van de voortdurende toegang van de gebruiker

    ![Opties voor instellingen na voltooiing](./media/create-access-review/upon-completion-settings-new.png)

Gebruik de actie om toe te passen op **geweigerde gastgebruikers** om op te geven wat er gebeurt met gastgebruikers als ze worden geweigerd.
- Als u het lidmaatschap van de gebruiker uit de resource verwijdert, wordt de toegang van de gebruiker tot de groep of toepassing die wordt gecontroleerd verwijderd. De gebruiker kan zich nog steeds aanmelden bij de tenant.
- Als de gebruiker zich 30 dagen lang niet kan aanmelden en vervolgens de gebruiker uit de tenant verwijdert, kunnen de geweigerde gebruikers zich niet aanmelden bij de tenant, ongeacht of ze toegang hebben tot andere resources. Als er een fout is gemaakt of als een beheerder besluit om de toegang opnieuw in te stellen, kan deze binnen 30 dagen nadat de gebruiker is uitgeschakeld dit doen. Als er geen actie wordt ondernomen voor de uitgeschakelde gebruikers, worden ze verwijderd uit de tenant.

Lees het artikel Use Azure AD Identity Governance to review and remove external users who longer have resource access (Gebruik Azure AD Identity Governance om externe gebruikers die geen toegang meer hebben tot resources) te controleren en verwijderen voor meer informatie over best practices voor het verwijderen van gastgebruikers die geen toegang meer hebben tot resources in uw [organisatie.](access-reviews-external-users.md)

   >[!NOTE]
   >Actie die moet worden toegepast op geweigerde gastgebruikers kan niet worden geconfigureerd voor beoordelingen die zijn beperkt tot meer dan gastgebruikers. Het kan ook niet worden geconfigureerd voor beoordelingen van **alle M365-groepen met gastgebruikers.** Wanneer dit niet configureerbaar is, wordt de standaardoptie voor het verwijderen van het lidmaatschap van de gebruiker uit de resource gebruikt bij geweigerde gebruikers.

13. Kies in **de helpers voor beoordelingsbeslissingen** inschakelen of u wilt dat uw revisor aanbevelingen ontvangt tijdens het beoordelingsproces.

    ![Opties voor beslissingshulp inschakelen](./media/create-access-review/helpers.png)

14. In de **sectie Geavanceerde instellingen** kunt u het volgende kiezen:
    - Stel **Reden vereist in** op **Inschakelen** om te vereisen dat de revisor een reden voor goedkeuring oplevert.
    - Stel **e-mailmeldingen** in op Inschakelen om azure AD e-mailmeldingen te laten verzenden naar revisoren wanneer een toegangsbeoordeling wordt gestart en naar beheerders wanneer een beoordeling is voltooid. 
    - Stel **Herinneringen in** op **Inschakelen** om Azure AD herinneringen over toegangsbeoordelingen te laten verzenden naar revisoren die hun beoordeling niet hebben voltooid. Deze herinneringen zijn halverwege de duur van de beoordeling zelf.
    - De inhoud van het e-mailbericht dat naar revisoren wordt verzonden, wordt automatisch gegenereerd op basis van de beoordelingsdetails, zoals de naam van de beoordeling, de resourcenaam, de vervaldatum, enzovoort. Als u een manier nodig hebt om aanvullende informatie te verstrekken, zoals aanvullende instructies of contactgegevens, kunt u deze gegevens opgeven in de sectie Aanvullende inhoud voor e-mail **van revisor.** De informatie die u op entert, is opgenomen in de uitnodigings- en herinneringsmails die worden verzonden naar toegewezen revisoren. In de sectie die is gemarkeerd in de onderstaande afbeelding ziet u waar deze informatie wordt weergegeven.


      ![aanvullende inhoud voor revisor](./media/create-access-review/additional-content-reviewer.png)

15. Klik op **Volgende: Controleren en maken om** naar de volgende pagina te gaan
16. Noem de toegangsbeoordeling. Geef eventueel een beschrijving op voor de beoordeling. De naam en beschrijving worden weergegeven voor de revisoren.
17. Controleer de informatie en selecteer **Maken**

       ![beoordelingsscherm maken](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>De toegangsbeoordeling starten

Nadat u de instellingen voor een toegangsbeoordeling hebt opgegeven, klikt u op **Start.** De toegangsbeoordeling wordt weergegeven in uw lijst met een indicator van de status.

![Lijst met toegangsbeoordelingen en hun status](./media/create-access-review/access-reviews-list.png)

Standaard verzendt Azure AD kort nadat de beoordeling is gestart een e-mail naar revisoren. Als u ervoor kiest om azure AD geen e-mail te laten verzenden, moet u de beoordelaars informeren dat er een toegangsbeoordeling wacht totdat deze is voltooid. U kunt de instructies voor het controleren van toegang tot groepen [of toepassingen aan hen tonen.](perform-access-review.md) Als uw beoordeling is dat gasten hun eigen toegang kunnen beoordelen, laat ze dan de instructies zien voor het beoordelen van de toegang voor [uzelf tot groepen of toepassingen.](review-your-access.md)

Als u gasten hebt toegewezen als revisoren en ze de uitnodiging niet hebben geaccepteerd, ontvangen ze geen e-mail van toegangsbeoordelingen omdat ze de uitnodiging eerst moeten accepteren voordat ze de uitnodiging beoordelen.

## <a name="access-review-status-table"></a>Statustabel voor toegangsbeoordeling

| Status | Definitie |
|--------|------------|
|Niet gestart | Beoordeling is gemaakt, gebruikersdetectie wacht om te starten. |
|Initialiseren   | Er wordt een gebruikersdetectie uitgevoerd om alle gebruikers te identificeren die deel uitmaken van de beoordeling. |
|Starten | De beoordeling wordt begonnen. Als e-mailmeldingen zijn ingeschakeld, worden e-mailberichten verzonden naar revisoren. |
|InProgress | De beoordeling is gestart. Als e-mailmeldingen zijn ingeschakeld, zijn e-mailberichten verzonden naar revisoren. Revisoren kunnen beslissingen indienen tot de einddatum. |
|Voltooien | De beoordeling wordt voltooid en e-mailberichten worden verzonden naar de eigenaar van de beoordeling. |
|Automatisch controleren | Beoordeling is in een systeembeoordelingsfase. Het systeem neemt beslissingen voor gebruikers die niet zijn beoordeeld op basis van aanbevelingen of vooraf geconfigureerde beslissingen. |
|Automatisch beoordeeld | Beslissingen zijn vastgelegd door het systeem voor alle gebruikers die niet zijn beoordeeld. Controleren is gereed om door **te gaan met Toepassen** als Automatisch toepassen is ingeschakeld. |
|Toepassing | De toegang voor goedgekeurde gebruikers wordt niet gewijzigd. |
|Toegepast | Geweigerde gebruikers, indien vandaan, zijn verwijderd uit de resource of map. |
|Mislukt | De beoordeling kan niet worden uitgevoerd. Deze fout kan betrekking hebben op het verwijderen van de tenant, een wijziging in licenties of andere interne tenantwijzigingen. |

## <a name="create-reviews-via-apis"></a>Beoordelingen maken via API's

U kunt ook toegangsbeoordelingen maken met behulp van API's. Wat u doet voor het beheren van toegangsbeoordelingen van groepen en toepassingsgebruikers in de Azure Portal kunt u ook doen met behulp Microsoft Graph API's. Zie de API-verwijzing [voor Azure AD-toegangsbeoordelingen voor meer informatie.](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) Zie Voorbeeld van het ophalen van [Azure AD-toegangsbeoordelingen via](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)Microsoft Graph voor een codevoorbeeld.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot groepen of toepassingen beoordelen](perform-access-review.md)
- [Toegang tot groepen of toepassingen voor uzelf controleren](review-your-access.md)
- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
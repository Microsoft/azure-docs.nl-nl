---
title: Een toegangs beoordeling maken voor groepen & toepassingen-Azure AD
description: Meer informatie over het maken van een toegangs beoordeling van groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
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
ms.openlocfilehash: 7143c3f9786d41c32ae954ab219197a9cfaa1050
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176872"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen en toepassingen in azure AD-toegangs beoordelingen

De toegang tot groepen en toepassingen voor werk nemers en gasten verandert in de loop van de tijd. Beheerders kunnen Azure Active Directory (Azure AD) gebruiken voor het maken van toegangs Beoordelingen voor groeps leden of toegang tot toepassingen om het risico te verminderen dat is gekoppeld aan verouderde toegangs toewijzingen. Als u de toegang regel matig wilt controleren, kunt u ook terugkerende toegangs beoordelingen maken. Zie [gebruikers toegang beheren](manage-user-access-with-access-reviews.md) en [gast toegang beheren](manage-guest-access-with-access-reviews.md)voor meer informatie over deze scenario's.

U kunt een snel video gesprek bekijken over het inschakelen van toegangs beoordelingen:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

In dit artikel wordt beschreven hoe u een of meer toegangs Beoordelingen voor groeps leden of toegang tot toepassingen maakt.

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder of gebruikers beheerder

Zie [Licentievereisten](access-reviews-overview.md#license-requirements) voor meer informatie.

## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangs beoordelingen maken

1. Meld u aan bij de Azure Portal en open de [pagina voor identiteits](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)beheer.

2. Klik in het menu links op **toegangs beoordelingen**.

3. Klik op **nieuwe toegangs beoordeling** om een nieuwe toegangs beoordeling te maken.

    ![Deel venster toegangs beoordelingen in Identity governance](./media/create-access-review/access-reviews.png)

4. Selecteer in **stap 1: selecteren wat** u wilt controleren welke resource u wilt bekijken.

    ![Een toegangs beoordeling maken-naam en beschrijving van de beoordeling](./media/create-access-review/select-what-review.png)

5. Als u in stap 1 **teams + groepen** hebt geselecteerd, hebt u twee opties in stap 2
   - **Alle Microsoft 365 groepen met gast gebruikers.** Selecteer deze optie als u terugkerende beoordelingen wilt maken voor al uw gast gebruikers in alle micro soft teams-en M365-groepen in uw organisatie. U kunt ervoor kiezen om bepaalde groepen uit te sluiten door te klikken op groep (en) selecteren om uit te sluiten.
   - **Selecteer teams + groepen.** Selecteer deze optie als u een eindige set teams en/of groepen wilt opgeven die u wilt controleren. Nadat u op deze optie hebt geklikt, ziet u een lijst met groepen aan de rechter kant waaruit u kunt kiezen.

     ![Teams en groepen](./media/create-access-review/teams-groups.png)

     ![Teams en groepen die u hebt gekozen in de gebruikers interface](./media/create-access-review/teams-groups-detailed.png)

6. Als u **toepassingen** hebt geselecteerd in stap 1, kunt u vervolgens een of meer toepassingen selecteren in stap 2.

    >[!NOTE]
    > Als u meerdere groepen en/of toepassingen selecteert, worden er meerdere toegangs beoordelingen gemaakt. Als u bijvoorbeeld 5 groepen selecteert om te controleren, resulteert dat in 5 afzonderlijke toegangs beoordelingen

   ![De interface die wordt weer gegeven als u toepassingen in plaats van groepen hebt gekozen](./media/create-access-review/select-application-detailed.png)

7. Vervolgens kunt u in stap 3 een bereik voor de beoordeling selecteren. Uw opties zijn
   - **Alleen gast gebruikers.** Als u deze optie selecteert, wordt de toegangs beoordeling beperkt tot alleen de Azure AD B2B-gast gebruikers in uw Directory.
   - **Iedereen.** Als u deze optie selecteert, wordt de toegangs beoordeling toegepast op alle gebruikers objecten die zijn gekoppeld aan de resource.

    >[!NOTE]
    > Als u in stap 2 alle groepen Microsoft 365 hebt geselecteerd met gast gebruikers, is de enige optie om gast gebruikers te controleren in stap 3

8. Klik op volgende: Recensies
9. Selecteer in de sectie **controleurs selecteren** een of meer personen om de toegangs beoordelingen uit te voeren. U kunt kiezen uit:
    - **Groeps eigenaar (s)** (alleen beschikbaar als u een beoordeling uitvoert op een team of groep)
    - **Geselecteerde gebruiker (s) of groepen (en)**
    - **Gebruikers controleren eigen toegang**
    - **Managers van gebruikers.**
    Als u een **van beide managers van gebruikers** of **groeps eigenaren**  kiest, hebt u ook de optie om een terugval revisor op te geven. Terugval controleurs wordt gevraagd een beoordeling uit te voeren als de gebruiker geen beheer heeft opgegeven in de map of als de groep geen eigenaar heeft.

    ![nieuwe toegangs beoordeling](./media/create-access-review/new-access-review.png)

10. In de sectie **terugkeer patroon van controle opgeven** kunt u een frequentie opgeven, zoals **wekelijks, maandelijks, elk kwar taal, jaarlijks**. Vervolgens geeft u een **duur** op waarmee wordt gedefinieerd hoe lang een beoordeling voor invoer van revisors is geopend. De maximale duur die u voor een maandelijkse beoordeling kunt instellen is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voor komen. Mogelijk wilt u de duur verkorten om ervoor te zorgen dat uw revisoren eerder zijn ingevoerd. Daarna kunt u een **begin datum** en **eind datum** selecteren.

    ![Kiezen hoe vaak de beoordeling moet plaatsvinden](./media/create-access-review/frequency.png)

11. Klik op de **volgende knop: instellingen** aan de onderkant van de pagina
12. In de **instellingen bij voltooiing** kunt u opgeven wat er gebeurt nadat de controle is voltooid

    ![Instellingen voor een toegangs beoordeling maken-bij voltooiing](./media/create-access-review/upon-completion-settings-new.png)

Als u de toegang voor geweigerde gebruikers automatisch wilt verwijderen, stelt u automatisch Toep assen resultaten in op resource om in te scha kelen. Als u de resultaten hand matig wilt Toep assen wanneer de controle is voltooid, stelt u de switch in op uitschakelen.
Gebruik de lijst als revisoren niet reageren om op te geven wat er gebeurt voor gebruikers die niet worden gecontroleerd door de revisor binnen de beoordelings periode. Deze instelling heeft geen invloed op gebruikers die hand matig door de controleurs zijn gecontroleerd. Als de laatste beslissing van de revisor weigert, wordt de toegang van de gebruiker verwijderd.

- **Geen wijziging** -gebruikers toegang ongewijzigd laten
- **Toegang verwijderen** -de toegang van de gebruiker verwijderen
- **Toegang goed keuren** : de toegang van de gebruiker goed keuren
- **Aanbevelingen doen** : de aanbeveling van het systeem voor het weigeren of goed keuren van de permanente toegang van de gebruiker

    ![Bij opties voor voltooiings instellingen](./media/create-access-review/upon-completion-settings-new.png)

Gebruik de actie om toe te passen op geweigerde **gast** gebruikers om op te geven wat er gebeurt met gast gebruikers als ze worden geweigerd.
- Als u het lidmaatschap van de gebruiker van de resource verwijdert, wordt de toegang van geweigerde gebruikers tot de groep of toepassing die wordt gecontroleerd, verwijderd, maar kunnen ze zich nog wel aanmelden bij de Tenant.
- Blok keren dat de gebruiker zich 30 dagen aanmeldt en vervolgens gebruiker verwijderen van de Tenant blokkeert, kunnen de geweigerde gebruikers zich niet aanmelden bij de Tenant, ongeacht of ze toegang hebben tot andere resources. Als er een fout is opgetreden of als een beheerder de toegang tot het account opnieuw inschakelt, kan dit binnen 30 dagen na het uitschakelen van de gebruiker. Als er geen actie is uitgevoerd op de uitgeschakelde gebruikers, worden deze verwijderd uit de Tenant.

Lees voor meer informatie over aanbevolen procedures voor het verwijderen van gast gebruikers die geen toegang meer hebben tot resources in uw organisatie het artikel [Azure AD Identity governance gebruiken om externe gebruikers te controleren en te verwijderen die niet langer toegang hebben tot bronnen.](access-reviews-external-users.md)

   >[!NOTE]
   >De actie die moet worden toegepast op geweigerde gast gebruikers kan niet worden geconfigureerd voor beoordelingen die zijn gericht aan meer dan gast gebruikers. Het kan ook niet worden geconfigureerd voor beoordelingen van **alle M365-groepen met gast gebruikers.** Wanneer niet kan worden geconfigureerd, wordt de standaard optie voor het verwijderen van het lidmaatschap van de gebruiker uit de resource gebruikt voor geweigerde gebruikers.

13. In de **helpers voor beoordelings besluit inschakelen** kunt u kiezen of u wilt dat uw revisor tijdens het controle proces aanbevelingen ontvangt.

    ![Opties voor de beslissings hulp inschakelen](./media/create-access-review/helpers.png)

14. In de sectie **Geavanceerde instellingen** kunt u het volgende kiezen:
    - Stel een reden in die **vereist** is om de revisor in te **scha kelen** om een redenen voor goed keuring op te geven.
    - Stel **e-mail meldingen** in om ervoor te **zorgen** dat Azure AD e-mail meldingen naar revisoren verzendt wanneer een toegangs beoordeling wordt gestart en aan beheerders wanneer een controle is voltooid.
    - Stel **herinneringen** in om ervoor te **zorgen** dat Azure AD herinneringen voor toegangs beoordelingen verzendt naar revisoren die hun beoordeling nog niet hebben voltooid. Deze herinneringen zijn zelf halverwege de duur van de beoordeling.
    - De inhoud van het e-mail bericht dat naar revisoren wordt verzonden, wordt automatisch gegenereerd op basis van de details van de beoordeling, zoals de naam van de beoordeling, de resource naam, de verval datum, enzovoort. Als u meer informatie wilt kunnen communiceren, zoals aanvullende instructies of contact gegevens, kunt u deze gegevens opgeven in de sectie **aanvullende inhoud voor revisor e-mail** . De informatie die u invoert, wordt opgenomen in de uitnodiging en herinneringen per e-mail die zijn verzonden naar toegewezen revisoren. In de sectie in de onderstaande afbeelding ziet u waar deze informatie wordt weer gegeven.


      ![aanvullende inhoud voor revisor](./media/create-access-review/additional-content-reviewer.png)

15. Klik op **volgende: controleren + maken** om naar de volgende pagina te gaan
16. Geef de toegangs beoordeling een naam. U kunt eventueel een beschrijving van de beoordeling opgeven. De naam en beschrijving worden weer gegeven aan de controleurs.
17. Bekijk de informatie en selecteer **maken**

       ![controle scherm maken](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>De toegangs beoordeling starten

Zodra u de instellingen voor een toegangs beoordeling hebt opgegeven, klikt u op **Start**. De toegangs beoordeling wordt weer gegeven in de lijst met een indicator van de status.

![Lijst met toegangs beoordelingen en hun status](./media/create-access-review/access-reviews-list.png)

Standaard verzendt Azure AD een e-mail naar revisoren kort nadat de controle is gestart. Als u ervoor kiest om Azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangs beoordeling wacht totdat deze is voltooid. U kunt de instructies weer geven voor het controleren van de [toegang tot groepen of toepassingen](perform-access-review.md). Als u wilt controleren of gasten hun eigen toegang kunnen bekijken, geeft u de instructies weer voor het [controleren van de toegang tot groepen of toepassingen](review-your-access.md).

Als gasten als revisoren zijn toegewezen en ze de uitnodiging niet hebben geaccepteerd, ontvangen ze geen e-mail berichten van toegangs beoordelingen omdat ze de uitnodiging eerst moeten accepteren voordat ze kunnen beoordelen.

## <a name="access-review-status-table"></a>Status tabel toegangs beoordeling

| Status | Definitie |
|--------|------------|
|NotStarted | De controle is gemaakt, de gebruikers detectie wacht op starten. |
|Initialiseren   | Gebruikers detectie wordt uitgevoerd om alle gebruikers te identificeren die deel uitmaken van de beoordeling. |
|Starten | De beoordeling wordt gestart. Als e-mail meldingen zijn ingeschakeld, worden e-mails naar revisoren verzonden. |
|InProgress | De beoordeling is gestart. Als e-mail meldingen zijn ingeschakeld, worden e-mails naar revisoren verzonden. Revisoren kunnen beslissingen verzenden tot de verval datum. |
|Invullen | De beoordeling wordt voltooid en e-mail berichten worden verzonden naar de eigenaar van de beoordeling. |
|Automatisch controleren | Beoordeling bevindt zich in een systeem revisie fase. Het systeem is bezig met het vastleggen van beslissingen voor gebruikers die niet zijn gecontroleerd op basis van aanbevelingen of vooraf geconfigureerde beslissingen. |
|Automatisch gecontroleerd | Er zijn beslissingen vastgelegd door het systeem voor alle gebruikers die niet zijn gecontroleerd. Beoordeling is gereed om verder te gaan met het **Toep assen van de toepassing** als automatisch Toep assen is ingeschakeld. |
|Aanvragen | Er is geen wijziging in de toegang voor gebruikers die zijn goedgekeurd. |
|Toegepast | Geweigerde gebruikers, indien van toepassing, zijn verwijderd uit de resource of map. |
|Mislukt | Controle kan niet worden uitgevoerd. Deze fout kan betrekking hebben op het verwijderen van de Tenant, een wijziging in licenties of andere interne Tenant wijzigingen. |

## <a name="create-reviews-via-apis"></a>Beoordelingen maken via Api's

U kunt ook toegangs beoordelingen maken met behulp van Api's. Wat u doet om toegangs beoordelingen van groepen en toepassings gebruikers in het Azure Portal te beheren, kunt u ook uitvoeren met behulp van Microsoft Graph-Api's. Zie voor meer informatie de [Azure AD Access beoordelingen API-referentie](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Voor een code voorbeeld, Zie [voor beeld van het ophalen van Azure AD-toegangs beoordelingen via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot groepen of toepassingen beoordelen](perform-access-review.md)
- [De toegang tot groepen of toepassingen controleren](review-your-access.md)
- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
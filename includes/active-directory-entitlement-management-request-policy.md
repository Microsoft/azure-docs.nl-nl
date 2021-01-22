---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 61858a478ead93b3709bb3e05c26335c9acbc24d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698124"
---
## <a name="for-users-in-your-directory"></a>Voor gebruikers in uw Directory

Volg deze stappen als u wilt toestaan dat gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Wanneer u het aanvraag beleid definieert, kunt u afzonderlijke gebruikers of meer vaak groepen gebruikers opgeven. Uw organisatie kan bijvoorbeeld al een groep hebben, zoals **alle werk nemers**.  Als deze groep wordt toegevoegd aan het beleid voor gebruikers die toegang kunnen aanvragen, kan een lid van die groep vervolgens toegang vragen.

1. Klik in de sectie **gebruikers die toegang kunnen aanvragen** , op **voor gebruikers in uw directory**.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven om te verfijnen wie in uw directory dit toegangs pakket kan aanvragen.

    ![Toegang tot pakket aanvragen: voor gebruikers in uw Directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke gebruikers en groepen** | Kies deze optie als u wilt dat alleen de gebruikers en groepen in uw directory die u opgeeft, dit toegangs pakket kunnen aanvragen. |
    | **Alle leden (exclusief gasten)** | Kies deze optie als u wilt dat alle gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Deze optie bevat geen gast gebruikers die u mogelijk hebt uitgenodigd voor uw Directory. |
    | **Alle gebruikers (inclusief gasten)** | Selecteer deze optie als u wilt dat alle leden van gebruikers en gast gebruikers in uw directory dit toegangs pakket kunnen aanvragen. |

    Gast gebruikers verwijzen naar externe gebruikers die zijn uitgenodigd voor uw directory met [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md). Zie [Wat zijn de standaard gebruikers machtigingen in azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md)voor meer informatie over de verschillen tussen gebruikers van het lid en gast gebruikers.

1. Als u **specifieke gebruikers en groepen** hebt geselecteerd, klikt u op **gebruikers en groepen toevoegen**.

1. Selecteer in het deel venster gebruikers en groepen selecteren de gebruikers en groepen die u wilt toevoegen.

    ![Toegangs pakket-aanvragen-gebruikers en groepen selecteren](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klik op **selecteren** om de gebruikers en groepen toe te voegen.

1. De sectie [goed keuring](#approval) overs Laan.

## <a name="for-users-not-in-your-directory"></a>Voor gebruikers die niet in uw Directory voor komt

 **Gebruikers die zich niet in uw directory** bevinden, verwijzen naar gebruikers in een andere Azure AD-Directory of een ander domein. Deze gebruikers zijn mogelijk nog niet in uw directory uitgenodigd. Azure AD-mappen moeten zodanig worden geconfigureerd dat uitnodigingen in **samenwerkings beperkingen** worden toegestaan. Zie [externe B2B-samen werking inschakelen en beheren wie gasten kan uitnodigen](../articles/active-directory/external-identities/delegate-invitations.md)voor meer informatie.

> [!NOTE]
> Er wordt een gast gebruikers account voor een gebruiker gemaakt dat nog niet in uw directory is waarvan de aanvraag is goedgekeurd of automatisch goedgekeurd. De gast wordt uitgenodigd, maar ontvangt geen uitnodigings-e-mail. In plaats daarvan ontvangen ze een e-mail bericht wanneer hun toegangs pakket toewijzing wordt bezorgd. Later, wanneer deze gast gebruiker geen toegangs pakket toewijzingen meer heeft, omdat de laatste toewijzing is verlopen of is geannuleerd, wordt het gast gebruikers account geblokkeerd om zich aan te melden en vervolgens te verwijderen. Als u wilt dat gast gebruikers voor onbepaalde tijd in uw Directory blijven, zelfs als ze geen toegangs pakket toewijzingen hebben, kunt u de instellingen voor uw rechten beheer configuratie wijzigen. Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](../articles/active-directory/external-identities/user-properties.md)voor meer informatie over het gast gebruikers object.

Volg deze stappen als u wilt toestaan dat gebruikers die geen toegang hebben tot uw directory dit toegangs pakket aanvragen:

1. Klik in de sectie **gebruikers die toegang kunnen aanvragen** , op **voor gebruikers die zich niet in uw directory** bevindt.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven.

    ![Toegang tot pakket aanvragen: voor gebruikers die niet in uw Directory voor komt](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke verbonden organisaties** | Kies deze optie als u een selectie wilt selecteren in een lijst met organisaties die de beheerder eerder heeft toegevoegd. Alle gebruikers van de geselecteerde organisaties kunnen dit toegangs pakket aanvragen. |
    | **Alle verbonden organisaties** | Kies deze optie als alle gebruikers van alle verbonden organisaties dit toegangs pakket kunnen aanvragen. |
    | **Alle gebruikers (alle verbonden organisaties en alle nieuwe externe gebruikers)** | Kies deze optie als alle gebruikers van alle verbonden organisaties dit toegangs pakket kunnen aanvragen en de instellingen van de lijst voor het toestaan of weigeren van de B2B-gebruikers prioriteit moeten hebben voor een nieuwe externe gebruiker. |

    Een verbonden organisatie is een extern Azure AD-adres lijst of-domein waarmee u een relatie hebt.

1. Als u **specifieke verbonden organisaties** hebt geselecteerd, klikt u op **mappen toevoegen** om te selecteren in een lijst met verbonden organisaties die de beheerder eerder heeft toegevoegd.

1. Typ de naam of de domein naam om te zoeken naar een eerder verbonden organisatie.

    ![Toegangs pakket-aanvragen-mappen selecteren](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Als de organisatie waarmee u wilt samen werken, zich niet in de lijst bevindt, kunt u uw beheerder vragen om deze toe te voegen als een verbonden organisatie. Zie [add a Connected Organization](../articles/active-directory/governance/entitlement-management-organization.md)(Engelstalig) voor meer informatie.

1. Wanneer u alle verbonden organisaties hebt geselecteerd, klikt u op **selecteren**.

    > [!NOTE]
    > Alle gebruikers van de geselecteerde verbonden organisaties kunnen dit toegangs pakket aanvragen. Dit omvat gebruikers in azure AD van alle subdomeinen die zijn gekoppeld aan de organisatie, tenzij deze domeinen worden geblokkeerd door de Azure B2B-lijst voor toestaan of weigeren. Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](../articles/active-directory/external-identities/allow-deny-list.md)voor meer informatie.

1. De sectie [goed keuring](#approval) overs Laan.

## <a name="none-administrator-direct-assignments-only"></a>Geen (alleen directe toewijzingen voor beheerders)

Volg deze stappen als u toegangs aanvragen wilt overs Laan en wilt toestaan dat beheerders rechtstreeks specifieke gebruikers aan dit toegangs pakket toewijzen. Gebruikers hoeven het toegangs pakket niet aan te vragen. U kunt nog steeds levenscyclus instellingen instellen, maar er zijn geen aanvraag instellingen.

1. Klik in de sectie **gebruikers die toegang kunnen vragen** , op **geen (alleen beheerders direct toewijzingen**).

    ![Toegang tot pakket-requests-geen alleen directe toewijzingen voor beheerders](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Nadat u het toegangs pakket hebt gemaakt, kunt u rechtstreeks specifieke interne en externe gebruikers aan het toegangs pakket toewijzen. Als u een externe gebruiker opgeeft, wordt er een gast gebruikers account in uw directory gemaakt. Zie [toewijzingen voor een toegangs pakket weer geven, toevoegen en verwijderen](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)voor meer informatie over het rechtstreeks toewijzen van een gebruiker.

1. Ga verder naar de sectie [aanvragen inschakelen](#enable-requests) .

## <a name="approval"></a>Goedkeuring

In de sectie goed keuring geeft u op of een goed keuring is vereist wanneer gebruikers dit toegangs pakket aanvragen. De goedkeurings instellingen werken op de volgende manier:

- Slechts één van de geselecteerde goed keurders of terugval fiatteurs moeten een aanvraag voor goed keuring in één fase goed keuren. 
- Slechts één van de geselecteerde goed keurders van elke fase moet een aanvraag voor het goed keuren van twee fasen goed keuren.
- De goed keurder kan een manager, interne sponsor of externe sponsor zijn, afhankelijk van wie het beleid van toepassing is.
- Goed keuring van elke geselecteerde goed keurder is niet vereist voor het goed keuren van één of twee fasen.
- De goedkeurings beslissing is gebaseerd op welke fiatteur de aanvraag eerst bekijkt.

Bekijk de volgende video voor een demonstratie van het toevoegen van goed keurders aan een aanvraag beleid:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Bekijk de volgende video voor een demonstratie van het toevoegen van een multi-fase goed keuring aan een aanvraag beleid:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Volg deze stappen om de goedkeurings instellingen op te geven voor aanvragen voor het toegangs pakket:

1. Als u goed keuring wilt vereisen voor aanvragen van de geselecteerde gebruikers, stelt u de schakel optie **goed keuring vereisen** in op **Ja**. Als u wilt dat aanvragen automatisch worden goedgekeurd, stelt u de wissel knop in op **Nee**.

1. Als u wilt dat gebruikers een reden opgeven om het toegangs pakket aan te vragen, stelt u de optie uitschakeling van **aanvrager vereisen** in op **Ja**.
    
1. Bepaal nu of aanvragen met één of twee fasen moeten worden goedgekeurd. Stel in hoeveel **fasen** moeten worden overgeschakeld op **1** voor de goed keuring van één fase of stel de wissel knop in op **2** voor goed keuring in twee fasen.

    ![Toegangs aanvragen-goedkeurings instellingen voor het pakket](./media/active-directory-entitlement-management-request-policy/approval.png)

Gebruik de volgende stappen om goed keurders toe te voegen nadat u hebt gekozen hoeveel fases u nodig hebt: 

### <a name="single-stage-approval"></a>Goed keuring in één fase

1. De **eerste goed keurder** toevoegen:
    
    Als het beleid is ingesteld op het bepalen van de toegang voor gebruikers in uw directory, kunt u **Manager selecteren als fiatteur**. U kunt ook een specifieke gebruiker toevoegen door te klikken op **goed keurders toevoegen** na selectie specifieke goed keurders selecteren in het vervolg keuzemenu.
    
    ![Toegang tot pakket aanvragen-voor gebruikers in Directory-First goed keurder](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Als dit beleid is ingesteld op het bepalen van de toegang voor gebruikers die zich niet in uw directory bevindt, kunt u **externe sponsor** of **interne sponsor** selecteren. U kunt ook een specifieke gebruiker toevoegen door te klikken op **fiatteurs** of groepen toevoegen onder specifieke goed keurders kiezen.
    
    ![Toegang tot pakket aanvragen-voor gebruikers buiten de Directory-eerste goed keurder](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Als u **Manager** als eerste fiatteur hebt geselecteerd, klikt u op **terugval toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren als terugval fiatteur. Terugval fiatteurs ontvangen de aanvraag als het beheer van rechten de Manager niet kan vinden voor de gebruiker die toegang vraagt.

    De Manager is gevonden door het beheer van rechten met behulp van het kenmerk **Manager** . Het kenmerk bevindt zich in het profiel van de gebruiker in azure AD. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.

1. Als u **specifieke goed keurders kiezen** hebt geselecteerd, klikt u op goed keurders **toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren voor goed keurders.

1. In het vak onder **besluit moet worden aangegeven hoeveel dagen?**, geeft u het aantal dagen op dat een fiatteur een aanvraag voor dit toegangs pakket moet beoordelen.

    Als een aanvraag niet binnen deze tijds periode is goedgekeurd, wordt deze automatisch geweigerd. De gebruiker moet een andere aanvraag indienen voor het toegangs pakket.

1. Als u goed keurders wilt verplichten om een motivering te geven voor de beslissing, stelt u de uitvulling van de fiatteur in op **Ja**.

    De motivering is zichtbaar voor andere goed keurders en de aanvrager.

### <a name="2-stage-approval"></a>2-fase goedkeuring

Als u een 2-fase goedkeuring hebt geselecteerd, moet u een tweede goed keurder toevoegen.

1. Voeg de **tweede goed keurder** toe: 
    
    Als de gebruikers zich in uw directory bevinden, voegt u een specifieke gebruiker als de tweede goed keurder toe door te klikken op **fiatteurs toevoegen** onder specifieke goed keurders kiezen.

    ![Toegang tot pakket aanvragen-gebruikers in de Directory-tweede goed keurder](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Als de gebruikers zich niet in uw directory bevinden, selecteert u **interne sponsor** of **externe sponsor** als de tweede goed keurder. Nadat u de goed keurder hebt geselecteerd, voegt u de terugval fiatteurs toe.

    ![Toegang tot pakket aanvragen-voor gebruikers buiten de Directory-tweede goed keurder](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Geef het aantal dagen op dat de tweede goed keurder de aanvraag moet goed keuren in het vak onder **besluit moeten worden gedaan in het aantal dagen?**. 

1. Stel de uitvulling van de goed keurder vereisen in op **Ja** of **Nee**.

### <a name="alternate-approvers"></a>Alternatieve goed keurders

U kunt alternatieve goed keurders opgeven, vergelijkbaar met het opgeven van de eerste en tweede goed keurders die aanvragen kunnen goed keuren. Met alternatieve goed keurders kunt u ervoor zorgen dat de aanvragen worden goedgekeurd of geweigerd voordat ze verlopen (time-out). U kunt de eerste goed keurder en tweede goed keurder een lijst met alternatieve goed keurders geven voor het goed keuren van twee fasen. 

Door alternatieve goed keurders op te geven, in het geval dat de eerste of tweede goed keurders de aanvraag niet goed keuren of afwijzen, wordt de in behandeling zijnde aanvraag doorgestuurd naar de alternatieve goed keurders, volgens het doorstuur schema dat u tijdens het instellen van het beleid hebt opgegeven. Ze ontvangen een e-mail om de in behandeling zijnde aanvraag goed te keuren of te weigeren.

Nadat de aanvraag is doorgestuurd naar de alternatieve goed keurders, kunnen de eerste of tweede goed keurders de aanvraag toch goed keuren of weigeren. Alternatieve goed keurders gebruiken dezelfde mijn Access-site om de in behandeling zijnde aanvraag goed te keuren of te weigeren.

We kunnen personen of groepen mensen een lijst geven als fiatteurs en alternatieve goed keurders. Zorg ervoor dat u de lijst met verschillende groepen van personen de eerste, tweede en alternatieve goed keurders vermeldt.
Als u bijvoorbeeld Anne en Bob als eerste fiatteur (s) hebt genoteerd, vermeldt u Carola en Dave als alternatieve goed keurders. Gebruik de volgende stappen om alternatieve goed keurders toe te voegen aan een toegangs pakket:

1. Klik onder de eerste goed keurder, tweede goed keurder of beide op **Geavanceerde aanvraag instellingen weer geven**.

    ![Toegangs pakket-beleid: Geavanceerde aanvraag instellingen weer geven](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Instellen **als er geen actie wordt ondernomen, door sturen naar alternatieve goed keurders? in-** of **uitschakelen.**

1. Klik op **alternatieve goed keurders toevoegen** en selecteer de alternatieve fiatteur (s) in de lijst.

    ![Toegangs pakket-beleid-alternatieve goed keurders toevoegen](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Als u Manager als fiatteur selecteert voor de eerste goed keurder, hebt u een extra optie, **tweede niveau Manager als alternatieve goed keurder**, beschikbaar om te kiezen in het veld alternatieve goed keurder. Als u deze optie selecteert, moet u een terugval-fiatteur toevoegen om de aanvraag door te sturen naar wanneer het systeem de tweede niveau Manager niet kan vinden.

1. Stel in het vak **door sturen naar alternatieve goed keurder (en) na hoeveel dagen** het aantal dagen in dat de goed keurders een aanvraag moeten goed keuren of weigeren. Als geen goed keurders de aanvraag hebben goedgekeurd of geweigerd vóór de duur van de aanvraag, is de aanvraag verloopt (time-out) en moet de gebruiker een andere aanvraag indienen voor het toegangs pakket. 

    Aanvragen kunnen alleen worden doorgestuurd naar alternatieve goed keurders op dag nadat de duur van de aanvraag halfjaarlijks is bereikt en de beslissing van de hoofd fiatteur (s) een time-out heeft na ten minste vier dagen. Als de time-out van de aanvraag kleiner is dan of gelijk is aan 3, is er onvoldoende tijd om de aanvraag door te sturen naar een alternatieve fiatteur (s). In dit voor beeld is de duur van de aanvraag 14 dagen. Daarom bereikt de duur van de aanvraag halfjaarlijks op dag 7. De aanvraag kan dus niet eerder dan dag 8 worden doorgestuurd. Bovendien kunnen aanvragen niet worden doorgestuurd op de laatste dag van de aanvraag duur. In het voor beeld kan de meest recente aanvraag worden doorgestuurd is dag 13.

## <a name="enable-requests"></a>Aanvragen inschakelen

1. Als u wilt dat het toegangs pakket onmiddellijk beschikbaar wordt gesteld voor gebruikers in het aanvraag beleid om aan te vragen, verplaatst u de schakel optie inschakelen naar **Ja**.

    U kunt deze altijd in de toekomst inschakelen nadat u klaar bent met het maken van het toegangs pakket.

    Als u geen hebt geselecteerd **(alleen voor beheerders directe toewijzingen)** en u instellen op **Nee** instelt, kunnen beheerders dit toegangs pakket niet rechtstreeks toewijzen.

    ![Scherm afbeelding met de optie voor het inschakelen van nieuwe aanvragen en toewijzingen.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klik op **Volgende**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Informatie van de aanvrager (preview-versie) toevoegen aan een toegangs pakket

1. Ga naar het tabblad **aanvraag gegevens** en klik op het subtabblad **vragen** .
 
1. Typ in wat u wilt vragen de aanvrager, ook wel de weergave teken reeks genoemd, voor de vraag in het vak **vraag** .

    ![Toegangs pakket-beleid-instelling van gegevens van aanvrager inschakelen](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Als u uw eigen lokalisatie opties wilt toevoegen, klikt u op **lokalisatie toevoegen**.
    1. Selecteer in het deel venster **lokalisaties voor vraag toevoegen** de **taal code** voor de taal waarin u de vraag lokalisatie.
    1. In de taal die u hebt geconfigureerd, typt u de vraag in het **gelokaliseerde** tekstvak.
    1. Zodra u alle benodigde lokalisaties hebt toegevoegd, klikt u op **Opslaan**.

    ![Toegangs pakket-beleid: gelokaliseerde tekst configureren](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Selecteer de **antwoord indeling** waarin aanvragers moeten reageren. Antwoord indelingen zijn: *korte tekst*, *meerdere keuzen* en *lange tekst*.
 
    ![Toegangs pakket-beleid: Selecteer weer geven en bewerken van de antwoord indeling voor meerdere keuzes](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Als u meerdere keuzen selecteert, klikt u op de knop **weer geven en bewerken** om de antwoord opties te configureren.
    1. Nadat u weer geven en bewerken hebt geselecteerd, wordt het deel venster **weer gave/bewerken vragen** geopend.
    1. Typ de antwoord opties die u wilt toewijzen aan de aanvrager bij het beantwoorden van de vraag in de vakken **antwoord waarden** .
    1. Typ zoveel reacties als u nodig hebt en klik vervolgens op **Opslaan**.
    
    ![Toegangs pakket-beleid: meerdere keuze opties opgeven](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Als u wilt dat aanvragers deze vraag beantwoorden wanneer ze toegang tot een toegangs pakket aanvragen, klikt u op het selectie vakje onder **vereist**.

1. Klik op Next
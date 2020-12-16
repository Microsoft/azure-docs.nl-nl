---
title: Goedkeurings instellingen voor een toegangs pakket wijzigen in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het wijzigen van de instellingen voor goed keuring en gegevens van de aanvrager voor een toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591294"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Instellingen voor de goed keuring van wijzigingen en gegevens van de aanvrager (preview-versie) voor een toegangs pakket in het beheer van rechten van Azure AD

Als toegangs pakket beheer kunt u de instellingen voor de goed keuring en de aanvrager van een toegangs pakket op elk gewenst moment wijzigen door een bestaand beleid te bewerken of een nieuw beleid toe te voegen.

In dit artikel wordt beschreven hoe u de instellingen voor goed keuring en gegevens van de aanvrager wijzigt voor een bestaand toegangs pakket.

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


## <a name="change-approval-settings-of-an-existing-access-package"></a>Goedkeurings instellingen van een bestaand toegangs pakket wijzigen

Volg deze stappen om de goedkeurings instellingen op te geven voor aanvragen voor het toegangs pakket:

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Selecteer een beleid om een nieuw beleid te bewerken of toe te voegen aan het toegangs pakket
    1. Klik op **beleid** en vervolgens op **beleid toevoegen** als u een nieuw beleid wilt maken.
    1. Klik op het beleid dat u wilt bewerken en klik vervolgens op **bewerken**.

1. Ga naar het tabblad **aanvraag** .

1. Als u goed keuring wilt vereisen voor aanvragen van de geselecteerde gebruikers, stelt u de schakel optie **goed keuring vereisen** in op **Ja**. Als u wilt dat aanvragen automatisch worden goedgekeurd, stelt u de wissel knop in op **Nee**.

1. Als u wilt dat gebruikers een reden opgeven om het toegangs pakket aan te vragen, stelt u de optie uitschakeling van **aanvrager vereisen** in op **Ja**.
    
1. Bepaal nu of aanvragen met één of twee fasen moeten worden goedgekeurd. Stel in hoeveel **fasen** moeten worden overgeschakeld op **1** voor de goed keuring van één fase of stel de wissel knop in op **2** voor goed keuring in twee fasen.

    ![Toegangs aanvragen-goedkeurings instellingen voor het pakket](./media/entitlement-management-access-package-approval-policy/approval.png)


Gebruik de volgende stappen om goed keurders toe te voegen nadat u hebt gekozen hoeveel fases u nodig hebt: 

### <a name="single-stage-approval"></a>Goed keuring in één fase

1. De **eerste goed keurder** toevoegen:
    
    Als het beleid is ingesteld op het bepalen van de toegang voor gebruikers in uw directory, kunt u **Manager selecteren als fiatteur**. U kunt ook een specifieke gebruiker toevoegen door te klikken op **goed keurders toevoegen** na selectie specifieke goed keurders selecteren in het vervolg keuzemenu.
    
    ![Toegang tot pakket aanvragen-voor gebruikers in Directory-First goed keurder](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Als dit beleid is ingesteld op het bepalen van de toegang voor gebruikers die zich niet in uw directory bevindt, kunt u **externe sponsor** of **interne sponsor** selecteren. U kunt ook een specifieke gebruiker toevoegen door te klikken op **fiatteurs** of groepen toevoegen onder specifieke goed keurders kiezen.
    
    ![Toegang tot pakket aanvragen-voor gebruikers buiten de Directory-eerste goed keurder](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Als u **Manager** als eerste fiatteur hebt geselecteerd, klikt u op **terugval toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren als terugval fiatteur. Terugval fiatteurs ontvangen de aanvraag als het beheer van rechten de Manager niet kan vinden voor de gebruiker die toegang vraagt.

    De Manager is gevonden door het beheer van rechten met behulp van het kenmerk **Manager** . Het kenmerk bevindt zich in het profiel van de gebruiker in azure AD. Zie [de profiel gegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.

1. Als u **specifieke goed keurders kiezen** hebt geselecteerd, klikt u op goed keurders **toevoegen** om een of meer gebruikers of groepen in uw directory te selecteren voor goed keurders.

1. In het vak onder **besluit moet worden aangegeven hoeveel dagen?**, geeft u het aantal dagen op dat een fiatteur een aanvraag voor dit toegangs pakket moet beoordelen.

    Als een aanvraag niet binnen deze tijds periode is goedgekeurd, wordt deze automatisch geweigerd. De gebruiker moet een andere aanvraag indienen voor het toegangs pakket.

1. Als u goed keurders wilt verplichten om een motivering te geven voor de beslissing, stelt u de uitvulling van de fiatteur in op **Ja**.

    De motivering is zichtbaar voor andere goed keurders en de aanvrager.

### <a name="2-stage-approval"></a>2-fase goedkeuring

Als u een 2-fase goedkeuring hebt geselecteerd, moet u een tweede goed keurder toevoegen.

1. Voeg de **tweede goed keurder** toe: 
    
    Als de gebruikers zich in uw directory bevinden, voegt u een specifieke gebruiker als de tweede goed keurder toe door te klikken op **fiatteurs toevoegen** onder specifieke goed keurders kiezen.

    ![Toegang tot pakket aanvragen-gebruikers in de Directory-tweede goed keurder](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Als de gebruikers zich niet in uw directory bevinden, selecteert u **interne sponsor** of **externe sponsor** als de tweede goed keurder. Nadat u de goed keurder hebt geselecteerd, voegt u de terugval fiatteurs toe.

    ![Toegang tot pakket aanvragen-voor gebruikers buiten de Directory-tweede goed keurder](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Geef het aantal dagen op dat de tweede goed keurder de aanvraag moet goed keuren in het vak onder **besluit moeten worden gedaan in het aantal dagen?**. 

1. Stel de uitvulling van de goed keurder vereisen in op **Ja** of **Nee**.

### <a name="alternate-approvers"></a>Alternatieve goed keurders

U kunt alternatieve goed keurders opgeven, vergelijkbaar met het opgeven van de eerste en tweede goed keurders die aanvragen kunnen goed keuren. Met alternatieve goed keurders kunt u ervoor zorgen dat de aanvragen worden goedgekeurd of geweigerd voordat ze verlopen (time-out). U kunt de eerste goed keurder en tweede goed keurder een lijst met alternatieve goed keurders geven voor het goed keuren van twee fasen. 

Door alternatieve goed keurders op te geven, in het geval dat de eerste of tweede goed keurders de aanvraag niet goed keuren of afwijzen, wordt de in behandeling zijnde aanvraag doorgestuurd naar de alternatieve goed keurders, volgens het doorstuur schema dat u tijdens het instellen van het beleid hebt opgegeven. Ze ontvangen een e-mail om de in behandeling zijnde aanvraag goed te keuren of te weigeren.

Nadat de aanvraag is doorgestuurd naar de alternatieve goed keurders, kunnen de eerste of tweede goed keurders de aanvraag toch goed keuren of weigeren. Alternatieve goed keurders gebruiken dezelfde mijn Access-site om de in behandeling zijnde aanvraag goed te keuren of te weigeren.

We kunnen personen of groepen mensen een lijst geven als fiatteurs en alternatieve goed keurders. Zorg ervoor dat u de lijst met verschillende groepen van personen de eerste, tweede en alternatieve goed keurders vermeldt.
Als u bijvoorbeeld Anne en Bob als eerste fiatteur (s) hebt genoteerd, vermeldt u Carola en Dave als alternatieve goed keurders. Gebruik de volgende stappen om alternatieve goed keurders toe te voegen aan een toegangs pakket:

1. Klik onder de eerste goed keurder, tweede goed keurder of beide op **Geavanceerde aanvraag instellingen weer geven**.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Toegangs pakket-beleid: Geavanceerde aanvraag instellingen weer geven":::

1. Instellen **als er geen actie wordt ondernomen, door sturen naar alternatieve goed keurders? in-** of **uitschakelen.**

1. Klik op **alternatieve goed keurders toevoegen** en selecteer de alternatieve fiatteur (s) in de lijst.

    ![Toegangs pakket-beleid-alternatieve goed keurders toevoegen](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Als u Manager als fiatteur selecteert voor de eerste goed keurder, hebt u een extra optie, **tweede niveau Manager als alternatieve goed keurder**, beschikbaar om te kiezen in het veld alternatieve goed keurder. Als u deze optie selecteert, moet u een terugval-fiatteur toevoegen om de aanvraag door te sturen naar wanneer het systeem de tweede niveau Manager niet kan vinden.

1. Stel in het vak **door sturen naar alternatieve goed keurder (en) na hoeveel dagen** het aantal dagen in dat de goed keurders een aanvraag moeten goed keuren of weigeren. Als geen goed keurders de aanvraag hebben goedgekeurd of geweigerd vóór de duur van de aanvraag, is de aanvraag verloopt (time-out) en moet de gebruiker een andere aanvraag indienen voor het toegangs pakket. 

    Aanvragen kunnen alleen worden doorgestuurd naar alternatieve goed keurders op dag nadat de duur van de aanvraag halfjaarlijks is bereikt en de beslissing van de hoofd fiatteur (s) een time-out heeft na ten minste vier dagen. Als de time-out van de aanvraag kleiner is dan of gelijk is aan 3, is er onvoldoende tijd om de aanvraag door te sturen naar een alternatieve fiatteur (s). In dit voor beeld is de duur van de aanvraag 14 dagen. Daarom bereikt de duur van de aanvraag halfjaarlijks op dag 7. De aanvraag kan dus niet eerder dan dag 8 worden doorgestuurd. Bovendien kunnen aanvragen niet worden doorgestuurd op de laatste dag van de aanvraag duur. In het voor beeld kan de meest recente aanvraag worden doorgestuurd is dag 13.

## <a name="enable-requests"></a>Aanvragen inschakelen

1. Als u wilt dat het toegangs pakket onmiddellijk beschikbaar wordt gesteld voor gebruikers in het aanvraag beleid om aan te vragen, verplaatst u de schakel optie inschakelen naar **Ja**.

    U kunt deze altijd in de toekomst inschakelen nadat u klaar bent met het maken van het toegangs pakket.

    Als u geen hebt geselecteerd **(alleen voor beheerders directe toewijzingen)** en u instellen op **Nee** instelt, kunnen beheerders dit toegangs pakket niet rechtstreeks toewijzen.

    ![Toegangs pakket-beleids instelling beleid inschakelen](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klik op **Volgende**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Aanvullende gegevens van de aanvrager voor goed keuring verzamelen (preview-versie)

Om ervoor te zorgen dat gebruikers toegang krijgen tot de juiste toegangs pakketten, kunt u vereisen dat aanvragers een aangepast tekst veld of meerdere keuze vragen op het moment van de aanvraag beantwoorden. Er geldt een limiet van 20 vragen per beleid en een limiet van 25 antwoorden op meerdere keuze vragen. De vragen worden vervolgens weer gegeven aan goed keurders om hen te helpen bij het nemen van een beslissing.

1. Ga naar het tabblad **aanvraag gegevens** en klik op het subtabblad **vragen** .
 
1. Typ in wat u wilt vragen de aanvrager, ook wel de weergave teken reeks genoemd, voor de vraag in het vak **vraag** .

    ![Toegangs pakket-beleid-instelling van gegevens van aanvrager inschakelen](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Als de community van gebruikers die toegang moeten hebben tot het toegangs pakket, niet allemaal een gemeen schappelijke Voorkeurs taal hebben, kunt u de gebruikers ervaring voor het aanvragen van toegang op myaccess.microsoft.com verbeteren. Als u de ervaring wilt verbeteren, kunt u alternatieve weergave teken reeksen voor verschillende talen opgeven. Als de webbrowser van een gebruiker bijvoorbeeld is ingesteld op Spaans en er Spaanse weergave teken reeksen zijn geconfigureerd, worden deze teken reeksen weer gegeven aan de gebruiker die de aanvraag heeft ingediend. Als u lokalisatie voor aanvragen wilt configureren, klikt u op **lokalisatie toevoegen**.
    1. Selecteer in het deel venster **lokalisaties voor vraag toevoegen** de **taal code** voor de taal waarin u de vraag lokalisatie.
    1. In de taal die u hebt geconfigureerd, typt u de vraag in het **gelokaliseerde** tekstvak.
    1. Zodra u alle benodigde lokalisaties hebt toegevoegd, klikt u op **Opslaan**.

    ![Toegangs pakket-beleid: gelokaliseerde tekst configureren](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Selecteer de **antwoord indeling** waarin aanvragers moeten reageren. Antwoord indelingen zijn: *korte tekst*, *meerdere keuzen* en *lange tekst*.
 
    ![Toegangs pakket-beleid: Selecteer weer geven en bewerken van de antwoord indeling voor meerdere keuzes](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Als u meerdere keuzen selecteert, klikt u op de knop **weer geven en bewerken** om de antwoord opties te configureren.
    1. Nadat u weer geven en bewerken hebt geselecteerd, wordt het deel venster **weer gave/bewerken vragen** geopend.
    1. Typ de antwoord opties die u wilt toewijzen aan de aanvrager bij het beantwoorden van de vraag in de vakken **antwoord waarden** .
    1. Typ zoveel reacties als u nodig hebt en klik vervolgens op **Opslaan**.
    
    ![Toegangs pakket-beleid: meerdere keuze opties opgeven](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Als u wilt dat aanvragers deze vraag beantwoorden wanneer ze toegang tot een toegangs pakket aanvragen, klikt u op het selectie vakje onder **vereist**.

1. Vul de resterende tabbladen (bijvoorbeeld levenscyclus) in op basis van uw behoeften.

Nadat u de gegevens van de aanvrager in het beleid voor toegangs pakketten hebt geconfigureerd, kunt u antwoorden op de vragen van de aanvrager bekijken. Zie [antwoorden op vragen van de aanvrager weer geven (preview)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview)voor meer informatie over het bekijken van de aanvrager.

## <a name="next-steps"></a>Volgende stappen
- [Levenscyclus instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-lifecycle-policy.md)
- [Aanvragen voor een toegangs pakket weer geven](entitlement-management-access-package-requests.md)
